---
title: 数据库语句优化
date:  	2020-03-13 21:56:36 +0800
category: Original
tags: SQL
excerpt: 把平时遇到的问题和优化的过程记录下来
---

**使用子查询优化大数据量分页查询**

这种方式的做法是先定位偏移位置的id，然后再往后查询，适用于id递增的情况。

```
select * from orders_history where type = 8 limit 100000,1;

select id from orders_history where type = 8 limit 100000,1;

select * from orders_history where type = 8 and id >= (
    select id from orders_history where type = 8 limit 100000,1
) limit 100;

select * from orders_history where type = 8 limit 100000,100;
```

上面4条语句的查询时间如下：

第1条语句：3674ms。

第2条语句：1315ms。

第3条语句：1327ms。

第4条语句：3710ms。

针对上面的查询需要注意：

1.比较第1条语句和第2条语句：使用select id代替select *速度增加了3倍。

2.比较第2条语句和第3条语句：速度相差几十毫秒。

3.比较第3条语句和第4条语句：得益于select id速度增加，第3条语句查询速度增加了3倍。

4.这种方式相较于原始一般的查询方法，将会增快数倍。