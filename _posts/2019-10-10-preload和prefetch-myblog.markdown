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


### Prefetch
```html
<link rel="prefetch" ></link>
```


* preload：当前页面使用，尽早下载，优先级较高；
* prefetch：首次渲染时不需要，之后可能需要。优先级较低，在浏览器空闲时才会下载。使用场景：比如当前页可能跳转的页面，或者条件加载的资源。





## 补充



### @vue/preload-webpack-plugin


[https://www.npmjs.com/package/@vue/preload-webpack-plugin](https://www.npmjs.com/package/@vue/preload-webpack-plugin)























