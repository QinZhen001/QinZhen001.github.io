---
layout:     post
title:      "面试之node"
date:       2020-02-04 17:50:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 面试
---

> “Yeah It's on. ”



# 面试之Node

[nodejs 面试篇](https://mp.weixin.qq.com/s?__biz=MzUxOTEzNjEwMQ==&mid=2247483676&idx=1&sn=10d2a069b52833eb324d4adbdab27a05&chksm=f9ff7716ce88fe00871f96dc55936a9b6c00a4ac5c727c687dc6cbc0b06bb83db5101fb97051&scene=21#wechat_redirect)



## node了解

> nodejs提供那么多模块，以及能在各个平台上跑的飞起，不是因为js很牛逼，而是因为底层依赖了一些你不知道的技术。最大的两个依赖便是**v8和libuv**

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 

它是单线程单进程模式，nodeJS的单线程指js的引擎只有一个实列。且是在主线程执行的，这样的。



```text
Nodejs封装了所有与底层交流的信息，给开发者提供一致的接口定义。在不断升级v8和libuv的同时，依然能够做到接口的一致性，这个就是nodejs想要实现的目标。

v8：一个帮助你将js代码转变成可以在各个平台和机器上运行的机器码
libuv：帮助你调用平台和机器上各种系统特性，包括操作文件、监听socket等等
```





## node里的模块是什么

[https://juejin.cn/post/6844903951742025736](https://juejin.cn/post/6844903951742025736)

Node中，每个文件模块都是一个对象，它的定义如下：

```js
function Module(id, parent) {
  this.id = id;
  this.exports = {};
  this.parent = parent;
  this.filename = null;
  this.loaded = false;
  this.children = [];
}

module.exports = Module;

var module = new Module(filename, parent);
```

所有的模块都是 Module 的实例。可以看到，当前模块（module.js）也是 Module 的一个实例。





## require的加载机制

[https://juejin.cn/post/6844903951742025736](https://juejin.cn/post/6844903951742025736)

- 1、先计算模块路径
- 2、如果模块在缓存里面，取出缓存
- 3、加载模块
- 4、输出模块的exports属性



```js
// require 其实内部调用 Module._load 方法
Module._load = function(request, parent, isMain) {
  //  计算绝对路径
  var filename = Module._resolveFilename(request, parent);

  //  第一步：如果有缓存，取出缓存
  var cachedModule = Module._cache[filename];
  if (cachedModule) {
    return cachedModule.exports;

  // 第二步：是否为内置模块
  if (NativeModule.exists(filename)) {
    return NativeModule.require(filename);
  }
  
  /********************************这里注意了**************************/
  // 第三步：生成模块实例，存入缓存
  // 这里的Module就是我们上面的1.1定义的Module
  var module = new Module(filename, parent);
  Module._cache[filename] = module;

  /********************************这里注意了**************************/
  // 第四步：加载模块
  // 下面的module.load实际上是Module原型上有一个方法叫Module.prototype.load
  try {
    module.load(filename);
    hadException = false;
  } finally {
    if (hadException) {
      delete Module._cache[filename];
    }
  }

  // 第五步：输出模块的exports属性
  return module.exports;
};

```





## `__dirname,__filename` 哪里来的

```js
(function (exports, require, module, __filename, __dirname) {
  // 模块源码
  // 假如模块代码如下
  var math = require('math');
  exports.area = function(radius){
      return Math.PI * radius * radius
  }
});

```

module里面都会传入__filename, __dirname参数，这两个参数并不是module本身就有的，是外界传入的





## exports 和 module.exports

```js
// hello.js

function hello() {
    console.log('Hello, world!');
}

function greet(name) {
    console.log('Hello, ' + name + '!');
}

function hello() {
    console.log('Hello, world!');
}


exports.hello = hello;
exports.greet = greet;
但是你不可以直接对exports赋值：

// 代码可以执行，但是模块并没有输出任何变量:
exports = {
    hello: hello,
    greet: greet
};

```

首先，Node会把整个待加载的hello.js文件放入一个包装函数load中执行。在执行这个load()函数前，Node准备好了module变量： 

```js
var module = {
    id: 'hello',
    exports: {}
};
```

load()函数最终返回module.exports

```js
var load = function (exports, module) {
    // hello.js的文件内容
    ...
    // load函数返回:
    return module.exports;
};

```

```js
var exportes = load(module.exports, module);
```

也就是说，默认情况下，Node准备的exports变量和module.exports变量实际上是同一个变量，并且初始化为空对象{}



如果我们要输出的是一个函数或数组，那么，只能给module.exports赋值

```js
module.exports = function () { return 'foo'; };
```

给exports赋值是无效的，因为赋值后，module.exports仍然是空对象{}。







## 单线程架构模型

优势：

- 单线程就一个线程在玩，省去了线程间切换的开销
- 还有线程同步的问题，线程冲突的问题的也不需要担心

劣势：

- 劣势也很明显，现在起步都是 4 核，单线程没法充分利用 cpu 的资源
- 单线程，一旦崩溃，应用就挂掉了，大家调试脚本也知道一旦执行过程报错了，本次调试就直接结束了
- 因为只能利用一个 cpu ，一旦 cpu 被某个计算一直占用， cpu 得不到释放，后续的请求就会一直被挂起，直接无响应了

> 当然这些劣势都已经有成熟的解决方案了，使用 PM2 管理进程，或者上 K8S 也可以

##  **事件循环（重要）**

[https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick](https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick)

[https://learnku.com/articles/38802](https://learnku.com/articles/38802)



```
   ┌───────────────────────────┐
┌─>│           timers 计时器阶段 │  => 此阶段执行由 setTimeout 和 setInterval 回调排序执行
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  => 执行 I/O 回调推迟到下一个循环 迭代
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │   => 仅在内部使用
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll 轮询        │<─────┤  connections, │   // 负责处理I/O请求的阶段
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check 检查       │  =>  setImmediate 回调执行
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

* timers：此阶段执行由 setTimeout 和 setInterval 设置的回调。
* pending callbacks：执行推迟到下一个循环迭代的 I/O 回调。
* idle, prepare, ：仅在内部使用。
* poll：取出新完成的 I/O 事件；执行与 I/O 相关的回调（除了关闭回调，计时器调度的回调和 setImmediate 之外，几乎所有这些回调） 适当时，node 将在此处阻塞。
* check：在这里调用 setImmediate 回调。
* close callbacks：一些关闭回调，例如 socket.on('close', ...)。



#### **timers 计时器阶段**

计时器可以在回调后面指定时间阈值，但这不是我们希望其执行的确切时间。 计时器回调将在经过指定的时间后尽早运行。 但是，操作系统调度或其他回调的运行可能会延迟它们。-- `执行的实际时间不确定`



#### **轮询 poll 阶段**

轮询阶段具有两个主要功能：

- 计算应该阻塞并 I/O 轮询的时间
- 处理轮询队列 (**poll** queue) 中的事件

为防止轮询 `poll` 阶段使事件循环陷入饥饿状态 (一直等待 `poll` 事件)，libuv 还具有一个硬最大值限制来停止轮询。

* 如果轮询队列 (poll queue) 不为空，则事件循环将遍历其回调队列，使其同步执行，直到队列用尽或达到与系统相关的硬限制为止 (到底是哪些硬限制？)。
* 如果轮询队列为空，则会发生以下两种情况之一：
  * 如果已通过 setImmediate 调度了脚本，则事件循环将结束轮询 poll 阶段，并继续执行 check 阶段以执行那些调度的脚本。
  * 如果脚本并没有 setImmediate 设置回调，则事件循环将等待 poll 队列中的回调，然后立即执行它们。

一旦轮询队列 (`poll queue`) 为空，事件循环将检查哪些计时器 `timer` 已经到时间。 如果一个或多个计时器 `timer` 准备就绪，则事件循环将返回到计时器阶段，以执行这些计时器的回调。



##  setImmediate、setTimeout 和 process.nextTick

它们之间的主要区别在于执行时机。

1. process.nextTick：
   - **在当前执行阶段结束后立即执行。   （同一个阶段）**
   - process.nextTick() 方法将回调函数放在当前执行栈的末尾，用于推迟执行任务。这意味着回调函数将在当前操作结束之后立即执行。
   - process.nextTick 回调函数排队执行的优先级会高于其他回调函数，包括 I/O 操作和定时器。
   - process.nextTick 适用于需要尽早执行的回调函数，比如需要尽快更新某些状态。
   - process.nextTick() 从技术上讲不是事件循环的一部分。相反，它都将在当前操作完成后处理nextTickQueue
2. setImmediate：
   - **timer 阶段 或者 check 阶段执行。**
   - setImmediate 回调函数排在其他 I/O 操作之后。
   - setImmediate 实际上是一个在事件循环的单独阶段运行的特殊计时器
3. setTimeout：
   - **在指定的延迟时间后执行，即在事件循环的定时器阶段执行。**
   - setTimeout 的执行会在 I/O 操作之后进行。
   - setTimeout 适用于需要在一段时间后执行的回调函数。

综上所述，setImmediate 和 process.nextTick 的执行会优先于 setTimeout，而它们之间的区别在于在事件循环的哪个阶段执行。







**在一个异步流程**里，setImmediate一定会比setTimeout先执行

举个例子：

```tsx
console.log('outer');

setTimeout(() => {
  setTimeout(() => {
    console.log('setTimeout');
  }, 0);
  setImmediate(() => {
    console.log('setImmediate');
  });
}, 0);


// outer
// setImmediate
// setTimeout
```

流程分析

> 1. 外层是一个`setTimeout`，所以执行他的回调的时候已经在`timers`阶段了
> 2. 处理里面的`setTimeout`，因为本次循环的`timers`正在执行，所以他的回调其实加到了下个`timers`阶段
> 3. 处理里面的`setImmediate`，将它的回调加入`check`阶段的队列
> 4. 外层`timers`阶段执行完，进入`pending callbacks`，`idle, prepare`，`poll`，这几个队列都是空的，所以继续往下
> 5. 到了`check`阶段，发现了`setImmediate`的回调，拿出来执行
> 6. 然后是`close callbacks`，队列是空的，跳过
> 7. 又是`timers`阶段，执行我们的`console`



如果 console.log('setTimeout') 和 console.log('setImmediate') 都写在外面

```tsx
console.log('outer');

setTimeout(() => {
  console.log('setTimeout');
}, 0);

setImmediate(() => {
  console.log('setImmediate');
});

// 输出顺序是不确定的
```

setTimeout(fn, 0) 会被强制改为 setTimeout(fn, 1)

[https://nodejs.org/api/timers.html#timers_settimeout_callback_delay_args](https://nodejs.org/api/timers.html#timers_settimeout_callback_delay_args)

When `delay` is larger than `2147483647` or less than `1`, the `delay` will be set to `1`. Non-integer delays are truncated to an integer.

> 1. 外层同步代码一次性全部执行完，遇到异步API就塞到对应的阶段
> 2. 遇到`setTimeout`，虽然设置的是0毫秒触发，但是被node.js强制改为1毫秒，塞入`times`阶段
> 3. 遇到`setImmediate`塞入`check`阶段
> 4. 同步代码执行完毕，进入Event Loop
> 5. 先进入`times`阶段，检查当前时间过去了1毫秒没有，如果过了1毫秒，满足`setTimeout`条件，执行回调，如果没过1毫秒，跳过
> 6. 跳过空的阶段，进入check阶段，执行`setImmediate`回调







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



## 文件读取最大值

**在读取大文件时，会有读取文件大小的限制 (最大2GB)**

```js
const readFileTest = async () => {
    let data = await fs.promises.readFile("./video.mp4")
    console.log(data)
}


// RangeError [ERR_FS_FILE_TOO_LARGE]: File size (5699669796) is greater than 2 GB
```

解决：

通过Steam的方式读取

```js
const fs = require('fs')
const readFileTest = () => {
    var data = ''
    var rs = fs.createReadStream('./video.mp4');
    rs.on('data', function(chunk) {
        data += chunk;
     });
    rs.on('end',function(){
        console.log(data);
    });
    rs.on('error', function(err){
        console.log(err.stack);
     });
}
readFileTest()

// RangeError: Invalid string length
```

此时是因为data的长度超过了最大限制，比如2048M等。因此在用Steam处理的时候，在对读取结果的保存时，要注意文件的大小，千万不能超过默认的Buffer的最大值。上述这种情况，我们不用data += chunk将数据全部保存在一个大的data中，我们可以边读取边处理。





# 源码

nodejs源码有两个重要的目录：

1. `lib`: 包含了所有nodejs函数和模块的javascript实现，这些实现都是可以直接在你js项目中引用进去的
2. `src`: 包含了所有函数的C++版本实现，这里的代码才会真正引用Libuv和V8。





