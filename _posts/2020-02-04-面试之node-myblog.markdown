---
大多数 Node.js 核心 API 构建于惯用的异步事件驱动架构，其中某些类型的对象（又称触发器，Emitter）会触发命名事件来调用函数（又称监听器，Listener）。layout:     post
title:      "前端之node"
date:       2020-02-04 17:50:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 面试
---

> “Yeah It's on. ”



# 面试之Node





## process进程

[https://juejin.im/post/5a996a87f265da239d48bebc](https://juejin.im/post/5a996a87f265da239d48bebc)



* 在Node.js中每个应用程序都是一个进程类的实例对象。
* 使用process对象代表应用程序,这是一个全局对象，可以通过它来获取Node.jsy应用程序以及运行该程序的用户、环境等各种信息的属性、方法和事件。





## child_process

在Node.js中，提供了一个child_process模块,通过它可以开启多个子进程，在多个子进程之间可以共享内存空间，可以通过子进程的互相通信来实现信息的交换。



[`child_process.spawn()`](http://nodejs.cn/s/CKoDGf)、[`child_process.fork()`](http://nodejs.cn/s/VDCJMa)、[`child_process.exec()`](http://nodejs.cn/s/pkpJMy) 和 [`child_process.execFile()`](http://nodejs.cn/s/N6uK8q) 方法都遵循其他 Node.js API 惯用的异步编程模式。



每个方法都返回一个 ChildProcess 实例。 这些对象实现了 Node.js 的 EventEmitter API，允许父进程注册监听器函数，在子进程的生命周期中当发生某些事件时会被调用。





## cluster



单个 Node.js 实例运行在单个线程中。 为了充分利用多核系统，有时需要启用一组 Node.js 进程去处理负载任务。



## EventEmitter



```js
const EventEmitter = require('events');
```



大多数 Node.js 核心 API 构建于惯用的异步事件驱动架构，其中某些类型的对象（又称触发器，Emitter）会触发命名事件来调用函数（又称监听器，Listener）。



**所有能触发事件的对象都是 `EventEmitter` 类的实例**



```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
  console.log('触发事件');
});
myEmitter.emit('event');
```





