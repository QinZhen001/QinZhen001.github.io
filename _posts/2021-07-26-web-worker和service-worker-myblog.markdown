---
layout:     post
title:      "web-worker和service-worker"
date:       2021-07-26 16:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>

# 正文

[https://tinyshare.cn/post/HpDVBvTWbUD](https://tinyshare.cn/post/HpDVBvTWbUD)

Web worker，service worker和worklet，这些都是“JavaScript Workers”，虽然它们在运行方式上有一些相似的地方，并且它们在使用上也有一些重叠的地方。

一般来说，一个worker是一个脚本在浏览器主线程之外的单独的线程上运行。如果你想要在HTML文档中引用一个`<script>`标签的典型的JavaScript文件，它会运行在主线程上。如果主线程上有太多的计算，会拖慢网站的速度，造成交互卡顿和响应延迟。





## **Web workers**

Web workers是最常用的worker类型。它不像另外两种，它们除了运行在主线程外的特性外，没有一个特殊的应用场景。所以，web worker可以用于减少主线程上大量的线程活动。

![web-worker](https://bitsofco.de/content/images/2018/11/web-worker.jpg)

**worker.js**文件中的任何代码都会开始运行，web worker最有用的是用于减少那些可能需要花费很长时间的工序或者与其他线程上平行运行。一个很好的例子是图片线程web应用：[Squoosh](https://squoosh.app/)，它使用web worker来处理图片处理任务，可以让主线程有精力处理用户与应用的交互而不被打扰。

像所有的worker，web worker没有获取DOM的权限，这意味着任何需要的信息将被在worker和主脚本间传递，传递参数使用**window.postMessage()**。

```javascript
/* main.js */线程

// 创建 worker
const myWorker = new Worker('worker.js');

// 向 worker 传递信息
myWorker.postMessage('Hello!');

// 接收从 worker 传递过来的信息
myWorker.onmessage = function(e) {
  console.log(e.data);
}
```

在worker脚本中，我们可以监听主脚本中的消息，并将响应返回。

```js
/* worker.js */

// 接收主文件的信息
self.onmessage = function(e) {
  console.log(e.data);

  // 向主文件发送信息
  self.postMessage(workerResult);
}
```

## **Service workers**

Service workers 是一种提供详细的作为浏览器和网络或缓存间的代理的服务。

![service-worker](https://bitsofco.de/content/images/2018/11/service-worker.jpg)

与Web worker类似，service worker 是在主脚本文件中注册，引用到一个专门的service worker文件。

```js
/* main.js */
navigator.serviceWorker.register('/service-worker.js');

```

与一般的web worker不同，service worker有一些额外的特性来实现代理的目的。只要它们被安装且被激活，service worker就可以拦截主文档中发起的任何网络请求。

```js
/* service-worker.js */

// Install （安装）
self.addEventListener('install', function(event) {
    // ...
});

// Activate （激活）
self.addEventListener('activate', function(event) {
    // ...
});

// 监听主文档中的网络请求
self.addEventListener('fetch', function(event) {
    // ...
});

```

只要一被拦截，service worker就可以返回一个缓存中的文档作为响应，而不用走网络请求，因此可以让应用离线运行。

```js
/* service-worker.js */

self.addEventListener('fetch', function(event) {
    // 返回缓存中的数据
    event.respondWith(
        caches.match(event.request);
    );
});
```

