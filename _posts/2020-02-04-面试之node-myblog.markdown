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





## Node了解

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 



## node的http模块创建服务与Express或Koa框架有何不同



express是一个服务端框架,框架简单封装了node的http模块,express支持node原生的写法,express不仅封装好服务器，还封装了中间件、路由等特征，方便开发web服务器，换句话说express = http模块 + 中间件 + 路由 

https://juejin.im/post/5a5f5a126fb9a01cb0495b4c)



## node多线程

查了很多资料，都是说 Node.js 只支持单线程。但是可以开启多进程充分利用多核 CPU。


>线程是cpu调度的一个基本单位，一个cpu同时只能执行一个线程的任务



## Node.js 中的进程与线程


[https://juejin.im/post/5d43017be51d4561f40adcf9?utm_source=gold_browser_extension](https://juejin.im/post/5d43017be51d4561f40adcf9?utm_source=gold_browser_extension)


**进程**



进程Process是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础，进程是线程的容器（来自百科）。**进程是资源分配的最小单位**。我们启动一个服务、运行一个实例，就是开一个服务进程，例如 Java 里的 JVM 本身就是一个进程，Node.js 里通过 node app.js 开启一个服务进程，多进程就是进程的复制（fork），fork 出来的每个进程都拥有自己的独立空间地址、数据栈，一个进程无法访问另外一个进程里定义的变量、数据结构，只有建立了 IPC 通信，进程之间才可数据共享。

-----------

Node.js开启服务进程例子


```javascript
const http = require('http');

const server = http.createServer();
server.listen(3000,()=>{
    process.title='程序员成长指北测试进程';
    console.log('进程id',process.pid)
})
```

-----------

**线程**



**线程是操作系统能够进行运算调度的最小单位，首先我们要清楚线程是隶属于进程的，被包含于进程之中。一个线程只能隶属于一个进程，但是一个进程是可以拥有多个线程的。**

-------


**单线程**


**单线程就是一个进程只开一个线程**




Javascript 就是属于单线程，程序顺序执行(这里暂且不提JS异步)，可以想象一下队列，前面一个执行完之后，后面才可以执行，当你在使用单线程语言编码时切勿有过多耗时的同步操作，否则线程会造成阻塞，导致后续响应无法处理。你如果采用 Javascript 进行编码时候，请尽可能的利用Javascript异步操作的特性。



>Node.js 虽然是单线程模型，但是其基于事件驱动、异步非阻塞模式，可以应用于高并发场景，避免了线程创建、线程之间上下文切换所产生的资源开销。


>当你的项目中需要有大量计算，CPU 耗时的操作时候，要注意考虑开启多进程来完成了。


>Node.js 开发过程中，错误会引起整个应用退出，应用的健壮性值得考验，尤其是错误的异常抛出，以及进程守护是必须要做的。





>单线程无法利用多核CPU，但是后来Node.js 提供的API以及一些第三方工具相应都得到了解决

在单核 CPU 系统之上我们采用 单进程 + 单线程 的模式来开发。在多核 CPU 系统之上，可以通过 child_process.fork 开启多个进程（Node.js 在 v0.8 版本之后新增了Cluster 来实现多进程架构） ，即 多进程 + 单线程 模式。注意：开启多进程不是为了解决高并发，主要是解决了单进程模式下 Node.js CPU 利用率不足的情况，充分利用多核 CPU 的性能。

作者：大西轰已在服务区
链接：https://juejin.im/post/5d43017be51d4561f40adcf9
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



------

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









## Koa2 中间件机制

https://juejin.im/post/5a5f5a126fb9a01cb0495b4c





## Express和Koa框架中间件有什么不同



- express 中间件：是通过 next 的机制，即上一个中间件会通过 next 触发下一个中间件   (express中间件处理方式是线性的，next过后继续寻找下一个中间件，当然如果没有调用next()的话，就不会调用下一个函数了，调用就会被终止)
- koa2 中间件：是通过 async await 实现的，中间件执行顺序是“洋葱圈”模型（推荐）



















