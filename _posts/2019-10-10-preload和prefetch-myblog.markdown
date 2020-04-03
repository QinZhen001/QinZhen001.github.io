---
layout:     post
title:      "preload和prefetch"
date:       2019-10-10 18:03:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”


## 正文


最近在研究vue-cli3，发现用到了一个插件PreloadPlugin，所以在这里学习一下preload和prefetch的知识。


### Preload

Preload 是一种新的web标准，旨在提高性能并为web开发人员提供更细粒度的加载控制

Preload is a web standard aimed at improving performance and granular loading of resources. It is a declarative fetch that can tell a browser to start fetching a source because a developer knows the resource will be needed soon. 

```html
<link rel="preload" ></link>
```

preload通常用于本页面要用到的关键资源，包括关键js、字体、css文件。preload将会把资源得下载顺序权重提高，使得关键数据提前下载好，优化页面打开速度。



**preload 是声明式的 fetch，可以强制浏览器请求资源，同时不阻塞文档 [onload](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onload) 事件。**



preload：当前页面使用，尽早下载，优先级较高；




### Prefetch
```html
<link rel="prefetch" ></link>
```



​	prefetch：首次渲染时不需要，之后可能需要。优先级较低，在浏览器空闲时才会下载。使用场景：比如当前页可能跳转的页面，或者条件加载的资源。



------



```js
什么时候该用 <link rel=”preload”> ？ 什么时候又该用 <link rel=”prefetch”> ?
```



**建议：对于当前页面很有必要的资源使用 preload，对于可能在将来的页面中使用的资源使用 prefetch。**

preload 是对浏览器指示预先请求当前页需要的资源（关键的脚本，字体，主要图片）。

prefetch 应用场景稍微又些不同 —— 用户将来可能在其他部分（比如视图或页面）使用到的资源。如果 A 页面发起一个 B 页面的 prefetch 请求，这个资源获取过程和导航请求可能是同步进行的，而如果我们用 preload 的话，页面 A 离开时它会立即停止。



preload 和 prefetch 都被存储在 **HTTP 缓存中**。



当一个资源被 **preload 或者 prefetch** 获取后，它可以从 HTTP 缓存移动至渲染器的内存缓存中。如果资源可以被缓存（比如说存在有效的[cache-control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) 和 max-age），它被存储在 HTTP 缓存中可以被**现在或将来的任务使用**，如果资源不能被缓存在 HTTP 缓存中，作为代替，它被放在内存缓存中直到被使用。




## 补充



### @vue/preload-webpack-plugin


[https://www.npmjs.com/package/@vue/preload-webpack-plugin](https://www.npmjs.com/package/@vue/preload-webpack-plugin)























