---
layout:     post
title:      "node相关"
date:       2017-07-27 15:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Node
---

> “Yeah It's on. ”



[nodejs 面试篇](https://mp.weixin.qq.com/s?__biz=MzUxOTEzNjEwMQ==&mid=2247483676&idx=1&sn=10d2a069b52833eb324d4adbdab27a05&chksm=f9ff7716ce88fe00871f96dc55936a9b6c00a4ac5c727c687dc6cbc0b06bb83db5101fb97051&scene=21#wechat_redirect)





## 科普

> nodejs提供那么多模块，以及能在各个平台上跑的飞起，不是因为js很牛逼，而是因为底层依赖了一些你不知道的技术。最大的两个依赖便是**v8和libuv**

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 

它是单线程单进程模式，nodeJS的单线程指js的引擎只有一个实列。且是在主线程执行的，这样的。

```text
Nodejs封装了所有与底层交流的信息，给开发者提供一致的接口定义。在不断升级v8和libuv的同时，依然能够做到接
口的一致性，这个就是nodejs想要实现的目标。

v8：一个帮助你将js代码转变成可以在各个平台和机器上运行的机器码
libuv：帮助你调用平台和机器上各种系统特性，包括操作文件、监听socket等等
```



### libuv

[https://luohaha.github.io/Chinese-uvbook/source/introduction.html](https://luohaha.github.io/Chinese-uvbook/source/introduction.html)

libuv是一个高性能的，事件驱动的I/O库，并且提供了跨平台（如windows, linux）的API。







### 模块

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




### 进程

进程Process是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础，进程是线程的容器。

Node.js 中的进程 Process 是一个全局对象，无需 require 直接使用

进程是资源分配的最小单位。我们启动一个服务、运行一个实例，就是开一个服务进程，例如 Java 里的 JVM 本身就是一个进程，Node.js 里通过 node app.js 开启一个服务进程，多进程就是进程的复制（fork），fork 出来的每个进程都拥有自己的独立空间地址、数据栈，一个进程无法访问另外一个进程里定义的变量、数据结构，只有建立了 IPC 通信，进程之间才可数据共享。

>在 Web 服务器方面，著名的 Nginx 也是采用此模式（事件驱动），避免了多线程的线程创建、线程上下文切换的开销，Nginx 采用 C 语言进行编写，主要用来做高性能的 Web 服务器，不适合做业务。


在单核 CPU 系统之上我们采用 单进程 + 单线程 的模式来开发。在多核 CPU 系统之上，可以通过 child_process.fork 开启多个进程（Node.js 在 v0.8 版本之后新增了Cluster 来实现多进程架构） ，即 多进程 + 单线程 模式。注意：开启多进程不是为了解决高并发，主要是解决了单进程模式下 Node.js CPU 利用率不足的情况，充分利用多核 CPU 的性能。

* process.env：环境变量，例如通过  process.env.NODE_ENV 获取不同环境项目配置信息
* process.nextTick：这个在谈及 Event Loop 时经常为会提到
* process.pid：获取当前进程id
* process.ppid：当前进程对应的父进程
* process.cwd()：获取当前进程工作目录，
* process.platform：获取当前进程运行的操作系统平台
* process.uptime()：当前进程已运行时间，例如：pm2 守护进程的 uptime 值

**child_process模块**


* child_process.spawn()：适用于返回大量数据，例如图像处理，二进制数据处理。
* child_process.exec()：适用于小量数据，maxBuffer 默认值为 200 * 1024 超出这个默认值将会导致程序崩溃，数据量过大可采用 spawn。
* child_process.execFile()：类似 child_process.exec()，区别是不能通过 shell 来执行，不支持像 I/O 重定向和文件查找这样的行为
* child_process.fork()： 衍生新的进程，进程之间是相互独立的，每个进程都有自己的 V8 实例、内存，系统资源是有限的，不建议衍生太多的子进程出来，通长根据系统** CPU 核心数**设置。


>CPU 核心数这里特别说明下，fork 确实可以开启多个进程，但是并不建议衍生出来太多的进程，cpu核心数的获取方式const cpus = require('os').cpus();,这里 cpus 返回一个对象数组，包含所安装的每个 CPU/内核的信息，二者总和的数组哦。假设主机装有两个cpu，每个cpu有4个核，那么总核数就是8。


#### cluster原理分析

cluster模块调用fork方法来创建子进程，该方法与child_process中的fork是同一个方法。
cluster模块采用的是经典的主从模型，Cluster会创建一个master，然后根据你指定的数量复制出多个子进程，可以使用cluster.isMaster属性判断当前进程是master还是worker(工作进程)。由master进程来管理所有的子进程，主进程不负责具体的任务处理，主要工作是负责调度和管理。



cluster模块使用内置的负载均衡来更好地处理线程之间的压力，该负载均衡使用了Round-robin算法（也被称之为循环算法）。当使用Round-robin调度策略时，master accepts()所有传入的连接请求，然后将相应的TCP请求处理发送给选中的工作进程（该方式仍然通过IPC来进行通信）。

----

**开启多进程时候端口疑问讲解：如果多个Node进程监听同一个端口时会出现 Error:listen EADDRIUNS的错误，而cluster模块为什么可以让多个子进程监听同一个端口呢?**



原因是master进程内部启动了一个TCP服务器，而真正监听端口的只有这个服务器，当来自前端的请求触发服务器的connection事件后，master会将对应的socket具柄发送给子进程。





#### child_process

[https://nodejs.org/docs/latest/api/child_process.html](https://nodejs.org/docs/latest/api/child_process.html)

child_process模块是Node.js中用于创建子进程的模块。它提供了多个函数来创建子进程，主要有spawn、fork和exec。

spawn：spawn函数用于创建一个新的进程，并通过标准输入输出流来进行通信。它可以执行任意可执行文件。(适合 run dev)

- 优点：可以实现并行执行多个子进程任务，并且可以与子进程进行双向通信。
- 缺点：不能执行shell命令，而且无法获取shell命令的返回结果。

fork：fork函数是基于spawn函数的一种特殊形式，主要用于创建一个与当前进程相同的副本。它可以通过进程间的IPC通道来实现主从通信。

- 优点：可以在子进程和主进程之间进行双向通信，并且可以实现主进程和子进程之间的共享内存。
- 缺点：不能执行shell命令，而且无法获取shell命令的返回结果。

exec：exec函数用于执行一个shell命令。它会创建一个新的shell进程，并运行指定的命令，然后返回命令的执行结果。

- 优点：可以执行shell命令，并且可以获取到命令的返回结果。
- 缺点：无法实现主从通信，一次只能执行一个命令，无法并行执行多个命令。

综上所述，spawn适合用于执行非shell命令且需要双向通信的场景，fork适合用于创建与当前进程相同副本的场景，exec适合用于执行shell命令并获取结果的场景。





#### child_process 与cluster 

无论是 child_process 模块还是 cluster 模块，为了解决 Node.js 实例单线程运行，无法利用多核 CPU 的问题而出现的。核心就是**父进程（即 master 进程）负责监听端口，接收到新的请求后将其分发给下面的 worker 进程。**

cluster内部隐时的构建TCP服务器的方式来说对使用者确实简单和透明了很多，但是这种方式无法像使用child_process那样灵活，因为一直主进程只能管理一组相同的工作进程，而自行通过child_process来创建工作进程，一个主进程可以控制多组进程。原因是child_process操作子进程时，可以隐式的创建多个TCP服务器



#### 进程通信IPC原理

[https://www.jianshu.com/p/c1015f5ffa74](https://www.jianshu.com/p/c1015f5ffa74)


前面讲解的无论是child_process模块，还是cluster模块，都需要主进程和工作进程之间的通信。通过fork()或者其他API，创建了子进程之后，为了实现父子进程之间的通信，父子进程之间才能通过message和send()传递信息。

IPC这个词我想大家并不陌生，不管那一张开发语言只要提到进程通信，都会提到它。IPC的全称是Inter-Process Communication,即进程间通信。它的目的是为了让不同的进程能够互相访问资源并进行协调工作。实现进程间通信的技术有很多，如命名管道，匿名管道，socket，信号量，共享内存，消息队列等。Node中实现IPC通道是依赖于libuv。

windows下由命名管道(name pipe)实现，linux系统则采用Unix Domain Socket实现。表现在应用层上的进程间通信只有简单的message事件和send()方法，接口十分简洁和消息化。




### 线程


线程是操作系统能够进行运算调度的最小单位，首先我们要清楚线程是隶属于进程的，被包含于进程之中。**一个线程只能隶属于一个进程，但是一个进程是可以拥有多个线程的。**



#### 单线程


**单线程就是一个进程只开一个线程**


Javascript 就是属于单线程，程序顺序执行(这里暂且不提JS异步)，可以想象一下队列，前面一个执行完之后，后面才可以执行，当你在使用单线程语言编码时切勿有过多耗时的同步操作，否则线程会造成阻塞，导致后续响应无法处理。你如果采用 Javascript 进行编码时候，请尽可能的利用Javascript异步操作的特性。

优势：

- 单线程就一个线程在玩，省去了线程间切换的开销
- 还有线程同步的问题，线程冲突的问题的也不需要担心

劣势：

- 劣势也很明显，现在起步都是 4 核，单线程没法充分利用 cpu 的资源
- 单线程，一旦崩溃，应用就挂掉了，大家调试脚本也知道一旦执行过程报错了，本次调试就直接结束了
- 因为只能利用一个 cpu ，一旦 cpu 被某个计算一直占用， cpu 得不到释放，后续的请求就会一直被挂起，直接无响应了

> 当然这些劣势都已经有成熟的解决方案了，使用 PM2 管理进程，或者上 K8S 也可以

----

单线程的一些说明

* 当你的项目中需要有大量计算，CPU 耗时的操作时候，要注意考虑开启多进程来完成了。
* Node.js 开发过程中，错误会引起整个应用退出，应用的健壮性值得考验，尤其是错误的异常抛出，以及进程守护是必须要做的。
* 单线程无法利用多核CPU，但是后来Node.js 提供的API以及一些第三方工具相应都得到了解决



## 基础

[https://juejin.im/post/5d43017be51d4561f40adcf9#heading-5](https://juejin.im/post/5d43017be51d4561f40adcf9#heading-5)





[网页链接](http://www.runoob.com/nodejs/nodejs-tutorial.html)

简单的说 Node.js 就是运行在服务端的 JavaScript。
Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。
Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。


### REPL(交互式解释器)

Node.js REPL(Read Eval Print Loop:交互式解释器) 表示一个电脑的环境，类似 Window 系统的终端或 Unix/Linux shell，我们可以在终端中输入命令，并接收系统的响应。

Node 自带了交互式解释器，可以执行以下任务：

* 读取 - 读取用户输入，解析输入了Javascript 数据结构并存储在内存中。
* 执行 - 执行输入的数据结构
* 打印 - 输出结果
* 循环 - 循环操作以上步骤直到用户两次按下 ctrl-c 按钮退出。

Node 的交互式解释器可以很好的调试 Javascript 代码。



### require 加载机制

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



### `__dirname和__filename`

## 

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



### exports 和 module.exports

## 

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











### 事件循环

[https://juejin.cn/post/6844904007270563848](https://juejin.cn/post/6844904007270563848)

[https://juejin.cn/post/6844903761949753352](https://juejin.cn/post/6844903761949753352)

[https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick](https://nodejs.org/zh-cn/docs/guides/event-loop-timers-and-nexttick)

[https://learnku.com/articles/38802](https://learnku.com/articles/38802)



```
   ┌───────────────────────────┐
┌─>│      timers 计时器阶段      │  => 此阶段执行由 setTimeout 和 setInterval 回调排序执行
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

**在每次执行完一个阶段的任务之后，会检查microtask队列是否为空。如果不为空，会立即执行队列中的任务，直到队列为空，然后再进行下一个阶段的任务。这样，可以确保microtask的执行可以尽可能快地响应，并在主线程空闲时执行。**

1. 在每个宏任务执行完毕后，会执行所有排队的 microtask。也就是说，在一个宏任务结束时，会将其中产生的所有 microtask 先执行完毕，然后再进行下一个宏任务的执行。
2. 一个宏任务可以被分成多个阶段，比如 I/O 操作、定时器等。在每个阶段的末尾，会执行排队的 microtask。
3. microtask 的执行是连续的，直到所有的 microtask 都被执行完毕，或者达到了某个限制（比如嵌套过深），才会继续执行下一个宏任务

**总结起来，microtask 的执行时机主要是在每个宏任务执行结束后，以及每个阶段的末尾。这样可以确保 microtask 在下一个宏任务执行之前都被执行完毕，从而保证了它们的执行顺序和响应性能。**





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



#### **timers 计时器阶段**

计时器可以在回调后面指定时间阈值，但这不是我们希望其执行的确切时间。 计时器回调将在经过指定的时间后尽早运行。 但是，操作系统调度或其他回调的运行可能会延迟它们。-- `执行的实际时间不确定`



#### setImmediate、setTimeout 和 process.nextTick

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





### 监听文件 watch

[如何利用 Nodejs 监听文件夹](https://github.com/ascoders/weekly/blob/master/%E5%89%8D%E6%B2%BF%E6%8A%80%E6%9C%AF/59.%E7%B2%BE%E8%AF%BB%E3%80%8A%E5%A6%82%E4%BD%95%E5%88%A9%E7%94%A8%20Nodejs%20%E7%9B%91%E5%90%AC%E6%96%87%E4%BB%B6%E5%A4%B9%E3%80%8B.md)

利用 nodejs 监听文件夹变化很容易，但提供准确的回调却很难，主要难在两点：

1. 抹平操作系统间的差异，这需要在结合 `fs.watch` 的同时，增加一些额外校验机制与延时机制。
2. 分清楚操作系统预期与用户预期，比如编辑器的额外操作、操作系统的多次读写都应该被忽略，用户的预期不会那么频繁，会忽略极小时间段内的连续触发。

另外还有兼容性、权限、软连接等其他因素要考虑，`fs.watch` 并不是一个开箱可用的工程级别 api。



### EventEmitter

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





### Buffer(缓冲区)

JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。

但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。

在 Node.js 中，Buffer 类是随 Node 内核一起发布的核心库。Buffer 库为 Node.js 带来了一种存储原始数据的方法，可以让 Node.js 处理二进制数据，每当需要在 Node.js 中处理I/O操作中移动的数据时，就有可能使用 Buffer 库。原始数据存储在 Buffer 类的实例中。一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。

>在v6.0之前创建Buffer对象直接使用new Buffer()构造函数来创建对象实例，但是Buffer对内存的权限操作相比很大，可以直接捕获一些敏感信息，所以在v6.0以后，官方文档里面建议使用 Buffer.from() 接口去创建Buffer对象。 

[Node.js Buffer](http://www.runoob.com/nodejs/nodejs-buffer.html)

Node.js 目前支持的字符编码包括：
* ascii - 仅支持 7 位 ASCII 数据。如果设置去掉高位的话，这种编码是非常快的。
* utf8 - 多字节编码的 Unicode 字符。许多网页和其他文档格式都使用 UTF-8 。
* utf16le - 2 或 4 个字节，小字节序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF）。
* ucs2 - utf16le 的别名。
* base64 - Base64 编码。
* latin1 - 一种把 Buffer 编码成一字节编码的字符串的方式。
* binary - latin1 的别名。
* hex - 将每个字节编码为两个十六进制字符。

#### 写入缓冲区
写入 Node 缓冲区的语法如下所示：
>buf.write(string[, offset[, length]][, encoding])

* string - 写入缓冲区的字符串。
* offset - 缓冲区开始写入的索引值，默认为 0 。
* length - 写入的字节数，默认为 buffer.length
* encoding - 使用的编码。默认为 'utf8' 。

根据 encoding 的字符编码写入 string 到 buf 中的 offset 位置。 length 参数是写入的字节数。 如果 buf 没有足够的空间保存整个字符串，则只会写入 string 的一部分。 只部分解码的字符不会被写入。

**返回值**
返回实际写入的大小。如果 buffer 空间不足， 则只会写入部分字符串。

```
buf = Buffer.alloc(256);
len = buf.write("www.runoob.com");

console.log("写入字节数 : "+  len);
```
执行以上代码，输出结果为：
```
$node main.js
写入字节数 : 14
```

#### 从缓冲区读取数据
读取 Node 缓冲区数据的语法如下所示：
>buf.toString([encoding[, start[, end]]])

* encoding - 使用的编码。默认为 'utf8' 。
* start - 指定开始读取的索引位置，默认为 0。
* end - 结束位置，默认为缓冲区的末尾。

#### 缓冲区合并
Node 缓冲区合并的语法如下所示：
>Buffer.concat(list[, totalLength])

* list - 用于合并的 Buffer 对象数组列表。
* totalLength - 指定合并后Buffer对象的总长度。

```
var buffer1 = Buffer.from(('菜鸟教程'));
var buffer2 = Buffer.from(('www.runoob.com'));
var buffer3 = Buffer.concat([buffer1,buffer2]);
console.log("buffer3 内容: " + buffer3.toString());
```

#### 缓冲区比较
>buf.compare(otherBuffer);

otherBuffer - 与 buf 对象比较的另外一个 Buffer 对象。 

**返回值**
返回一个数字，表示 buf 在 otherBuffer 之前，之后或相同。

```
var buffer1 = Buffer.from('ABC');
var buffer2 = Buffer.from('ABCD');
var result = buffer1.compare(buffer2);

if(result < 0) {
   console.log(buffer1 + " 在 " + buffer2 + "之前");
}else if(result == 0){
   console.log(buffer1 + " 与 " + buffer2 + "相同");
}else {
   console.log(buffer1 + " 在 " + buffer2 + "之后");
}
```

#### 拷贝缓冲区
Node 缓冲区拷贝语法如下所示：
>buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])

* targetBuffer - 要拷贝的 Buffer 对象。
* targetStart - 数字, 可选, 默认: 0
* sourceStart - 数字, 可选, 默认: 0
* sourceEnd - 数字, 可选, 默认: buffer.length

```
var buf1 = Buffer.from('abcdefghijkl');
var buf2 = Buffer.from('RUNOOB');

//将 buf2 插入到 buf1 指定位置上
buf2.copy(buf1, 2);

console.log(buf1.toString());
```
执行以上代码，输出结果为：
>abRUNOOBijkl

### Stream(流)
 Stream 是一个抽象接口，Node 中有很多对象实现了这个接口。例如，对http 服务器发起请求的request 对象就是一个 Stream，还有stdout（标准输出）。

Node.js，Stream 有四种流类型：
* Readable - 可读操作。
* Writable - 可写操作。
* Duplex - 可读可写操作.
* Transform - 操作被写入数据，然后读出结果。

所有的 Stream 对象都是 EventEmitter 的实例。常用的事件有：
* data - 当有数据可读时触发。
* end - 没有更多的数据可读时触发。
* error - 在接收和写入过程中发生错误时触发。
* finish - 所有数据已被写入到底层系统时触发。

#### 管道流
管道提供了一个输出流到输入流的机制。通常我们用于从一个流中获取数据并将数据传递到另外一个流中。我们用一根管子(pipe)连接两个桶使得水从一个桶流入另一个桶，这样就慢慢的实现了大文件的复制过程。 

#### 链式流
链式是通过连接输出流到另外一个流并创建多个流操作链的机制。链式流一般用于管道操作。

接下来我们就是用管道和链式来压缩和解压文件。
```
var fs = require("fs");
var zlib = require('zlib');

// 压缩 input.txt 文件为 input.txt.gz
fs.createReadStream('input.txt')
  .pipe(zlib.createGzip())
  .pipe(fs.createWriteStream('input.txt.gz'));
  
console.log("文件压缩完成。");
```





### process



#### exit

[https://stackoverflow.com/questions/43147330/what-is-difference-between-method-process-exit1-and-process-exit0-in-node-js](https://stackoverflow.com/questions/43147330/what-is-difference-between-method-process-exit1-and-process-exit0-in-node-js)

process.exit(1)  and process.exit(0) 的区别

Node normally exits with a 0 status code （正常情况） when no more async operations are pending. There are other exit codes which are described below:

> `0` is a success code and `1` (or another number) can be a failure code



### 环境变量

[https://github.com/frmachao/frmachao.github.io/issues/4](https://github.com/frmachao/frmachao.github.io/issues/4)

错误做法：

```tsx
cross-env NODE_ENV=production && npm run clean && webpack
```

此类的设置不能使环境变量生效。原因是 && 符号把命令语句分离成了多个，每一个命令执行的环境都是隔离的，而 cross-env 无法作用于其他环境的命令。

正确做法：

```tsx
cross-env NODE_ENV=production npm run clean
```





### node:module 语法

[https://savokiss.com/tech/nodejs-module-syntax.html](https://savokiss.com/tech/nodejs-module-syntax.html)

```bash
import fs from 'node:fs/promises';
```







### 模块的缓存

**这是一个非常重要的知识点**

* 第一次加载某个缓存时，Node会缓存该模块，以后再加载该模块，就直接从缓存取出该模块的module.exports属性(不会再执行该模块)
* 如果需要多次执行模块中的代码，一般可以让模块暴露行为(函数)
* 模块的缓存可以通过require.cache拿到，同样也可以删除

什么时候需要清除缓存？

由于可以加载json数据，所以每次数据更新时，需要同步更新json数据，此时就需要清除缓存，一般都是对数据做清除缓存操作

 如果不需要缓存，可以将暴漏的属性改为暴露方法(行为)，因为暴露的方法在每一次加载的时候都会执行，从而不会进行缓存。

```javascript
function requireNoCache(filePath) {
  try {
    const content = require(filePath);
    delete require.cache[require.resolve(filePath)];
    return content;
  } catch (e) {
    return null;
  }
}
```

>require.resolve 相当于把相对路径转化成绝对路径，避免了自己手写的绝对路径跟cache里的key不一致的问题


[为什么我反对清除node.js中require的cache？
](http://www.yuezhihan.com/post/e10a1b34.html)

#### require.resolve


在Node.js中，可以使用require.resolve函数来查询某个模块文件的带有完整绝对路径的文件名

```javascript
require.resolve('./testModule.js');
```

在这行代码中，我们使用require.resolve函数来查询当前目录下testModule.js模块文件的带有完整绝对路径的模块文件名。


**注意：使用require.resolve函数查询模块文件名时并不会加载该模块。**






### 服务端的模块
也许你已经注意到，我们已经在代码中使用了模块了
```
var http = require("http");
...
http.createServer(...);
```

Node.js 中自带了一个叫做http的模块，我们在我们的代码中请求它并把返回值赋给一个本地变量。这把我们的本地变量变成了一个拥有所有 http 模块所提供的公共方法的对象。

### Node.js 路由
我们要为路由提供请求的 URL 和其他需要的 GET 及 POST 参数，随后路由需要根据这些数据来执行相应的代码。

因此，我们需要查看 HTTP 请求，从中提取出请求的 URL 以及 GET/POST 参数。这一功能应当属于路由还是服务器（甚至作为一个模块自身的功能）确实值得探讨，但这里暂定其为我们的HTTP服务器的功能。

我们需要的所有数据都会包含在 request 对象中，该对象作为 onRequest() 回调函数的第一个参数传递。但是为了解析这些数据，我们需要额外的 Node.JS 模块，它们分别是 url 和 querystring 模块。 
```
                          url.parse(string).query
                                           |
           url.parse(string).pathname      |
                       |                   |
                       |                   |
                     ------ -------------------
http://localhost:8888/start?foo=bar&hello=world
                                ---       -----
                                 |          |
                                 |          |
              querystring.parse(queryString)["foo"]    |
                                            |
                         querystring.parse(queryString)["hello"]
```



### **查看平台和架构**

有的时候我们需要确保当前node使用的是x86架构

使用命令

```bash
node -p "process"
```

输出

```bash
process {
  version: 'v16.6.1',
  versions: {
    node: '16.6.1',
    v8: '9.2.230.21-node.19',
    uv: '1.42.0',
    zlib: '1.2.11',
    brotli: '1.0.9',
    ares: '1.17.1',
    modules: '93',
    nghttp2: '1.44.0',
    napi: '8',
    llhttp: '6.0.2',
    openssl: '1.1.1k',
    cldr: '39.0',
    icu: '69.1',
    tz: '2021a',
    unicode: '13.0'
  },
  arch: 'x64',  // 架构
  platform: 'darwin',  // 平台
  release: {
    name: 'node',
    sourceUrl: 'https://nodejs.org/download/release/v16.6.1/node-v16.6.1.tar.gz',
    headersUrl: 'https://nodejs.org/download/release/v16.6.1/node-v16.6.1-headers.tar.gz'
  },
  ....
}
```







### 全局对象
JavaScript 中有一个特殊的对象，称为全局对象（Global Object），它及其所有属性都可以在程序的任何地方访问，即全局变量。

在浏览器 JavaScript 中，通常 window 是全局对象， 而 Node.js 中的全局对象是 global，所有全局变量（除了 global 本身以外）都是 global 对象的属性。

在 Node.js 我们可以直接访问到 global 的属性，而不需要在应用中包含它。

#### 全局对象与全局变量
global 最根本的作用是作为全局变量的宿主。按照 ECMAScript 的定义，满足以下条 件的变量是全局变量：
* 在最外层定义的变量；
* 全局对象的属性；
* 隐式定义的变量（未定义直接赋值的变量）。

#### __filename

__filename 表示当前正在执行的脚本的文件名。它将输出文件所在位置的绝对路径，且和命令行参数所指定的文件名不一定相同。 如果在模块中，返回的值是模块文件的路径。 

----------


__dirname 与 process.cwd() 的区别


* cwd() 是当前执行node命令时候的文件夹地址 
* __dirname 是被执行的js 文件的地址




#### process
 process 是一个全局变量，即 global 对象的属性。

它用于描述当前Node.js 进程状态的对象，提供了一个与操作系统的简单接口。通常在你写本地命令行程序的时候，少不了要 和它打交道。下面将会介绍 process 对象的一些最常用的成员方法。

1. exit
   当进程准备退出时触发。
2. beforeExit
当 node 清空事件循环，并且没有其他安排时触发这个事件。通常来说，当没有进程安排时 node 退出，但是 'beforeExit' 的监听器可以异步调用，这样 node 就会继续执行。
3. uncaughtException
当一个异常冒泡回到事件循环，触发这个事件。如果给异常添加了监视器，默认的操作（打印堆栈跟踪信息并退出）就不会发生。
4. Signal 事件
当进程接收到信号时就触发。信号列表详见标准的 POSIX 信号名，如 SIGINT、SIGUSR1 等。



**process.argv**


process.argv返回命令行脚本的各个参数组成的数组

```
// print process.argv
process.argv.forEach(function(val, index, array) {
  console.log(index + ': ' + val);
});
$ node process.js one two=three four
0: node
1: /Users/node/process.js
2: one
3: two=three
4: four
```


可以看出，会将bat命令以空格为标记，拆成一个数组。索引0是node命令，索引1是运行路径。索引2开始就是参数了。



这个用处挺多的。例如你在bat的文件这样写。node app 127.0.0.1 7001 这时候通过.argv [2] 得到IP，argv[3]得到端口，更方便我们修改配置。

----------


再看个例子：

```javascript
/**
 * 通过process.argv读取命令行参数，其构造如下
 * 1.process.argv返回为是一个数组形式
 * 2.process.argv[0] 当前命令,一般为node，因为通过node执行
 * 3.process.argv[1] 当前命令执行的脚本完整路径
 * 4.process.argv[2,n] 命令参数
 */
//这里要实现通过命令行传参数进行求和的算法
console.log("1.当前执行的命令为：",process.argv[0]);
console.log("2.执行的脚本地址为：",process.argv[1]);
//这里截取参数数组中有效的参数列表
var params = process.argv.slice(2);
console.log("3.执行的命令参数为：",params);
var result = 0;
for(var i = 0;i<params.length;i++){
    //由于传入的参数都是字符串，所以要通过parseFloat进行转换
    var v = parseFloat(params[i]);
    //在转换后的值，可能存在非法的非数值的结果，
    //所以通过isNaN进行判断是否为非数值，如果为非数值，则跳过
    if(isNaN(v)) continue;
    result += v;
}
console.log("4.计算后的有效值的和为：",result);
```


执行



```javascript
node process-argv.js 1 2 H 3 4
1.当前执行的命令为： node
2.执行的脚本地址为： /home/code/workspace/node/core/process-argv.js
3.执行的命令参数为： [ '1', '2', 'H', '3', '4' ]
4.计算后的有效值的和为： 10
```












## util
util 是一个Node.js 核心模块，提供常用函数的集合，用于弥补核心JavaScript 的功能 过于精简的不足。 

#### util.inherits
util.inherits(constructor, superConstructor)是一个实现对象间原型继承 的函数。 

JavaScript 的面向对象特性是基于原型的，与常见的基于类的不同。JavaScript 没有 提供对象继承的语言级别特性，而是通过原型复制来实现的。

```js
var util = require('util'); 
function Base() { 
    this.name = 'base'; 
    this.base = 1991; 
    this.sayHello = function() { 
    console.log('Hello ' + this.name); 
    }; 
} 
Base.prototype.showName = function() { 
    console.log(this.name);
}; 
function Sub() { 
    this.name = 'sub'; 
} 
util.inherits(Sub, Base); 
var objBase = new Base(); 
objBase.showName(); 
objBase.sayHello(); 
console.log(objBase); 
var objSub = new Sub(); 
objSub.showName(); 
//objSub.sayHello(); 
console.log(objSub); 
```
我们定义了一个基础对象Base 和一个继承自Base 的Sub。运行结果如下：
```js
base 
Hello base 
{ name: 'base', base: 1991, sayHello: [Function] } 
sub 
{ name: 'sub' }
```

**注意：Sub 仅仅继承了Base 在原型中定义的函数，而构造函数内部创造的 base 属 性和 sayHello 函数都没有被 Sub 继承。**

#### util.inspect
util.inspect(object,[showHidden],[depth],[colors])是一个将任意对象转换 为字符串的方法，通常用于调试和错误输出。它至少接受一个参数 object，即要转换的对象。

showHidden 是一个可选参数，如果值为 true，将会输出更多隐藏信息。

depth 表示最大递归的层数，如果对象很复杂，你可以指定层数以控制输出信息的多 少。如果不指定depth，默认会递归2层，指定为 null 表示将不限递归层数完整遍历对象。 如果color 值为 true，输出格式将会以ANSI 颜色编码，通常用于在终端显示更漂亮 的效果。

特别要指出的是，util.inspect 并不会简单地直接把对象转换为字符串，即使该对 象定义了toString 方法也不会调用。



### utility

https://github.com/node-modules/utility



A collection of useful utilities.

(包括一些常用的加密方式)



# node调试

[http://www.ruanyifeng.com/blog/2018/03/node-debugger.html](http://www.ruanyifeng.com/blog/2018/03/node-debugger.html)



## Inspector

[https://nodejs.org/en/docs/guides/debugging-getting-started/](https://nodejs.org/en/docs/guides/debugging-getting-started/)

When started with the `--inspect` switch, a Node.js process listens for a debugging client. By default, it will listen at host and port 127.0.0.1:9229. Each process is also assigned a unique [UUID](https://tools.ietf.org/html/rfc4122).







# Express





# KOA

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

[深入理解 Koa2 中间件机制](https://juejin.im/post/5a5f5a126fb9a01cb0495b4c)



### koa-bodyparser

[ https://www.npmjs.com/package/koa-bodyparser ]( https://www.npmjs.com/package/koa-bodyparser )

To use `koa-bodyparser` with koa@1, please use [bodyparser 2.x](https://github.com/koajs/bodyparser/tree/2.x).

```
npm install koa-bodyparser@2 --save
```

> **Notice: this module don't support parsing multipart format data**, please use [co-busboy](https://github.com/cojs/busboy) to parse multipart format data.

**很重要的一点 koa-bodyparser 不支持 multipart/form-data**  



### 和Express中间件比较

- express 中间件：是通过 next 的机制，即上一个中间件会通过 next 触发下一个中间件   (express中间件处理方式是线性的，next过后继续寻找下一个中间件，当然如果没有调用next()的话，就不会调用下一个函数了，调用就会被终止)
- koa2 中间件：是通过 async await 实现的，中间件执行顺序是“洋葱圈”模型（推荐）



## 原理

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













## 中间件



### body-parser



[网页链接](https://www.npmjs.com/package/body-parser)

Node.js body parsing middleware.
Node.js正文解析中间件。


>一般用于post请求


This module provides the following parsers:

* **JSON body parser**
* Raw body parser
* Text body parser
* URL-encoded form body parser

该bodyParser对象公开了各种工厂来创建中间件。所有中间件都会req.body在Content-Type请求头匹配type选项时使用解析的主体来填充属性，{}如果没有要分析的主体，Content-Type则不匹配或发生错误，所有中间件都会使用解析的主体来填充属性。

#### bodyParser.json([选项]）

返回仅解析json并仅查看Content-Type标题与type选项匹配的请求的中间件。该解析器接受身体的任何Unicode编码并支持自动膨胀gzip和 deflate编码。

body包含分析数据的新对象request 在中间件（即req.body）之后被填充到对象上。

**类型**

该type选项用于确定中间件将解析的媒体类型。该选项可以是字符串，字符串数组或函数。如果不是函数，则type选项直接传递到 类型库，这可以是扩展名（例如json），MIME类型（如application/json）或具有通配符的MIME类型（如*/*或*/json）。如果一个函数，该type 选项被调用，fn(req)并且如果它返回一个真值，则该请求被解析。**默认为application/json。**





#### bodyParser.urlencoded([options])

返回仅分析urlencoded正文的中间件，并仅查看Content-Type标题与type选项匹配的请求。此解析器只接受身体的UTF-8编码，并支持自动通胀gzip和deflate编码。

body包含分析数据的新对象request 在中间件（即req.body）之后被填充到对象上。该对象将包含键-值对，其中该值可以是一个字符串或阵列（时extended是 false），或任何类型的（当extended是true）。

**类型**

该type选项用于确定中间件将解析的媒体类型。该选项可以是字符串，字符串数组或函数。如果不是函数，则将type选项直接传递到 类型库，这可以是扩展名（例如urlencoded），MIME类型（如 application/x-www-form-urlencoded）或具有通配符（如*/x-www-form-urlencoded）的MIME类型 。如果一个函数，该type选项被调用， fn(req)并且如果它返回一个真值，则该请求被解析。**默认为application/x-www-form-urlencoded。**





### Connect

[https://github.com/senchalabs/connect](https://github.com/senchalabs/connect)

Connect is an extensible HTTP server framework for [node](http://nodejs.org/) using "plugins" known as *middleware*.



# PM2

[https://hkc452.github.io/patronum-pm2/#/?id=patronum-pm2](https://hkc452.github.io/patronum-pm2/#/?id=patronum-pm2)

PM2的原理，用一句话来概括，就是我们通过rpc跟守护进程通信,让守护进程帮我们去创建、销毁、重启进程



## [如何和守护进程通信](https://hkc452.github.io/patronum-pm2/#/pm2/导言?id=如何和守护进程通信)

[https://hkc452.github.io/patronum-pm2/#/pm2/%E5%AF%BC%E8%A8%80?id=pm2-%e6%98%af%e6%80%8e%e4%b9%88%e5%81%9a%e7%9a%84](https://hkc452.github.io/patronum-pm2/#/pm2/%E5%AF%BC%E8%A8%80?id=pm2-%e6%98%af%e6%80%8e%e4%b9%88%e5%81%9a%e7%9a%84)





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





## fileURLToPath

```ts
import { fileURLToPath } from 'node:url';

const dir = fileURLToPath(new URL('.', import.meta.url));
```

在 ES 模块中，`import.meta.url` 提供了当前模块的完整 URL。  file://xxx

**`new URL('.', import.meta.url)`** 这行代码创建了一个新的 URL 对象，`'.'` 表示当前目录。

`fileURLToPath` 是 Node.js 的一个方法，它接受一个 URL 对象并返回对应的本地文件系统路径。

 file://xxx => 

## tsc 和 ts-node

[What's the difference between tsc (TypeScript compiler) and ts-node?](https://stackoverflow.com/questions/51448376/whats-the-difference-between-tsc-typescript-compiler-and-ts-node)

The main difference is that `tsc` transpile all the file **according to your tsconfig**.

Instead, `ts-node` will start from the entry file and transpile the file step by step through the tree **based on the import/export**.

Most common practice is that `tsc` is used for production build and `ts-node` for development purposes running in `--watch` mode along with `nodemon`.

## CLI

> CLI，全称是 command-line interface，也就是命令行界面。

前端脚手架 CLI，是一个命令行工具，它的出现主要解决这几个问题：

- 减少重复性工作
- 规范项目开发目录结构
- 统一团队统一开发风格，便于跨团队合作，以及后期维护，降低新人上手成本
- 提供一键前端项目的创建、配置、本地开发、插件扩展等功能，让开发者更多时间专注于业务

## 服务器部署

**node启动服务的时候只有监听内网IP才可以供外网访问**

### nodemon

For use during development of a node.js based application.

nodemon will watch the files in the directory in which nodemon was started, and if any files change, nodemon will automatically restart your node application.

nodemon **does not require any changes** to your code or method of development. nodemon simply wraps your node application and keeps an eye on any files that have changed. Remember that nodemon is a replacement wrapper for node, think of it as replacing the word "node" on the command line when you run your script.

### nodemon 结合 typescript

[nodemon实现Typescript项目热更新](https://juejin.cn/post/6844903999947145223)

最好的方案：

nodemon + ts-node

## 查看芯片arch架构

```bash
node -p process.arch
// x64
```

切换x64架构

```bash
arch -x86_64 zsh
```



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







## 杀掉进程

```bash
lsof -i :8001
// 查找对应端口的进程
```

```bash
kill xxx
// xxx 为pid
```

## Pure ESM package

[https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c](https://gist.github.com/sindresorhus/a39789f98801d908bbc7ff3ecc99d99c)

The package that linked you here is now pure [ESM](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). It cannot be `require()`'d from CommonJS.

[how-to-use-an-es6-import-in-node-js](https://www.geeksforgeeks.org/how-to-use-an-es6-import-in-node-js/)

## path is a directory?

[https://melvingeorge.me/blog/check-if-path-is-directory-nodejs](https://melvingeorge.me/blog/check-if-path-is-directory-nodejs)

```tsx
// Check if path is a directory
fs.statSync("./reports").isDirectory();
```



## 禁止favicon.ico请求


在做node爬虫时，发现会不断地发送 avicon.ico 请求，导致不断console输出，不断写入logger日志


可以在页面的 `<head>` 区域，加上如下代码实现屏蔽：

```javascript
<link rel="icon" href="data:;base64,=">
```


或者详细一点

```javascript
<link rel="icon" href="data:image/ico;base64,aWNv">
```

>当然，既然是 dataURL 方式，IE < 8 等 old browser 就不适用了



## Cannot find node-sass

 https://blog.csdn.net/sliver1836/article/details/95950284 

Error: Cannot find module 'node-sass'问题

记得先把node-sass卸载掉

npm uninstall node-sass

```
1.在项目目录cmd下运行 npm install -g cnpm --registry=https://registry.npm.taobao.org

2.下载成功后再运行 cnpm install node-sass
```

## __dirname is not available

当package.json 中   "type": "module" 时， 我们使用ES modules运行js，这个时候不存在 __dirname 变量了

In ES modules, the `__dirname` variable is not available because ES modules use a different scope than CommonJS modules.

In CommonJS modules, `__dirname` is a global variable that represents the directory name of the current module. It can be used to get the absolute path of the current module's directory.

However, in ES modules, there is no direct equivalent for `__dirname`. Instead, you can use the `import.meta.url` property to get the URL of the current module and then extract the directory name from it.

```tsx
import { fileURLToPath } from 'url';
import { dirname } from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

console.log(__dirname);
```







# 源码

nodejs源码有两个重要的目录：

1. `lib`: 包含了所有nodejs函数和模块的javascript实现，这些实现都是可以直接在你js项目中引用进去的
2. `src`: 包含了所有函数的C++版本实现，这里的代码才会真正引用Libuv和V8。



