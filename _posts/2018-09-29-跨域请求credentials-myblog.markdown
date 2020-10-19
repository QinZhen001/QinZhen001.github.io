---
layout:     post
title:      "跨域请求之credentials"
date:       2018-09-29 18:16:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 网络
---

> “Yeah It's on. ”


## 正文
[网页链接](https://developer.mozilla.org/zh-CN/docs/Web/API/Request/credentials)

[https://blog.csdn.net/vincent_ling/article/details/51714691](https://blog.csdn.net/vincent_ling/article/details/51714691)

credentials 是Request接口的只读属性，用于表示用户代理是否应该在跨域请求的情况下从其他域发送cookies。这与XHR的withCredentials 标志相似，不同的是有三个可选值（后者是两个）：

* omit: 从不发送cookies.
* same-origin: 只有当URL与响应脚本同源才发送 cookies、 HTTP Basic authentication 等验证信息.(浏览器默认值,在旧版本浏览器，例如safari 11依旧是omit，safari 12已更改)
* include: 不论是不是跨域的请求,总是发送请求资源域在本地的 cookies、 HTTP Basic authentication 等验证信息.



在以下代码中，我们使用Request.Request()创建了一个新的request（为了一个与脚本在同一目录下的图片文件）， 接着将request credentials存入一个变量：
```javascript
var myRequest = new Request('flowers.jpg');
var myCred = myRequest.credentials; // returns "same-origin" by default
```


遇到的问题：

默认情况下，标准的跨域请求是不会发送cookie等用户认证凭据的。所以，当你再次访问远程api的时候，cookie是不会被带上的，于是乎，服务器理所当然地认为你还没有登录。MDN上的简单介绍 credentials 。用XMLHttpRequest请求的时候，我们需要设置属性
 withCredentials=true ;


```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', 'http://www.xxx.com/api');
xhr.withCredentials = true;
xhr.onload = onLoadHandler;
xhr.send();
```


**需要注意的是，当这个属性为true的时候，远程服务器也要作相应的处理。在响应头那里设置  Access-Control-Allow-Credentials: true 。如果没有这个设置的话，浏览器就会报错。**


----------


**引入新的问题**


然后，还有一点需要说明的是，当服务器设置了Access-Control-Allow-Credentials: true之后，Access-Control-Allow-Origin就不能设置为 * 了(可能会有这个问题)


----------


上面的是使用原生的ajax请求，实际上很多人都选择诸如jquery这类框架。我之前是在beforeSend方法那里设置 xhr.withCredentials=true。然后悲剧地发现根本不能达到我的预期效果。其实，不是这么用的。应该是作为一个属性，而不是方法里面设置。与data属性并列设置就好。所以看起来是这样子的




```javascript
function func() {
        $.ajax({
            type: "GET",
            dataType: "json",
            xhrFields: {
                withCredentials: true    // 要在这里设置
            },
            url: 'https://xxx.com/api/login',
            success: function (data) {
 
            },
            beforeSend: function (xhr) {
//                下面的设置无效
//                xhr.withCredentials = true;
            },
            error: function (err) {
                alert(JSON.stringify(err))
            }
        })
    }
```