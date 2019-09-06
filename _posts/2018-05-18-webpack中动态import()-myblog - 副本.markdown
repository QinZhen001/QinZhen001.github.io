---
layout:     post
title:      "webpack中动态import()"
date:       2018-05-18 13:29:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Webpack
---

> “Yeah It's on. ”


## 正文
[网页链接](https://segmentfault.com/a/1190000008787672)



>需要翻墙 [https://medium.com/webpack/webpack-4-import-and-commonjs-d619d626b655](https://medium.com/webpack/webpack-4-import-and-commonjs-d619d626b655)

目前，类函模import()块加载的语法建议——syntax proposal整体交给ECMAScript。
ES2015(es6)加载器说明定义import()作为一个方法用来动在运行时态加载es6模块。


在webpack中的import()是个分离点——**split-point**，用来把被请求的模块独立成一个单独的模块。**import()把模块的名字作为一个参数，并且返回一个Promise: import(name)->Promise.**


index.js
```javascript
function determineDate() {
  import('moment').then(function(moment) {
    console.log(moment().format());
  }).catch(function(err) {
    console.log('Failed to load moment', err);
  });
}

determineDate();
```




