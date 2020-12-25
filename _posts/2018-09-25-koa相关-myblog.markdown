---
layout:     post
title:      "koa相关"
date:       2018-09-23 23:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Node
---

> “Yeah It's on. ”


# 正文


## 何时使用await next()

[网页链接](https://blog.csdn.net/u014445339/article/details/79372834)

问题场景还原

```js
app.use((context, next) => {
    if (context.request.method === 'OPTIONS') {
        context.response.status = 200
        context.response.set('Access-Control-Allow-Origin', context.request.headers.origin)
        context.response.set('Access-Control-Allow-Headers', 'content-type')
    } else {
        next()
    }
})

app.use(async (context, next) => {
    // 异步操作数据库
    let result = await readdb()
    context.response.status = 200
    context.response.set('Access-Control-Allow-Origin', context.request.headers.origin)
    context.response.set('Access-Control-Allow-Headers', 'content-type')
    context.response.message = '读取成功'
}) 
```


在上述代码中，第一个use的回调对options请求做过滤，第二个use的回调等待读取数据库，然后设置response。



### 问题：

所有除options请求外的请求，返回的都是404。在第二个use的回调中打断点，发现确实执行了，但是返回的却不是设置的200，而是404，很是奇怪。


### 原因：


第二个use的回调是异步函数，而第一个use不是，所以执行next()后，不会等待第二个use的异步操作完成，而直接返回response，从而第二个use回调中异步操作之后的代码没有影响到这个返回的response（此时，response没有设置任何返回信息，koa会默认是404），而在第二个use的异步操作完成之后，后面的代码会执行（打断点能有反应的原因），但此时response已经返回，后面代码虽然改变了response，但是已经不会发送给客户端了。


### 正确玩法


在第一个use回调中添加await next()，等待第二个use回调函数完整执行完毕，再发送response。代码如下。
```js
app.use(async (context, next) => {
    if (context.request.method === 'OPTIONS') {
        context.response.status = 200
        context.response.set('Access-Control-Allow-Origin', context.request.headers.origin)
        context.response.set('Access-Control-Allow-Headers', 'content-type')
    } else {
        await next()
    }
})

app.use(async (context, next) => {
    // 异步操作数据库
    let result = await readdb()
    context.response.status = 200
    context.response.set('Access-Control-Allow-Origin', context.request.headers.origin)
    context.response.set('Access-Control-Allow-Headers', 'content-type')
    context.response.message = '读取成功'
}) 
```









## 中间件





### koa-bodyparser

[ https://www.npmjs.com/package/koa-bodyparser ]( https://www.npmjs.com/package/koa-bodyparser )

 

To use `koa-bodyparser` with koa@1, please use [bodyparser 2.x](https://github.com/koajs/bodyparser/tree/2.x).

```
npm install koa-bodyparser@2 --save
```





> **Notice: this module don't support parsing multipart format data**, please use [co-busboy](https://github.com/cojs/busboy) to parse multipart format data.





**很重要的一点 koa-bodyparser 不支持 multipart/form-data**  







# 原理

[https://chenshenhai.github.io/koa2-note/note/start/info.html](https://chenshenhai.github.io/koa2-note/note/start/info.html)

```js
├── lib
│   ├── application.js
│   ├── context.js
│   ├── request.js
│   └── response.js
└── package.json
```

核心代码就是lib目录下的四个文件

- application.js 是整个koa2 的入口文件，封装了context，request，response，以及最核心的中间件处理流程。
- context.js 处理应用上下文，里面直接封装部分request.js和response.js的方法
- request.js 处理http请求
- response.js 处理http响应





## **Context**

[https://github.com/berwin/Blog/issues/8](https://github.com/berwin/Blog/issues/8)

![img](https://camo.githubusercontent.com/b0e0a46aa132ed314f3058e922f000de85c0a58e2de5dd46b80a46334c798a21/687474703a2f2f62657277696e2e6769746875622e696f2f707074732f6b6f612f696d672f6b6f612d666c6f772e6a7067)

```js
// 下面是Context源码片段。

var delegate = require('delegates');
var proto = module.exports = {}; // 一些自身方法，被我删了

/**
 * Response delegation.
 */

delegate(proto, 'response')
  .method('attachment')
  .method('redirect')
  .method('remove')
  .method('vary')
  .method('set')
  
.....

/**
 * Request delegation.
 */

delegate(proto, 'request')
  .method('acceptsLanguages')
  .method('acceptsEncodings')
  .method('acceptsCharsets')
  .method('accepts')
  .method('get')

......
```

delegates是第三方npm包，功能就是把一个对象上的方法，属性委托到另一个对象上

面那一排方法，都是Request和Response静态类中的方法

method方法是委托方法，getter方法用来委托getter，access方法委托getter+setter











