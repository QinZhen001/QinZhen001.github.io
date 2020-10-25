---
layout:     post
title:      "DNS预解析详解"
date:       2018-04-11 23:10:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 网络
---

> “Yeah It's on. ”


## 正文
[网页链接](https://www.xuanfengge.com/dns-prefetching-analysis.html)

DNS解析时间可能导致大量用户感知延迟，DNS解析所需的时间差异非常大，延迟范围可以从1ms（本地缓存结果）到普遍的几秒钟时间。所以利用DNS预解析是有意义的。


### DNS与域名解析

![浏览器缓存-系统缓存-路由器缓存-ISP DNS缓存-递归搜索][1]


DNS全称为Domain Name System，即域名系统，是域名和IP地址相互映射的一个分布式数据库。

域名解析即通过主机名，最终得到该主机名对应的IP地址的过程。

浏览器对网站第一次的域名DNS解析查找流程依次为：




### 解决方案

DNS Prefetching

DNS 请求需要的带宽非常小，但是延迟却有点高，**这点在手机网络上特别明显**。DNS预解析 能让延迟明显减少一些，例如用户点击链接时。在某些情况下，延迟能减少一秒钟。

X-DNS-Prefetch-Control 头控制着浏览器的DNS预解析功能

on：启用DNS预解析。在浏览器支持DNS预解析的特性时及时不适用该标签浏览器依然会进行预解析。

off：关闭DNS预解析。这个属性在页面上的链接并不是由你控制的或是你根本不想向这些域名引导数据时非常有用。


预解析的实现：

1. 用meta信息来告知浏览器, 当前页面要做DNS预解析:`<meta http-equiv="x-dns-prefetch-control" content="on" />`
2. 在页面header中使用link标签来强制对DNS预解析: `<link rel="dns-prefetch" href="http://bdimg.share.baidu.com" />`



>也可以通过在服务器端发送 X-DNS-Prefetch-Control 报头

![enter description here][2]


[1]: http://cdn.xuanfengge.com/wp-content/uploads/2017/05/sdfgbf.jpg
[2]: http://cdn.xuanfengge.com/wp-content/uploads/2017/05/sdfdsf.png