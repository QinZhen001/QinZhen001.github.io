---
layout:     post
title:      "Express相关"
date:       2017-12-15 16:12:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 后端
---

> “Yeah It's on. ”


## 正文

基于 Node.js 平台，快速、开放、极简的 web 开发框架。


http://www.expressjs.com.cn/

[Express-api](https://www.runoob.com/w3cnote/express-4-x-api.html)



### app.get、app.use、app.all 的区别是什么

[https://juejin.im/post/5ef57aca6fb9a07e5f516814?utm_source=gold_browser_extension](https://juejin.im/post/5ef57aca6fb9a07e5f516814?utm_source=gold_browser_extension)





## middleware 





### route-cache

Blazing fast 🚄 Express middleware for route caching with a given TTL (in seconds)

> 使用给定TTL(以秒为单位)进行路由缓存的快速中间件  



####  TTL

>  Time To Live的缩写



简单来说，TTL是IP协议包中的一个值，它告诉网络路由器包在网络中的时间是否太长而应被丢弃。有很多原因使包在一定时间内不能被传递到目的地。



TTL由IP数据包的发送者设置，在IP数据包从源到目的的整个转发路径上，每经过一个路由器，则把该TTL的值减1，然后再将IP包转发出去。如果在IP包到达目的IP之前，TTL减少为0，路由器将会丢弃收到的TTL=0的IP包，并向IP包的发送者发送 ICMP time exceeded消息，以防止数据包不断在IP互联网络上永不终止地循环。



**TTL的主要作用是避免IP包在网络中的无限循环和收发，节省了网络资源，并能使IP包的发送者能收到告警消息。**













