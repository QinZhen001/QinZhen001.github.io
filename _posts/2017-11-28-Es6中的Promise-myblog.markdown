---
layout:     post
title:      "ES6中的Promise"
date:       2017-11-28 20:36:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ES6
---

> “Yeah It's on. ”


## 正文


[网页链接](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Promise 对象用于一个异步操作的最终完成（或失败）及其结果值的表示。
(简单点说就是处理异步请求。我们经常会做些承诺，如果我赢了你就嫁给我，如果输了我就嫁给你之类的诺言。这就是promise的中文含义：诺言，一个成功，一个失败。)

### 语法

new Promise( function(resolve, reject) {...} /* executor */  );

#### 参数 executor
executor是一个带有 resolve 和 reject 两个参数的函数 。executor 函数在Promise构造函数执行时同步执行，被传递 resolve 和 reject 函数（executor 函数在Promise构造函数返回新建对象前被调用）。resolve 和 reject 函数被调用时，分别将promise的状态改为fulfilled（完成）或rejected（失败）。


executor 内部通常会执行一些异步操作，一旦完成，可以调用resolve函数来将promise状态改成fulfilled，或者在发生错误时将它的状态改为rejected。

如果在executor函数中抛出一个错误，那么该promise状态为rejected。executor函数的返回值被忽略。 

### 描述
Promise 对象是一个代理对象（代理一个值），被代理的值在Promise对象创建时可能是未知的。它允许你为异步操作的成功和失败分别绑定相应的处理方法（handlers）。 这让异步方法可以像同步方法那样返回值，但并不是立即返回最终执行结果，而是一个能代表未来出现的结果的promise对象

一个 Promise有以下几种状态:
* pending: 初始状态，不是成功或失败状态。
* fulfilled: 意味着操作成功完成。
* rejected: 意味着操作失败。


pending 状态的 Promise 对象可能触发fulfilled 状态并传递一个值给相应的状态处理方法，也可能触发失败状态（rejected）并传递失败信息。当其中任一种情况出现时，Promise 对象的 then 方法绑定的处理方法（handlers ）就会被调用（then方法包含两个参数：onfulfilled 和 onrejected，它们都是 Function 类型。当Promise状态为fulfilled时，调用 then 的 onfulfilled 方法，当Promise状态为rejected时，调用 then 的 onrejected 方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争）。


因为 Promise.prototype.then 和  Promise.prototype.catch 方法返回promise 对象， 所以它们可以被链式调用。

![enter description here][1]


### 创建Promise

Promise 对象是由关键字 new 及其构造函数来创建的。该构造函数会把一个叫做“处理器函数”（executor function）的函数作为它的参数。这个“处理器函数”接受两个函数——resolve 和 reject ——作为其参数。当异步任务顺利完成且返回结果值时，会调用 resolve 函数；而当异步任务失败且返回失败原因（通常是一个错误对象）时，会调用reject 函数。
```javascript
const myFirstPromise = new Promise((resolve, reject) => {
  // ?异步操作，最终调用:
  //
  //   resolve(someValue); // fulfilled
  // ?或
  //   reject("failure reason"); // rejected
});
```

想要某个函数拥有promise功能，只需让其返回一个promise即可。
```javascript
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};
```

### 示例
```javascript
let myFirstPromise = new Promise(function(resolve, reject){
    //当异步代码执行成功时，我们才会调用resolve(...), 当异步代码失败时就会调用reject(...)
    //在本例中，我们使用setTimeout(...)来模拟异步代码，实际编码时可能是XHR请求或是HTML5的一些API方法.
    setTimeout(function(){
        resolve("成功!"); //代码正常执行！
    }, 250);
});

myFirstPromise.then(function(successMessage){
    //successMessage的值是上面调用resolve(...)方法传入的值.
    //successMessage参数不一定非要是字符串类型，这里只是举个例子
    console.log("Yay! " + successMessage);
});
```

包装成一个函数
```javascript
function runAsync () {
    var promise = new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log('执行完成');
            resolve('随便什么数据');
        }, 2000);
    });
    return promise;
}


runAsync().then(function (data) {
    console.log(data);
    // 后边可以用传过来的数据来做一些其他操作
    // ......
});
```

### 链式调用
```javascript
function runAsync1(){
    var promise = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务1执行完成');
            resolve('随便什么数据1');
        }, 1000);
    });
    return promise;
}
function runAsync2(){
    var promise = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务2执行完成');
            resolve('随便什么数据2');
        }, 2000);
    });
    return promise;
}
function runAsync3(){
    var promise = new Promise(function(resolve, reject){
        //做一些异步操作
        setTimeout(function(){
            console.log('异步任务3执行完成');
            resolve('随便什么数据3');
        }, 2000);
    });
    return promise;
}
runAsync1()
    .then(function (data) {
        console.log(data);
        return runAsync2();
    })
    .then(function (data) {
        console.log(data);
        return runAsync3();
    })
    .then(function (data) {
        console.log(data);
    });
```

这段代码运行的结果是：

异步任务1执行完成
随便什么数据1
异步任务2执行完成
随便什么数据2
异步任务3执行完成
随便什么数据3

### Promise.prototype.then() VS Promise.prototype.catch()

[https://www.zhihu.com/question/48765053](https://www.zhihu.com/question/48765053)

.then()方法使Promise原型链上的方法，它包含两个参数方法，分别是已成功resolved的回调和已失败rejected的回调

```
promise.then(
    () => { console.log('this is success callback') },
    () => { console.log('this is fail callback') }
)
```

.catch()的作用是捕获Promise的错误，与then()的rejected回调作用几乎一致。但是由于Promise的抛错具有冒泡性质，能够不断传递，这样就能够在下一个catch()中统一处理这些错误。同时catch()也能够捕获then()中抛出的错误，**所以建议不要使用then()的rejected回调，而是统一使用catch()来处理错误**

```
promise.then(
    () => { console.log('this is success callback') }
).catch(
    (err) => { console.log(err) }
)
```



Promise.prototype.catch()实际上等于 [`Promise.prototype.then(undefined, onRejected)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)



Promise.prototype.catch()返回的也是一个Promise



举个例子：

```js
function test(res) {
    return Promise.resolve(res)
        .then(res => {
            console.log(res += '!');
            return res;
        })
        .then(res => {
            console.log(res += '!');
            return Promise.reject("end"); //此处返回了一个新的promise
        })
        .catch(res => {
            console.log(res);
            return res;  //此处也返回了一个新的resolved的promise
        })
        .then(res => {
            console.log(res += '!');  //肯定会执行了
        });
}

test('hello');
```





### Promise.resolve()  

>Promise这里是大写的P

[https://segmentfault.com/q/1010000005330351](https://segmentfault.com/q/1010000005330351)

```javascript
var foo = {
    then: (resolve, reject) => resolve('foo')
};
var resolved = Promise.resolve(foo);
相当于
var resolved = new Promise((resolve, reject) => {
    foo.then(resolve, reject)
});

resolved.then((str) => 
    console.log(str);//foo
)
```


----------

```javascript
Promise.resolve('hello');
// 相当于
const promise = new Promise(resolve => {
   resolve('hello');
});
```



Promise.resolve方法有下面三种形式：

Promise.resolve(value);
Promise.resolve(promise);
Promise.resolve(theanable);
这三种形式都会产生一个新的Promise。其中：

第一种形式提供了自定义Promise的值的能力，它与Promise.reject(reason)对应。两者的不同，在于得到的Promise的状态不同。

第二种形式，提供了创建一个Promise的副本的能力。

第三种形式，是将一个类似Promise的对象转换成一个真正的Promise对象。它的一个重要作用是将一个其他实现的Promise对象封装成一个当前实现的Promise对象。例如你正在用bluebird，但是现在有一个Q的Promise，那么你可以通过此方法把Q的Promise变成一个bluebird的Promise。

实际上第二种形式可以归在第三种形式中。



### 一个promise依赖另外一个


[http://es6.ruanyifeng.com/#docs/promise](http://es6.ruanyifeng.com/#docs/promise)



resolve函数的参数除了正常的值以外，还可能是另一个 Promise 实例，比如像下面这样。




注意，这时p1的状态就会传递给p2，也就是说，p1的状态决定了p2的状态。如果p1的状态是pending，那么p2的回调函数就会等待p1的状态改变；如果p1的状态已经是resolved或者rejected，那么p2的回调函数将会立刻执行。



```javascript
const p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject(new Error('fail')), 3000)
})

const p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000)
})

p2
  .then(result => console.log(result))
  .catch(error => console.log(error))
  
// 输出  
// Error: fail
```



上面代码中，p1是一个 Promise，3 秒之后变为rejected。p2的状态在 1 秒之后改变，resolve方法返回的是p1。由于p2返回的是另一个 Promise，导致p2自己的状态无效了，由p1的状态决定p2的状态。所以，后面的then语句都变成针对后者（p1）。又过了 2 秒，p1变为rejected，导致触发catch方法指定的回调函数。





## 补充

### try/catch无法捕获promise.reject的问题

> try/catch 只能捕获同步的错误

[https://segmentfault.com/q/1010000014905440](https://segmentfault.com/q/1010000014905440)


```javascript
function f2() {
  try {
    Promise.reject('出错了');
  } catch(e) {
    console.log(e)
  }
}
```


执行f2()，无法通过try/catch捕获promise.reject，控制台抛出Uncaught (in promise)


```javascript
async function f() {
  try {
    await Promise.reject('出错了')
  } catch(e) {
    console.log(e)
  }
}
```


为什么改成await/async后，执行f()就能在catch中捕获到错误了，并不会抛出Uncaught (in promise)


------------


**这是一个非常好的问题**



```javascript
function f2() {
  try {
    Promise.reject('出错了').catch(err => {
      console.log('2', err)
    });
    console.log('1')
  } catch (e) {
    console.log(e)
  }
}
```

**这样应该就理解了吧，拒绝reject发生在未来。**


**try..catch 结构，它只能是同步的，无法用于异步代码模式**



### Promise含有隐式 try…catch

[https://zh.javascript.info/promise-error-handling#yin-shi-trycatch](https://zh.javascript.info/promise-error-handling#yin-shi-trycatch)

Promise 的执行者（executor）和 promise 的处理程序（handler）周围有一个“隐式的 `try..catch`”。如果发生异常，它（译注：指异常）就会被捕获，并被视为 rejection 进行处理。



例如，下面这段代码：

```javascript
new Promise((resolve, reject) => {
  throw new Error("Whoops!");
}).catch(alert); // Error: Whoops!
```

……与下面这段代码工作上完全相同：

```js
new Promise((resolve, reject) => {
  reject(new Error("Whoops!"));
}).catch(alert); // Error: Whoops!
```








### catch返回的还是Promise


一般总是建议，Promise 对象后面要跟catch方法，这样可以处理 Promise 内部发生的错误。catch方法返回的还是一个 Promise 对象，因此后面还可以接着调用then方法。

```javascript
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};


someAsyncThing()
.catch(function(error) {
  console.log('oh no', error);
})
.then(function() {
  console.log('carry on');
});


// 输出
// oh no [ReferenceError: x is not defined]
// carry on
```







### promise中如何取到[[PromiseValue]]

[https://segmentfault.com/q/1010000010670739](https://segmentfault.com/q/1010000010670739)


```javascript
var a = Promise.resolve('xx')
// Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "xx"}
a.then(function (result) { console.log(result) })
```

### Promise的执行时机


Promise 新建后就会立即执行。

```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```

上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。




### resolve或reject不会终止Promise的执行


注意，调用resolve或reject并不会终结 Promise 的参数函数的执行。

```javascript
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});

// 输出
// 2
// 1
```


上面代码中，调用resolve(1)以后，后面的console.log(2)还是会执行，**并且会首先打印出来**。


**这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务。**



这一点非常的重要



### promise.all使用问题
```javascript
const getRandom = () => +(Math.random()*1000).toFixed(0);

const asyncTask = taskID => new Promise(resolve => {
    let timeout = getRandom();
    console.log(`taskID=${taskID} start.`);
    setTimeout(function() {
        console.log(`taskID=${taskID} finished in time=${timeout}.`);
        resolve(taskID)
    }, timeout);
});

Promise.all([asyncTask(1),asyncTask(2),asyncTask(3)])
.then(resultList => {
    console.log('results:',resultList);
});
```

结论:

由此可见，Promise.all里的任务列表[asyncTask(1),asyncTask(2),asyncTask(3)]，是按顺序发起的，**由于它们都是异步的，互相之间并不阻塞，每个任务完成时机是不确定的。尽管如此，所有任务结束之后，它们的结果仍然是按顺序地映射到resultList里**，这样就能和Promise.all里的任务列表[asyncTask(1),asyncTask(2),asyncTask(3)]一一对应起来。


### return的问题

**return resolve() 或 return reject()之后的代码不会执行**



例子：


resolve前面不加return情况

```javascript
function test() {
  test1().then(res=>{
    console.log(res)
  })
}


function test1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('resolve 前面')
      resolve('aaa')
      console.log('resolve 后面')
    }, 1000)
  })
}


test()
```


```javascript
//输出
resolve 前面
resolve 后面
aaa
```


----------





resolve前面加return情况

```javascript
function test() {
  test1().then(res=>{
    console.log(res)
  })
}


function test1() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('resolve 前面')
      return resolve('aaa')
      console.log('resolve 后面')
    }, 1000)
  })
}


test()
```


```javascript
//输出
resolve 前面
aaa
```



### promise中存在try-catch



```javascript
  function _templateListReport() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject("111111")
      }, 3000)
    })
  }


  function test() {
    return new Promise(async (resolve, reject) => {
      try {
        return resolve(await _templateListReport());
      } catch (e) {
        // 捕获reject
        console.error(e)
        reject(e)
      }
    })
  }


  test().then(res => {
    console.log("我成功了", res)
  }, rej => {
    console.log("我失败了", rej)
  })
```


这个会输出什么？


答案：

```javascript
await _templateListReport()

返回的是reject状态，catch会捕获reject
```

## 其他




### Promise 代码实现
```javascript
/**
 * Promise 实现 遵循promise/A+规范
 * Promise/A+规范译文:
 * https://malcolmyu.github.io/2015/06/12/Promises-A-Plus/#note-4
 */

// promise 三个状态
const PENDING = "pending";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";

function Promise(excutor) {
    let that = this; // 缓存当前promise实例对象
    that.status = PENDING; // 初始状态
    that.value = undefined; // fulfilled状态时 返回的信息
    that.reason = undefined; // rejected状态时 拒绝的原因
    that.onFulfilledCallbacks = []; // 存储fulfilled状态对应的onFulfilled函数
    that.onRejectedCallbacks = []; // 存储rejected状态对应的onRejected函数

    function resolve(value) { // value成功态时接收的终值
        if(value instanceof Promise) {
            return value.then(resolve, reject);
        }

        // 为什么resolve 加setTimeout?
        // 2.2.4规范 onFulfilled 和 onRejected 只允许在 execution context 栈仅包含平台代码时运行.
        // 注1 这里的平台代码指的是引擎、环境以及 promise 的实施代码。实践中要确保 onFulfilled 和 onRejected 方法异步执行，且应该在 then 方法被调用的那一轮事件循环之后的新执行栈中执行。

        setTimeout(() => {
            // 调用resolve 回调对应onFulfilled函数
            if (that.status === PENDING) {
                // 只能由pedning状态 => fulfilled状态 (避免调用多次resolve reject)
                that.status = FULFILLED;
                that.value = value;
                that.onFulfilledCallbacks.forEach(cb => cb(that.value));
            }
        });
    }

    function reject(reason) { // reason失败态时接收的拒因
        setTimeout(() => {
            // 调用reject 回调对应onRejected函数
            if (that.status === PENDING) {
                // 只能由pedning状态 => rejected状态 (避免调用多次resolve reject)
                that.status = REJECTED;
                that.reason = reason;
                that.onRejectedCallbacks.forEach(cb => cb(that.reason));
            }
        });
    }

    // 捕获在excutor执行器中抛出的异常
    // new Promise((resolve, reject) => {
    //     throw new Error('error in excutor')
    // })
    try {
        excutor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}

/**
 * resolve中的值几种情况：
 * 1.普通值
 * 2.promise对象
 * 3.thenable对象/函数
 */

/**
 * 对resolve 进行改造增强 针对resolve中不同值情况 进行处理
 * @param  {promise} promise2 promise1.then方法返回的新的promise对象
 * @param  {[type]} x         promise1中onFulfilled的返回值
 * @param  {[type]} resolve   promise2的resolve方法
 * @param  {[type]} reject    promise2的reject方法
 */
function resolvePromise(promise2, x, resolve, reject) {
    if (promise2 === x) {  // 如果从onFulfilled中返回的x 就是promise2 就会导致循环引用报错
        return reject(new TypeError('循环引用'));
    }

    let called = false; // 避免多次调用
    // 如果x是一个promise对象 （该判断和下面 判断是不是thenable对象重复 所以可有可无）
    if (x instanceof Promise) { // 获得它的终值 继续resolve
        if (x.status === PENDING) { // 如果为等待态需等待直至 x 被执行或拒绝 并解析y值
            x.then(y => {
                resolvePromise(promise2, y, resolve, reject);
            }, reason => {
                reject(reason);
            });
        } else { // 如果 x 已经处于执行态/拒绝态(值已经被解析为普通值)，用相同的值执行传递下去 promise
            x.then(resolve, reject);
        }
        // 如果 x 为对象或者函数
    } else if (x != null && ((typeof x === 'object') || (typeof x === 'function'))) {
        try { // 是否是thenable对象（具有then方法的对象/函数）
            let then = x.then;
            if (typeof then === 'function') {
                then.call(x, y => {
                    if(called) return;
                    called = true;
                    resolvePromise(promise2, y, resolve, reject);
                }, reason => {
                    if(called) return;
                    called = true;
                    reject(reason);
                })
            } else { // 说明是一个普通对象/函数
                resolve(x);
            }
        } catch(e) {
            if(called) return;
            called = true;
            reject(e);
        }
    } else {
        resolve(x);
    }
}

/**
 * [注册fulfilled状态/rejected状态对应的回调函数]
 * @param  {function} onFulfilled fulfilled状态时 执行的函数
 * @param  {function} onRejected  rejected状态时 执行的函数
 * @return {function} newPromsie  返回一个新的promise对象
 */
Promise.prototype.then = function(onFulfilled, onRejected) {
    const that = this;
    let newPromise;
    // 处理参数默认值 保证参数后续能够继续执行
    onFulfilled =
        typeof onFulfilled === "function" ? onFulfilled : value => value;
    onRejected =
        typeof onRejected === "function" ? onRejected : reason => {
            throw reason;
        };

    // then里面的FULFILLED/REJECTED状态时 为什么要加setTimeout ?
    // 原因:
    // 其一 2.2.4规范 要确保 onFulfilled 和 onRejected 方法异步执行(且应该在 then 方法被调用的那一轮事件循环之后的新执行栈中执行) 所以要在resolve里加上setTimeout
    // 其二 2.2.6规范 对于一个promise，它的then方法可以调用多次.（当在其他程序中多次调用同一个promise的then时 由于之前状态已经为FULFILLED/REJECTED状态，则会走的下面逻辑),所以要确保为FULFILLED/REJECTED状态后 也要异步执行onFulfilled/onRejected

    // 其二 2.2.6规范 也是resolve函数里加setTimeout的原因
    // 总之都是 让then方法异步执行 也就是确保onFulfilled/onRejected异步执行

    // 如下面这种情景 多次调用p1.then
    // p1.then((value) => { // 此时p1.status 由pedding状态 => fulfilled状态
    //     console.log(value); // resolve
    //     // console.log(p1.status); // fulfilled
    //     p1.then(value => { // 再次p1.then 这时已经为fulfilled状态 走的是fulfilled状态判断里的逻辑 所以我们也要确保判断里面onFuilled异步执行
    //         console.log(value); // 'resolve'
    //     });
    //     console.log('当前执行栈中同步代码');
    // })
    // console.log('全局执行栈中同步代码');
    //

    if (that.status === FULFILLED) { // 成功态
        return newPromise = new Promise((resolve, reject) => {
            setTimeout(() => {
                try{
                    let x = onFulfilled(that.value);
                    resolvePromise(newPromise, x, resolve, reject); // 新的promise resolve 上一个onFulfilled的返回值
                } catch(e) {
                    reject(e); // 捕获前面onFulfilled中抛出的异常 then(onFulfilled, onRejected);
                }
            });
        })
    }

    if (that.status === REJECTED) { // 失败态
        return newPromise = new Promise((resolve, reject) => {
            setTimeout(() => {
                try {
                    let x = onRejected(that.reason);
                    resolvePromise(newPromise, x, resolve, reject);
                } catch(e) {
                    reject(e);
                }
            });
        });
    }

    if (that.status === PENDING) { // 等待态
        // 当异步调用resolve/rejected时 将onFulfilled/onRejected收集暂存到集合中
        return newPromise = new Promise((resolve, reject) => {
            that.onFulfilledCallbacks.push((value) => {
                try {
                    let x = onFulfilled(value);
                    resolvePromise(newPromise, x, resolve, reject);
                } catch(e) {
                    reject(e);
                }
            });
            that.onRejectedCallbacks.push((reason) => {
                try {
                    let x = onRejected(reason);
                    resolvePromise(newPromise, x, resolve, reject);
                } catch(e) {
                    reject(e);
                }
            });
        });
    }
};

/**
 * Promise.all Promise进行并行处理
 * 参数: promise对象组成的数组作为参数
 * 返回值: 返回一个Promise实例
 * 当这个数组里的所有promise对象全部变为resolve状态的时候，才会resolve。
 */
Promise.all = function(promises) {
    return new Promise((resolve, reject) => {
        let done = gen(promises.length, resolve);
        promises.forEach((promise, index) => {
            promise.then((value) => {
                done(index, value)
            }, reject)
        })
    })
}

function gen(length, resolve) {
    let count = 0;
    let values = [];
    return function(i, value) {
        values[i] = value;
        if (++count === length) {
            console.log(values);
            resolve(values);
        }
    }
}

/**
 * Promise.race
 * 参数: 接收 promise对象组成的数组作为参数
 * 返回值: 返回一个Promise实例
 * 只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理(取决于哪一个更快)
 */
Promise.race = function(promises) {
    return new Promise((resolve, reject) => {
        promises.forEach((promise, index) => {
           promise.then(resolve, reject);
        });
    });
}

// 用于promise方法链时 捕获前面onFulfilled/onRejected抛出的异常
Promise.prototype.catch = function(onRejected) {
    return this.then(null, onRejected);
}

Promise.resolve = function (value) {
    return new Promise(resolve => {
        resolve(value);
    });
}

Promise.reject = function (reason) {
    return new Promise((resolve, reject) => {
        reject(reason);
    });
}

/**
 * 基于Promise实现Deferred的
 * Deferred和Promise的关系
 * - Deferred 拥有 Promise
 * - Deferred 具备对 Promise的状态进行操作的特权方法（resolve reject）
 *
 *参考jQuery.Deferred
 *url: http://api.jquery.com/category/deferred-object/
 */
Promise.deferred = function() { // 延迟对象
    let defer = {};
    defer.promise = new Promise((resolve, reject) => {
        defer.resolve = resolve;
        defer.reject = reject;
    });
    return defer;
}

/**
 * Promise/A+规范测试
 * npm i -g promises-aplus-tests
 * promises-aplus-tests Promise.js
 */

try {
  module.exports = Promise
} catch (e) {
}
```




作者：君未来我已老
链接：https://www.jianshu.com/p/c98eb98bd00c
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



-------------------------






[网页链接](https://github.com/forthealllight/blog/issues/4)


### Promise/A+规范 实现Promise

#### 术语
1. "promise"是一个对象或者函数，该对象或者函数有一个then方法
2. "thenable"是一个对象或者函数，用来定义then方法
3. "value"是promise状态成功时的值
4. "reason"是promise状态失败时的值

#### 要求
一个promise必须有3个状态，pending，fulfilled(resolved)，rejected当处于pending状态的时候，可以转移到fulfilled(resolved)或者rejected状态。当处于fulfilled(resolved)状态或者rejected状态的时候，就不可变。

promise英文译为承诺，也就是说promise的状态一旦发生改变，就永远是不可逆的。


----------


一个promise必须有一个then方法，then方法接受两个参数：

`promise.then(onFulfilled,onRejected)`

其中onFulfilled方法表示状态从pending——>fulfilled(resolved)时所执行的方法，而onRejected表示状态从pending——>rejected所执行的方法。


----------


为了实现链式调用，then方法必须返回一个promise


`promise2=promise1.then(onFulfilled,onRejected)`


#### v1.0 初始版本myPromise



```javascript
function myPromise(constructor){
    let self=this;
    self.status="pending" //定义状态改变前的初始状态
    self.value=undefined;//定义状态为resolved的时候的状态
    self.reason=undefined;//定义状态为rejected的时候的状态
    function resolve(value){
        //两个==="pending"，保证了状态的改变是不可逆的
       if(self.status==="pending"){
          self.value=value;
          self.status="resolved";
       }
    }
    function reject(reason){
        //两个==="pending"，保证了状态的改变是不可逆的
       if(self.status==="pending"){
          self.reason=reason;
          self.status="rejected";
       }
    }
    //捕获构造异常
    try{
       constructor(resolve,reject);
    }catch(e){
       reject(e);
    }
}
```

同时，需要在myPromise的原型上定义链式调用的then方法：


```javascript
myPromise.prototype.then=function(onFullfilled,onRejected){
   let self=this;
   switch(self.status){
      case "resolved":
        onFullfilled(self.value);
        break;
      case "rejected":
        onRejected(self.reason);
        break;
      default:       
   }
}
```

上述就是一个初始版本的myPromise，在myPromise里发生状态改变，然后在相应的then方法里面根据不同的状态可以执行不同的操作。


```javascript
var p=new myPromise(function(resolve,reject){resolve(1)});
p.then(function(x){console.log(x)})
//输出1
```



**问题:这里myPromise无法处理异步的resolve**



#### v2.0基于观察模式实现


为了处理异步resolve，我们修改myPromise的定义，用2个数组onFullfilledArray和onRejectedArray来保存异步的方法。在状态发生改变时，一次遍历执行数组中的方法。

```javascript
function myPromise(constructor){
    let self=this;
    self.status="pending" //定义状态改变前的初始状态
    self.value=undefined;//定义状态为resolved的时候的状态
    self.reason=undefined;//定义状态为rejected的时候的状态
    self.onFullfilledArray=[];
    self.onRejectedArray=[];
    function resolve(value){
       if(self.status==="pending"){
          self.value=value;
          self.status="resolved";
          self.onFullfilledArray.forEach(function(f){
                f(self.value);
                //如果状态从pending变为resolved，
                //那么就遍历执行里面的异步方法
          });
        
       }
    }
    function reject(reason){
       if(self.status==="pending"){
          self.reason=reason;
          self.status="rejected";
          self.onRejectedArray.forEach(function(f){
              f(self.reason);
             //如果状态从pending变为rejected， 
             //那么就遍历执行里面的异步方法
          })
       }
    }
    //捕获构造异常
    try{
       constructor(resolve,reject);
    }catch(e){
       reject(e);
    }
}
```

对于then方法，状态为pending时，往数组里面添加方法：


```javascript
myPromise.prototype.then=function(onFullfilled,onRejected){
   let self=this;
   switch(self.status){
      case "pending":
        self.onFullfilledArray.push(function(){
             onFullfilled(self.value)
        });
        self.onRejectedArray.push(function(){
             onRejected(self.reason)
        });
      case "resolved":
        onFullfilled(self.value);
        break;
      case "rejected":
        onRejected(self.reason);
        break;
      default:       
   }
}
```


这样，通过两个数组，在状态发生改变之后再开始执行，这样可以处理异步resolve无法调用的问题。这个版本的myPromise就能处理所有的异步，那么这样做就完整了吗？

没有，我们做Promise/A+规范的最大的特点就是链式调用，也就是说then方法返回的应该是一个promise。

#### v3.0then方法实现链式调用
要通过then方法实现链式调用，那么也就是说then方法每次调用需要返回一个primise,同时在返回promise的构造体里面，增加错误处理部分，我们来改造then方法

```javascript
myPromise.prototype.then=function(onFullfilled,onRejected){
    let self=this;
    let promise2;
    switch(self.status){
      case "pending":
        promise2=new myPromise(function(resolve,reject){
             self.onFullfilledArray.push(function(){
                try{
                   let temple=onFullfilled(self.value);
                   resolve(temple)
                }catch(e){
                   reject(e) //error catch
                }
             });
             self.onRejectedArray.push(function(){
                 try{
                   let temple=onRejected(self.reason);
                   reject(temple)
                 }catch(e){
                   reject(e)// error catch
                 }
             });
        })
      case "resolved":
        promise2=new myPromise(function(resolve,reject){
            try{
              let temple=onFullfilled(self.value);
              //将上次一then里面的方法传递进下一个Promise的状态
              resolve(temple);
            }catch(e){
              reject(e);//error catch
            }
        })
        break;
      case "rejected":
        promise2=new myPromise(function(resolve,reject){
            try{
               let temple=onRejected(self.reason);
               //将then里面的方法传递到下一个Promise的状态里
               resolve(temple);   
            }catch(e){
               reject(e);
            }
        })
        break;
      default:       
   }
   return promise2;
}
```

这样通过then方法返回一个promise就可以实现链式的调用：

```
p.then(function(x){console.log(x)}).then(function(){console.log("链式调用1")}).then(function(){console.log("链式调用2")})
//输出
1
链式调用1
链式调用2
```


这样我们虽然实现了then函数的链式调用，但是还有一个问题，就是在Promise/A+规范中then函数里面的onFullfilled方法和onRejected方法的返回值可以是对象，函数，甚至是另一个promise。



#### v4.0 then函数中的onFullfilled和onRejected方法的返回值问题


特别的为了解决onFullfilled和onRejected方法的返回值可能是一个promise的问题。














[1]: https://mdn.mozillademos.org/files/8633/promises.png