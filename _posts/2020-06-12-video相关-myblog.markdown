---
layout:     post
title:      "video相关"
date:       2020-06-12 19:16:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>








# 正文





### 设置referrer解决403





抖音视频的url直接在浏览器中打开是没有问题的,直接打开本地的.html文件也是可以正常播放视频的,但访问服务器上的请求过来的页面就无法播放视频，浏览器里按F12查看network发现video标签里的src请求视频资源时报**403 Forbiddn** 错误。



经过对比发现错误的请求头中多了个**Referer**



估计请求的是服务器 referer 做了判断，不是正常的referer就拒绝了，可以模拟他们的 referer 请求试下，测试发现, 通过 https 站点打开的页面, 可以正常打开视频链接，在 https 下, 发送的请求是不会带有 Referer 的header 的, 这个时候是可以正常加载视频的. 所以, 我们在 页面的 head 标签内, 增加一行代码,指定浏览器任何情况下都不发送Referer,这样就可以正常加载资源了。



```xml
<meta name="referrer" content="no-referrer">
```











