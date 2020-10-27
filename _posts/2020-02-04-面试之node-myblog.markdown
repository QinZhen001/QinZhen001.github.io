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

[https://mp.weixin.qq.com/s?__biz=MzUxOTEzNjEwMQ==&mid=2247483676&idx=1&sn=10d2a069b52833eb324d4adbdab27a05&chksm=f9ff7716ce88fe00871f96dc55936a9b6c00a4ac5c727c687dc6cbc0b06bb83db5101fb97051&scene=21#wechat_redirect](https://mp.weixin.qq.com/s?__biz=MzUxOTEzNjEwMQ==&mid=2247483676&idx=1&sn=10d2a069b52833eb324d4adbdab27a05&chksm=f9ff7716ce88fe00871f96dc55936a9b6c00a4ac5c727c687dc6cbc0b06bb83db5101fb97051&scene=21#wechat_redirect)



## Node了解

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 





## nodejs 的单线程架构模型



优势：

- 单线程就一个线程在玩，省去了线程间切换的开销
- 还有线程同步的问题，线程冲突的问题的也不需要担心

劣势：

- 劣势也很明显，现在起步都是 4 核，单线程没法充分利用 cpu 的资源
- 单线程，一旦崩溃，应用就挂掉了，大家调试脚本也知道一旦执行过程报错了，本次调试就直接结束了
- 因为只能利用一个 cpu ，一旦 cpu 被某个计算一直占用， cpu 得不到释放，后续的请求就会一直被挂起，直接无响应了

> 当然这些劣势都已经有成熟的解决方案了，使用 PM2 管理进程，或者上 K8S 也可以



###  **nodejs 的事件循环（重要）**

![](https://mmbiz.qpic.cn/mmbiz_png/68e53Trxt1gCoLYibhDAZ5wOAadwAsxzevKW0Wlco5QwzKxJeyGq8z3bAcmbRmdiaaepajdibdBlwNUn7PyC3rJbg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



如上图，事件循环中细分为这六个阶段，依次如下：

1. `Timers`: 定时器 Interval Timoout 回调事件，将依次执行定时器回调函数
2. `Pending`: 一些系统级回调将会在此阶段执行
3. `Idle,prepare`: 此阶段"仅供内部使用"
4. `Poll`: IO回调函数，这个阶段较为重要也复杂些，
5. `Check`: 执行 setImmediate() 的回调
6. `Close`: 执行 socket 的 close 事件回调



与我们开发相关的三个阶段分别是 `Timers Poll Check`



* **`Timers` ：执行定时器的回调，但注意，在 node 11 前，连续的几个定时器回调会连续的执行，而不是像浏览器那样，执行完一个宏任务立即执行微任务。**

* `Check` ：这个阶段执行 setImmediate() 的回调，这个事件只在 nodejs 中存在。

* `Poll` ：上面两个阶段的触发，其实是在 poll 阶段触发的。

  



## node微任务的执行时机

那么微任务是在什么时候执行呢？

在上图，黄色的几个阶段的旁边挨着个小块 `microtask`，每个阶段执行后就立即执行微任务队列里的事件。



举个例子：

```js
setTimeout(() => {
  console.log('timer1')
  Promise.resolve().then(function() {
    console.log('promise1')
  })
}, 0)
setTimeout(() => {
  console.log('timer2')
  Promise.resolve().then(function() {
    console.log('promise2')
  })
}, 0)
```

对浏览器事件队列熟悉的朋友很快就可得出 `浏览器中 timer1->promise1->timer2->promise2`，在浏览器中微任务队列是在每个宏任务执行完成后立即执行的。



那么在 nodejs 中呢？

结果是这样的：`timer1->timer2->promise1->promise2` ，因为微任务队列是在每个阶段完成后立即执行，所以 Timer 阶段有两个回调事件，将事件依次执行后，在进入下一阶段的之前，先执行微队列中的事件。



**注意：这个结果是在 `node 10` 及以下的版本测试出来的，在 11 及以上的版本做了修改，执行的结果与浏览器的执行结果是一致的**

```
timer1->promise1->timer2->promise2
```







## node的http模块创建服务与Express或Koa框架有何不同



express是一个服务端框架,框架简单封装了node的http模块,express支持node原生的写法,express不仅封装好服务器，还封装了中间件、路由等特征，方便开发web服务器，换句话说express = http模块 + 中间件 + 路由 

https://juejin.im/post/5a5f5a126fb9a01cb0495b4c)







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



### node多进程

查了很多资料，都是说 Node.js 只支持单线程。但是可以开启多进程充分利用多核 CPU。


>线程是cpu调度的一个基本单位，一个cpu同时只能执行一个线程的任务





### child_process

在Node.js中，提供了一个child_process模块,通过它可以开启多个子进程，在多个子进程之间可以共享内存空间，可以通过子进程的互相通信来实现信息的交换。



[`child_process.spawn()`](http://nodejs.cn/s/CKoDGf)、[`child_process.fork()`](http://nodejs.cn/s/VDCJMa)、[`child_process.exec()`](http://nodejs.cn/s/pkpJMy) 和 [`child_process.execFile()`](http://nodejs.cn/s/N6uK8q) 方法都遵循其他 Node.js API 惯用的异步编程模式。



每个方法都返回一个 ChildProcess 实例。 这些对象实现了 Node.js 的 EventEmitter API，允许父进程注册监听器函数，在子进程的生命周期中当发生某些事件时会被调用。





### 开启多个子进程

单线程的一个缺点是不能充分利用多核，所以官方推出了 `cluster` 模块， **cluster 模块可以创建共享服务器端口的子进程**

```js
const cluster = require('cluster');
for (let i = 0; i < numCPUs; i++) {
    cluster.fork(); // 生成新的工作进程，可以使用 IPC 和父进程通信
}
```





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







# 补充





## 同步和异步

同步和异步关注的是消息通信机制。

- 同步：在发起一个调用后，在没有得到结果前，该调用不返回，知道调用返回，才往下执行，也就是说调用者等待被调用方返回结果。
- 异步：在发起一个调用后，调用就直接返回，不等待结果，继续往下执行，而执行的结果是由被调用方通过状态、通知等方式告知调用方，典型的异步编程模型比如 Node.js

## 阻塞和非阻塞

阻塞和非阻塞，关注的是在等待结果时，线程的状态。

- 阻塞：在等待调用结果时，线程挂起了，不往下执行
- 非阻塞：与上面相反，当前线程继续往下执行



> 什么时候会出现请求阻塞呢？涉及到大量计算的时候，因为计算是在 js 引擎上执行的，执行栈一直卡着，别的函数就没法执行，举个栗子，构建一个层级非常深的大对象，反复对这个这个对象 `JSON.parse(JSON.stringify(bigObj))`







## 进程间通信IPC

InterProcess Communication

[https://www.jianshu.com/p/c1015f5ffa74](https://www.jianshu.com/p/c1015f5ffa74)

每个进程各自有不同的用户地址空间，任何一个进程的全局变量在另一个进程中都看不到，所以进程之间要交换数据必须通过内核，在内核中开辟一块缓冲区，进程1把数据从用户空间拷到内核缓冲区，进程2再从内核缓冲区把数据读走，内核提供的这种机制称为**进程间通信（IPC，InterProcess Communication）**



