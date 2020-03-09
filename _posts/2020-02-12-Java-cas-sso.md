---
title: CAS-SSO单点登录实例
date:  2020-02-12 12:49:33 +0800
category: Original
tags: Java
excerpt: 基于CAS协议的SSO单点登录
---

### 实现代码

```
package biyeseason.utils;

import org.apache.http.Header;
import org.apache.http.HttpResponse;
import org.apache.http.NameValuePair;
import org.apache.http.client.CookieStore;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.conn.socket.LayeredConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
import org.apache.http.cookie.Cookie;
import org.apache.http.impl.client.BasicCookieStore;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.security.cert.X509Certificate;
import java.util.ArrayList;
import java.util.List;


/***
 * 模拟cas登录
 * 使用账号密码模拟登录广财cas登录系统。
 * 验证用户密码的正确性
 */
public class Login_cas {
    public static boolean genLogin(String username, String password) throws Exception {
        //创建客户端
        CookieStore httpCookie = new BasicCookieStore();
        CloseableHttpClient client = createHttpClientWithNoSsl(httpCookie);

        /* 第一次请求[GET] 拉取流水号信息 */
        HttpGet request = new HttpGet("http://lib.gdufe.edu.cn/sso");
        HttpResponse response = client.execute(request);
        Document htmlPage = Jsoup.parse(readResponse(response));
        Element form = htmlPage.select("#fm1").first();

        String execution = form.select("[name=execution]").first().val();
        String _eventId = form.select("[name=_eventId]").first().val();
        String submit = form.select("[name=submit]").first().val();
        String lt = form.select("[name=lt]").first().val();


        /* 第二次请求[POST] 发送表单验证信息 */
        HttpPost request2 = new HttpPost("http://lib.gdufe.edu.cn/sso/login");
        List<NameValuePair> params = new ArrayList<NameValuePair>();
        params.add(new BasicNameValuePair("username", username));
        params.add(new BasicNameValuePair("password", password));
        params.add(new BasicNameValuePair("execution", execution));
        params.add(new BasicNameValuePair("_eventId", _eventId));
        params.add(new BasicNameValuePair("lt", lt));
        //params.add(new BasicNameValuePair("submit", submit));
        request2.setEntity(new UrlEncodedFormEntity(params));
        HttpResponse response2 = client.execute(request2);

        Header headerSetCookie = response2.getFirstHeader("Set-Cookie");
        String TGC = headerSetCookie == null ? null : headerSetCookie.getValue().substring(4, headerSetCookie.getValue().indexOf(";")); // TGC
        Header headerLocation = response2.getFirstHeader("Location");
        String location = headerLocation == null ? null : headerLocation.getValue();

        List<Cookie> cookies = httpCookie.getCookies();
        for (Cookie cookie:cookies){
            if (cookie != null && cookie.getName().equals("CASTGC")) {
                return true;
            }
        }
        return false;
    }

    /* 读取 response body 内容为字符串 */
    private static String readResponse(HttpResponse response) throws IOException {
        BufferedReader in = new BufferedReader(
                new InputStreamReader(response.getEntity().getContent()));
        String result = new String();
        String line;
        while ((line = in.readLine()) != null) {
            result += line;
        }
        return result;
    }




    /**
     * 创建模拟客户端（针对 https 客户端禁用 SSL 验证）
     *
     * @param cookieStore 缓存的 Cookies 信息
     * @return
     * @throws Exception
     */
    private static CloseableHttpClient createHttpClientWithNoSsl(CookieStore cookieStore) throws Exception {
        // Create a trust manager that does not validate certificate chains
        TrustManager[] trustAllCerts = new TrustManager[]{
                new X509TrustManager() {
                    public X509Certificate[] getAcceptedIssuers() {
                        return null;
                    }

                    public void checkClientTrusted(X509Certificate[] certs, String authType) {
                        // don't check
                    }

                    public void checkServerTrusted(X509Certificate[] certs, String authType) {
                        // don't check
                    }
                }
        };

        SSLContext ctx = SSLContext.getInstance("TLS");
        ctx.init(null, trustAllCerts, null);
        LayeredConnectionSocketFactory sslSocketFactory = new SSLConnectionSocketFactory(ctx);
        return HttpClients.custom()
                .setSSLSocketFactory(sslSocketFactory)
                .setDefaultCookieStore(cookieStore == null ? new BasicCookieStore() : cookieStore)
                .build();
    }

    public static void main(String[] args) throws Exception {
        String name = "";
        String password = "";
        Login_cas lo = new Login_cas();
        System.out.println(lo.genLogin(name,password));
    }


}
```

### 实现原理

用户输入用户名密码登录时，资源服务器模拟一个客户端，向学校认证服务器发送认证请求，认证服务器给当前用户返回认证通行的CASTGC，资源服务器对返回的CASTGC的真伪进行检验，从而放行。

#### CAS协议

CAS 1.0 协议定义了一组术语，一组票据，一组接口。



术语：

- Client：用户。
- Server：中心服务器，也是 SSO 中负责单点登录的服务器。
- Service：需要使用单点登录的各个服务，相当于上文中的产品 a/b。

接口：

- /login：登录接口，用于登录到中心服务器。
- /logout：登出接口，用于从中心服务器登出。
- /validate：用于验证用户是否登录中心服务器。
- /serviceValidate：用于让各个 service 验证用户是否登录中心服务器。

票据

- TGT：Ticket Grangting Ticket

TGT 是 CAS 为用户签发的登录票据，拥有了 TGT，用户就可以证明自己在 CAS 成功登录过。TGT 封装了 Cookie 值以及此 Cookie 值对应的用户信息。当 HTTP 请求到来时，CAS 以此 Cookie 值（TGC）为 key 查询缓存中有无 TGT ，如果有的话，则相信用户已登录过。

- TGC：Ticket Granting Cookie

CAS Server 生成TGT放入自己的 Session 中，而 TGC 就是这个 Session 的唯一标识（SessionId），以 Cookie 形式放到浏览器端，是 CAS Server 用来明确用户身份的凭证。

- ST：Service Ticket

ST 是 CAS 为用户签发的访问某一 service 的票据。用户访问 service 时，service 发现用户没有 ST，则要求用户去 CAS 获取 ST。用户向 CAS 发出获取 ST 的请求，CAS 发现用户有 TGT，则签发一个 ST，返回给用户。用户拿着 ST 去访问 service，service 拿 ST 去 CAS 验证，验证通过后，允许用户访问资源。

| Cookie | key    | value  |
| ------ | ------ | ------ |
|        | CASTGC | ${TGC} |

| Session | key       | value  |
| ------- | --------- | ------ |
|         | ${TGC}    | ${TGT} |
|         | ${PGTIOU} | ${PGT} |

*票据关系*

*用户信息 **签发** TGT

TGT **签发** ST

PGT **签发** PT