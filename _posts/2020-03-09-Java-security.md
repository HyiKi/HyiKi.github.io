---
title: Spring Security框架(持续学习中)
date:  	2020-03-09 14:56:36 +0800
category: Original
tags: [Java,FrameWork]
excerpt: Spring FrameWork + Spring Security对用户完成认证、授权操作。
---

### 简述

#### Spring FrameWork + Spring Security

> 权限管理，一般指根据系统设置的安全规则或者安全策略，用户可以访问而且只能访问自己被授权的资源。权限管理几乎出现在任何系统里面，前提是需要有用户和密码认证的系统。

1. 认证（Authentication）通过用户名和密码成功登陆系统后，让系统得到当前角色身份。
2. 授权（Access-control）系统根据当前用户的角色，给其授予对应可以操作的权限资源。

#### 完成权限管理的三个对象

1. 用户：
   含有用户名、用户信息、密码，可实现认证操作
2. 角色：
   主要包含角色名称，角色描述和当前角色拥有的权限信息，可实现授权操作。
3. 权限：
   权限也可以称为菜单，主要包含当前权限名称，url地址等信息，可实现动态展示菜单。

#### 定义

Spring Security 是 Spring 基于AOP（Aspect Oriented Programming）和 Servlet 过滤实现的安全框架。

### 导入依赖包

> Spring Security
> 主要jar包功能介绍 spring-security-core.jar 核心包，任何Spring Security功能都需要此包。
>
> spring-security-web.jar 
> web工程必备，包含过滤器和相关的Web安全基础结构代码。
>
> spring-security-conﬁg.jar 
> 用于解析xml配置文件，用到Spring Security的xml配置文件的就要用到此包。 
>
> spring-security-taglibs.jar
> Spring Security提供的动态标签库，jsp页面可以用。

```
		<dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-config</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-taglibs</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
```

### 配置

1. web.xml

   ```
   <!--SpringSecurity核心过滤器链-->
   <!--springSecurityFilterChain名词不能修改-->
   <filter>
   	<filter-name>springSecurityFilterChain</filter-name>
   	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
   </filter>
   <filter-mapping>
   	<filter-name>springSecurityFilterChain</filter-name>
   	<url-pattern>/*</url-pattern>
   </filter-mapping>
   ```

2. spring-security.xml

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xmlns:tx="http://www.springframework.org/schema/tx"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xmlns:security="http://www.springframework.org/schema/security"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
   			    http://www.springframework.org/schema/beans/spring-beans.xsd
   			    http://www.springframework.org/schema/context
   			    http://www.springframework.org/schema/context/spring-context.xsd
   			    http://www.springframework.org/schema/aop
   			    http://www.springframework.org/schema/aop/spring-aop.xsd
   			    http://www.springframework.org/schema/tx
   			    http://www.springframework.org/schema/tx/spring-tx.xsd
   			    http://www.springframework.org/schema/mvc
   			    http://www.springframework.org/schema/mvc/spring-mvc.xsd
                   http://www.springframework.org/schema/security
   			    http://www.springframework.org/schema/security/spring-security.xsd">
   
       <!--释放静态资源-->
       <security:http pattern="/css/**" security="none"/>
       <security:http pattern="/img/**" security="none"/>
       <security:http pattern="/plugins/**" security="none"/>
       <security:http pattern="/failer.jsp" security="none"/>
       <!--配置springSecurity-->
       <!--
       auto-config="true"  表示自动加载springsecurity的配置文件
       use-expressions="true" 表示使用spring的el表达式来配置springsecurity
       -->
       <security:http auto-config="true" use-expressions="true">
           <!--让认证页面可以匿名访问-->
           <security:intercept-url pattern="/login.jsp" access="permitAll()"/>
           <!--拦截资源-->
           <!--
           pattern="/**" 表示拦截所有资源
           access="hasAnyRole('ROLE_USER')" 表示只有ROLE_USER角色才能访问资源
           -->
           <security:intercept-url pattern="/**" access="hasAnyRole('ROLE_USER')"/>
           <!--配置认证信息-->
           <security:form-login login-page="/login.jsp"
                                login-processing-url="/login"
                                default-target-url="/index.jsp"
                                authentication-failure-url="/failer.jsp"/>
           <!--配置退出登录信息-->
           <security:logout logout-url="/logout"
                            logout-success-url="/login.jsp"/>
           <!--去掉csrf拦截的过滤器-->
           <security:csrf disabled="true"/>
   
           <!--开启remember me过滤器，设置token存储时间为60秒-->
           <security:remember-me
                   data-source-ref="dataSource"
                   token-validity-seconds="60"
                   remember-me-parameter="remember-me"/>
   
           <!--处理403异常-->
           <!--<security:access-denied-handler error-page="/403.jsp"/>-->
       </security:http>
   
       <!--把加密对象放入的IOC容器中-->
       <bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
   
       <!--设置Spring Security认证用户信息的来源-->
       <!--
       springsecurity默认的认证必须是加密的，加上{noop}表示不加密认证。
       -->
       <security:authentication-manager>
           <security:authentication-provider user-service-ref="userServiceImpl">
               <security:password-encoder ref="passwordEncoder"/>
           </security:authentication-provider>
       </security:authentication-manager>
   
   </beans>
   ```

3. applicationContext.xml

   ```
   	<!--引入springsecurity的配置文件-->
       <import resource="classpath:spring-security.xml"/>
   ```

### Spring Security Filter Clain过滤器链

1. org.springframework.security.web.context.SecurityContextPersistenceFilter
   
   > SecurityContextPersistenceFilter主要是使用SecurityContextRepository在session中保存或更新一个 SecurityContext，并将SecurityContext给以后的过滤器使用，来为后续ﬁlter建立所需的上下文。 SecurityContext中存储了当前用户的认证以及权限信息。

  

2. org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter 
   
   > 此过滤器用于集成SecurityContext到Spring异步执行机制中的WebAsyncManager首当其冲的一个过滤器，作用之重要，自不必多言。 

  

3. org.springframework.security.web.header.HeaderWriterFilter
   > 向请求的Header中添加相应的信息,可在http标签内部使用security:headers来控制

   

4. org.springframework.security.web.csrf.CsrfFilter 
   
   > csrf又称跨域请求伪造，SpringSecurity会对所有post请求验证是否包含系统生成的csrf的token信息， 如果不包含，则报错。起到防止csrf攻击的效果。 

  

5. org.springframework.security.web.authentication.logout.LogoutFilter
   
   > 匹配URL为/logout的请求，实现用户退出,清除认证信息。 

  

6. org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter
   
   > 认证操作全靠这个过滤器，默认匹配URL为/login且必须为POST请求。 

  

7. org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter
   
   > 如果没有在配置文件中指定认证页面，则由该过滤器生成一个默认认证页面。

  

8. org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter
   
   > 由此过滤器可以生产一个默认的退出登录页面

  

9. org.springframework.security.web.authentication.www.BasicAuthenticationFilter 
   > 此过滤器会自动解析HTTP请求中头部名字为Authentication，且以Basic开头的头信息。

   

10. org.springframework.security.web.savedrequest.RequestCacheAwareFilter 
    > 通过HttpSessionRequestCache内部维护了一个RequestCache，用于缓存HttpServletRequest

    

11. org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter
    > 针对ServletRequest进行了一次包装，使得request具有更加丰富的API  

    

12. org.springframework.security.web.authentication.AnonymousAuthenticationFilter 
    > 当SecurityContextHolder中认证信息为空,则会创建一个匿名用户存入到SecurityContextHolder中。 spring security为了兼容未登录的访问，也走了一套认证流程，只不过是一个匿名的身份。

    

13. org.springframework.security.web.session.SessionManagementFilter 
    > SecurityContextRepository限制同一用户开启多个会话的数量

    

14. org.springframework.security.web.access.ExceptionTranslationFilter 
    > 异常转换过滤器位于整个springSecurityFilterChain的后方，用来转换整个链路中出现的异常

    

15. org.springframework.security.web.access.intercept.FilterSecurityInterceptor
    
    > 获取所配置资源访问的授权信息，根据SecurityContextHolder中存储的用户信息来决定其是否有权限。

### Spring Security的 csrf 防护机制

跨站请求伪造（Cross-Site Request Forgery），一种难以防范的网络攻击方式。

1. 禁用csrf

   ```
       <!--禁用csrf防护机制-->
       <security:csrf disabled="true"/>
   ```

2.  页面请求中携带token请求

   1. 添加Spring Security标签库

      ```
      <%@taglib uri="http://www.springframework.org/security/tags" prefix="security"%>
      ```

   2. 在表单中加入语句添加csrf防护

      ```
      <security:csrfInput/>
      ```

#### 登陆表单

```
<form action="${pageContext.request.contextPath}/login" method="post">
	<security:csrfInput/>
	<div class="form-group has-feedback">
		<input type="text" name="username" class="form-control"
			placeholder="用户名"> <span
			class="glyphicon glyphicon-envelope form-control-feedback"></span>
	</div>
	<div class="form-group has-feedback">
		<input type="password" name="password" class="form-control"
			placeholder="密码"> <span
			class="glyphicon glyphicon-lock form-control-feedback"></span>
	</div>
	<div class="row">
		<div class="col-xs-8">
			<div class="checkbox icheck">
				<label><input type="checkbox" name="remember-me" value="true"> 记住 下次自动登录</label>
			</div>
		</div>
		<!-- /.col -->
		<div class="col-xs-4">
			<button type="submit" class="btn btn-primary btn-block btn-flat">登录</button>
		</div>
		<!-- /.col -->
	</div>
</form>
```

#### 注销表单

```
<form action="${pageContext.request.contextPath}/logout" method="post">
	<security:csrfInput/>
	<input type="submit" value="注销">
</form>
```

基于csrf防护机制的底层源码`CsrfFilter.java`

```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.security.web.csrf;

import java.io.IOException;
import java.util.Arrays;
import java.util.HashSet;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.security.web.access.AccessDeniedHandlerImpl;
import org.springframework.security.web.util.UrlUtils;
import org.springframework.security.web.util.matcher.RequestMatcher;
import org.springframework.util.Assert;
import org.springframework.web.filter.OncePerRequestFilter;

public final class CsrfFilter extends OncePerRequestFilter {
    public static final RequestMatcher DEFAULT_CSRF_MATCHER = new CsrfFilter.DefaultRequiresCsrfMatcher();
    private final Log logger = LogFactory.getLog(this.getClass());
    private final CsrfTokenRepository tokenRepository;
    private RequestMatcher requireCsrfProtectionMatcher;
    private AccessDeniedHandler accessDeniedHandler;

    public CsrfFilter(CsrfTokenRepository csrfTokenRepository) {
        this.requireCsrfProtectionMatcher = DEFAULT_CSRF_MATCHER;
        this.accessDeniedHandler = new AccessDeniedHandlerImpl();
        Assert.notNull(csrfTokenRepository, "csrfTokenRepository cannot be null");
        this.tokenRepository = csrfTokenRepository;
    }
	//通过这里可以看出SpringSecurity的csrf机制把请求方式分成两类来处理 
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        request.setAttribute(HttpServletResponse.class.getName(), response);
        CsrfToken csrfToken = this.tokenRepository.loadToken(request);
        boolean missingToken = csrfToken == null;
        if (missingToken) {
            csrfToken = this.tokenRepository.generateToken(request);
            this.tokenRepository.saveToken(csrfToken, request, response);
        }

        request.setAttribute(CsrfToken.class.getName(), csrfToken);
        request.setAttribute(csrfToken.getParameterName(), csrfToken);
        //第一类："GET", "HEAD", "TRACE", "OPTIONS"四类allowedMethods请求可以直接通过
        if (!this.requireCsrfProtectionMatcher.matches(request)) {
            filterChain.doFilter(request, response);
        } else {//第二类：除去上面四类，包括POST都要被验证携带token才能通过
            String actualToken = request.getHeader(csrfToken.getHeaderName());
            if (actualToken == null) {
                actualToken = request.getParameter(csrfToken.getParameterName());
            }

            if (!csrfToken.getToken().equals(actualToken)) {
                if (this.logger.isDebugEnabled()) {
                    this.logger.debug("Invalid CSRF token found for " + UrlUtils.buildFullRequestUrl(request));
                }

                if (missingToken) {
                    this.accessDeniedHandler.handle(request, response, new MissingCsrfTokenException(actualToken));
                } else {
                    this.accessDeniedHandler.handle(request, response, new InvalidCsrfTokenException(csrfToken, actualToken));
                }

            } else {
                filterChain.doFilter(request, response);
            }
        }
    }

    public void setRequireCsrfProtectionMatcher(RequestMatcher requireCsrfProtectionMatcher) {
        Assert.notNull(requireCsrfProtectionMatcher, "requireCsrfProtectionMatcher cannot be null");
        this.requireCsrfProtectionMatcher = requireCsrfProtectionMatcher;
    }

    public void setAccessDeniedHandler(AccessDeniedHandler accessDeniedHandler) {
        Assert.notNull(accessDeniedHandler, "accessDeniedHandler cannot be null");
        this.accessDeniedHandler = accessDeniedHandler;
    }
	
    private static final class DefaultRequiresCsrfMatcher implements RequestMatcher {
        private final HashSet<String> allowedMethods;

        private DefaultRequiresCsrfMatcher() {
            this.allowedMethods = new HashSet(Arrays.asList("GET", "HEAD", "TRACE", "OPTIONS"));
        }

        public boolean matches(HttpServletRequest request) {
            return !this.allowedMethods.contains(request.getMethod());
        }
    }
}
```

### Spring Security使用数据库数据完成认证

#### UserService接口继承UserDetailsService

**示例**

编写loadUserByUsername业务

1. **public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException**
   业务层继承UserDetailsService类的重写方法

2. **SimpleGrantedAuthority**
   以List的形式封装用户的角色，`List<SimpleGrantedAuthority> authorities = new ArrayList<>();`，并且向List中放置角色

   ```
   for (SysRole role : roles) {
   	authorities.add(new SimpleGrantedAuthority(role.getRoleName()));
   }
   ```

3. **new User(sysUser.getUsername(),sysUser.getPassword(),true,true,true,true,authorities);**
   需要给UserDetails类创建的对象，也是返回的类型。具体操作是拿着用户名往数据库中查询用户信息，把用户名和密码、用户状态返回，用于与当前页面输入的用户名和密码进行比对判断是否通过登陆

4. **四个true**

   - boolean enabled 是否可用
   - boolean accountNonExpired 账户是否失效
   - boolean credentialsNonExpired 秘密是否失效
   - boolean accountNonLocked 账户是否锁定

```
    /**
     * 认证业务
     * @param username 用户在浏览器输入的用户名
     * @return UserDetails 是springsecurity自己的用户对象
     * @throws UsernameNotFoundException
     */
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        try {
            //根据用户名做查询
            SysUser sysUser = userDao.findByName(username);
            if(sysUser==null){
                return null;
            }
            List<SimpleGrantedAuthority> authorities = new ArrayList<>();
            List<SysRole> roles = sysUser.getRoles();
            for (SysRole role : roles) {
                authorities.add(new SimpleGrantedAuthority(role.getRoleName()));
            }
            //{noop}后面的密码，springsecurity会认为是原文。
            UserDetails userDetails = new User(sysUser.getUsername(),
                    sysUser.getPassword(),
                    sysUser.getStatus()==1,
                    true,
                    true,
                    true,
                    authorities);
            return userDetails;
        }catch (Exception e){
            e.printStackTrace();
            //认证失败！
            return null;
        }
```

**返回的userDetails会与页面中用户输入的用户名密码进行比对**

#### 在Spring Security主配置文件中指定认证使用的业务对象

```
    <!--设置Spring Security认证用户信息的来源-->
    <!--
    springsecurity默认的认证必须是加密的，加上{noop}表示不加密认证。
    -->
    <security:authentication-manager>
        <security:authentication-provider user-service-ref="userServiceImpl">
            <security:password-encoder ref="passwordEncoder"/>
        </security:authentication-provider>
    </security:authentication-manager>
```

#### 加密认证

##### 在IOC容器中提供加密对象

```
    <!--加密对象--> 
    <bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
    <!--设置Spring Security认证用户信息的来源-->
    <!--
    springsecurity默认的认证必须是加密的，加上{noop}表示不加密认证。
    -->
    <security:authentication-manager>
        <security:authentication-provider user-service-ref="userServiceImpl">
            <security:password-encoder ref="passwordEncoder"/>
        </security:authentication-provider>
    </security:authentication-manager>
```

##### 相应修改用户业务的操作

```
@Override    
public void save(SysUser user) {        //对密码进行加密，然后再入库        						user.setPassword(passwordEncoder.encode(user.getPassword()));        					userDao.save(user);
}
```

做加密认证后，此时数据库存储的应该是用户密码加密后的数据，从而通过用户名查询后，读取到用户密码数据返回给Spring Security时，Spring Security可以使用BCryptPasswordEncoder解密与当前页面输入的密码进行比对。

### 开启Remember-me自动认证

##### 原理

通过记录remember-me这个Cookie值于浏览器中，二次打开网页时Spring Security自动调用autoLogin进行自动认证登陆

**login.jsp**

```
<label><input type="checkbox" name="remember-me" value="true"> 记住 下次自动登录 </label>
```

**spring-security.xml**

```
        <security:remember-me
                token-validity-seconds="60"
                remember-me-parameter="remember-me"/>
```

#### 持久化remember me信息

remember me信息保存在Cookie中，在浏览器中暴露会不安全，防止用户被窃取，Spring Security还提供了remember me的另一种相对更安全的实现机制 :在客户端的cookie中，仅保存一个 无意义的加密串（与用户名、密码等敏感数据无关），然后在db中保存该加密串-用户信息的对应关系，自动登录 时，用cookie中的加密串，到db中验证，如果通过，自动登录才算通过。

**创建数据库表**

```
CREATE TABLE `persistent_logins` (  
`username` varchar(64) NOT NULL,  
`series` varchar(64) NOT NULL,  
`token` varchar(64) NOT NULL,  
`last_used` timestamp NOT NULL,
PRIMARY KEY (`series`) 
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

**spring-security.xml**

        <security:remember-me
        		data-source-ref="dataSource"
                token-validity-seconds="60"
                remember-me-parameter="remember-me"/>
#### 显示当前用户的用户名

```
<security:authentication property="name" />
```

### 权限操作

#### 对应前端页面的显示

```
<security:authorize access="hasAnyRole('ROLE_PRODUCT', 'ROLE_ADMIN')">
<li id="system-setting"><a
        href="${pageContext.request.contextPath}/product/findAll">
    <i class="fa fa-circle-o"></i> 产品管理
</a></li>
</security:authorize>
```

#### 开启权限的注解支持

三类权限注解，用一类即可

```
    <!--
        开启权限控制的注解支持
        secured-annotations="enabled"     springSecurity内部的权限控制注解开关
        pre-post-annotations="enabled"     spring指定的权限控制的注解开关
        jsr250-annotations="enabled"      开启java250注解支持
        -->
    <security:global-method-security
            secured-annotations="enabled"
            pre-post-annotations="enabled"
            jsr250-annotations="enabled"/>
```

#### 在注解支持对应类或者方法上添加注解

```
@Controller
@RequestMapping("/product")
public class ProductController {

    //@Secured({"ROLE_PRODUCT","ROLE_ADMIN"})//springSecurity内部制定的注解
    //@RolesAllowed({"ROLE_PRODUCT","ROLE_ADMIN"})//jsr250注解
    @PreAuthorize("hasAnyAuthority('ROLE_PRODUCT','ROLE_ADMIN')")//spring的el表达式注解
    @RequestMapping("/findAll")
    public String findAll(){
        return "product-list";
    }

}
```

### 权限不足异常处理

#### 方案一（少用）

**在spring-security.xml配置文件中处理**

处理403异常，只能处理403异常

```
<!--设置可以用spring的el表达式配置Spring Security并自动生成对应配置组件（过滤器）-->
<security:http auto-config="true" use-expressions="true">
	<!--省略其它配置-->    
	<!--403异常处理-->    
	<security:access-denied-handler error-page="/403.jsp"/>
</security:http>
```

#### 方案二（不适用于前后端分离）

**在web.xml配置文件中处理**

处理403/404异常，可根据错误码处理多个异常

```
    <!--处理403异常-->
    <error-page>
        <error-code>403</error-code>
        <location>/403.jsp</location>
    </error-page>

    <!--处理404异常-->
    <error-page>
        <error-code>404</error-code>
        <location>/404.jsp</location>
    </error-page>
```

#### 方案三（推荐）

**编写HandlerControllerAdvice.java处理**

处理403/500异常，根据抛出的异常做相应地跳转处理

```
@ControllerAdvice
public class HandlerControllerAdvice{

    @ExceptionHandler(AccessDeniedException.class)
    public String handlerException(){
        return "redirect:/403.jsp";
    }

    @ExceptionHandler(RuntimeException.class)
    public String runtimeHandlerException(){
        return "redirect:/500.jsp";
    }

}
```

