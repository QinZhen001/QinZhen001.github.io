---
layout:     post
title:      "理解ES7的async和await "
date:       2018-04-18 15:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ES7
---

> “Yeah It's on. ”


## 正文
[网页链接](https://blog.csdn.net/sinat_17775997/article/details/60609498)


[http://es6.ruanyifeng.com/#docs/async](http://es6.ruanyifeng.com/#docs/async)


ES2017 标准引入了 async 函数，使得异步操作变得更加方便。
async 函数是什么？

**一句话，它就是 Generator 函数的语法糖。**

**把异步写成同步的形式**



```javascript
const fs = require('fs');

const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```



写成async函数，就是下面这样。

```javascript
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```



一比较就会发现，async函数就是将 Generator 函数的星号（*）替换成async，将yield替换成await，仅此而已。


async函数对 Generator 函数的改进，体现在以下四点。

**内置执行器**

Generator 函数的执行必须靠执行器，所以才有了co模块，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。

`asyncReadFile();`

上面的代码调用了asyncReadFile函数，然后它就会自动执行，输出最后结果。这完全不像 Generator 函数，需要调用next方法，或者用co模块，才能真正执行，得到最后结果。

**更好的语义**

async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。


**更广的适用性**

co模块约定，yield命令后面只能是 Thunk 函数或 Promise 对象，而async函数的await命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）


**返回值是 Promise**

async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用then方法指定下一步的操作


**进一步说，async函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而await命令就是内部then命令的语法糖。**




### 基本用法

async函数返回一个 Promise 对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句


```javascript
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```



async函数的语法规则总体上比较简单，难点是错误处理机制。

**async函数返回一个 Promise 对象。**

**async函数内部return语句返回的值，会成为then方法回调函数的参数。**

```javascript
async function f() {
  return 'hello world';
}

f().then(v => console.log(v))
// "hello world"
```



上面代码中，函数f内部return命令返回的值，会被then方法回调函数接收到。

async函数内部抛出错误，会导致返回的 Promise 对象变为reject状态。抛出的错误对象会被catch方法回调函数接收到。


```javascript
async function f() {
  throw new Error('出错了');
}

f().then(
  v => console.log(v),
  e => console.log(e)
)
// Error: 出错了
```

### Promise 对象的状态变化
async函数返回的 Promise 对象，必须等到内部所有await命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到return语句或者抛出错误。也就是说，只有async函数内部的异步操作执行完，才会执行then方法指定的回调函数。

下面是一个例子
```javascript
async function getTitle(url) {
  let response = await fetch(url);
  let html = await response.text();
  return html.match(/<title>([\s\S]+)<\/title>/i)[1];
}
getTitle('https://tc39.github.io/ecma262/').then(console.log)
// "ECMAScript 2017 Language Specification"
```
上面代码中，函数getTitle内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行then方法里面的console.log。


### await 命令
**正常情况下，await命令后面是一个 Promise 对象，返回该对象的结果。如果不是 Promise 对象，就直接返回对应的值。**

```javascript
async function f() {
  return await 123;
}

f().then(v => console.log(v))
// 123
```

上面代码中，await命令的参数是数值123，它被转成 Promise 对象，并立即resolve。






await命令后面的 Promise 对象如果变为reject状态，则reject的参数会被catch方法的回调函数接收到。

```javascript
async function f() {
  await Promise.reject('出错了');
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
// 出错了
```

注意，上面代码中，await语句前面没有return，但是reject方法的参数依然传入了catch方法的回调函数。




**只要一个await语句后面的 Promise 变为reject，那么整个async函数都会中断执行。**

```javascript
async function f() {
  await Promise.reject('出错了');
  await Promise.resolve('hello world'); // 不会执行
}
```


上面代码中，第二个await语句是不会执行的，因为第一个await语句状态变成了reject。


有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个await放在**try...catch结构**里面，这样不管这个异步操作是否成功，第二个await都会执行。
```javascript
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
```

另一种方法是await后面的 Promise 对象再跟一个catch方法，处理前面可能出现的错误。



```javascript
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// 出错了
// hello world
```


### 错误处理
如果await后面的异步操作出错，那么等同于async函数返回的 Promise 对象被reject。

```javascript
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了');
  });
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
// Error：出错了
```


上面代码中，async函数f执行后，await后面的 Promise 对象会抛出一个错误对象，导致catch方法的回调函数被调用，它的参数就是抛出的错误对象


防止出错的方法，也是将其放在try...catch代码块之中。

```javascript
async function f() {
  try {
    await new Promise(function (resolve, reject) {
      throw new Error('出错了');
    });
  } catch(e) {
  }
  return await('hello world');
}
```
如果有多个await命令，可以统一放在try...catch结构中。

```javascript
async function main() {
  try {
    const val1 = await firstStep();
    const val2 = await secondStep(val1);
    const val3 = await thirdStep(val1, val2);

    console.log('Final: ', val3);
  }
  catch (err) {
    console.error(err);
  }
}
```

下面的例子使用try...catch结构，实现多次重复尝试。
```javascript
const superagent = require('superagent');
const NUM_RETRIES = 3;

async function test() {
  let i;
  for (i = 0; i < NUM_RETRIES; ++i) {
    try {
      await superagent.get('http://google.com/this-throws-an-error');
      break;
    } catch(err) {}
  }
  console.log(i); // 3
}

test();
```
上面代码中，如果await操作成功，就会使用break语句退出循环；如果失败，会被catch语句捕捉，然后进入下一轮循环。


### 使用的注意点
第一点，前面已经说过，await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。
```javascript
async function myFunction() {
  try {
    await somethingThatReturnsAPromise();
  } catch (err) {
    console.log(err);
  }
}

// 另一种写法

async function myFunction() {
  await somethingThatReturnsAPromise()
  .catch(function (err) {
    console.log(err);
  });
}
```
第二点，多个await命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
```
let foo = await getFoo();
let bar = await getBar();
```

上面代码中，getFoo和getBar是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有getFoo完成以后，才会执行getBar，完全可以让它们同时触发。

```javascript
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

上面两种写法，getFoo和getBar都是同时触发，这样就会缩短程序的执行时间。

第三点，await命令只能用在async函数之中，如果用在普通函数，就会报错。
```
async function dbFuc(db) {
  let docs = [{}, {}, {}];

  // 报错
  docs.forEach(function (doc) {
    await db.post(doc);
  });
}
```
上面代码会报错，因为await用在普通函数之中了。但是，如果将forEach方法的参数改成async函数，也有问题。

```
function dbFuc(db) { //这里不需要 async
  let docs = [{}, {}, {}];

  // 可能得到错误结果
  docs.forEach(async function (doc) {
    await db.post(doc);
  });
}
```

上面代码可能不会正常工作，原因是这时三个db.post操作将是并发执行，也就是同时执行，而不是继发执行。正确的写法是采用for循环。

```
async function dbFuc(db) {
  let docs = [{}, {}, {}];

  for (let doc of docs) {
    await db.post(doc);
  }
}
```

如果确实希望多个请求并发执行，可以使用Promise.all方法。当三个请求都会resolved时，下面两种写法效果相同。

```
async function dbFuc(db) {
  let docs = [{}, {}, {}];
  let promises = docs.map((doc) => db.post(doc));

  let results = await Promise.all(promises);
  console.log(results);
}

// 或者使用下面的写法

async function dbFuc(db) {
  let docs = [{}, {}, {}];
  let promises = docs.map((doc) => db.post(doc));

  let results = [];
  for (let promise of promises) {
    results.push(await promise);
  }
  console.log(results);
}
```

## async实现原理

**async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里**

```
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
  return spawn(function* () {
    // ...
  });
}
```
所有的async函数都可以写成上面的第二种形式，其中的spawn函数就是自动执行器。

```
function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if(next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() { return gen.next(v); });
      }, function(e) {
        step(function() { return gen.throw(e); });
      });
    }
    step(function() { return gen.next(undefined); });
  });
}
```



## 问题


### forEach同步执行

```javascript
  function timeOut(name) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(name)
      }, 3000)
    })
  }


  let arr = ["aaa", "bbb", "ccc", "ddd"]

  arr.forEach(async item => {
    let res = await timeOut(item)
    console.log(res)
  })


 三秒后 同时输出 "aaa"  "bbb"  "ccc"  "ddd"


 // await 没有起作用
```



------------------------------


正确做法


```javascript
 function timeOut(name) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(name)
      }, 3000)
    })
  }
  
  let arr = ["aaa", "bbb", "ccc", "ddd"]
  
  async function fn() {
    for (const item of arr) {
      let res = await timeOut(item)
      console.log(res)
    }
  }


  fn()
```


### while包裹await会异步执行

一直有个疑问，就是使用while一个接一个执行await，里面的await会等待上一个await结束才去执行吗？

也就是while包裹await会异步执行吗？

答案是会的。

```
let num = 1;

function test() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log("1111", num++);
      resolve(true);
    }, 5000);
  });
}


async function initBucketA() {
  let arr = [1, 1, 1, 1, 1, 1, 1, 1];
  let item = null;
  while (item = arr.shift()) {
    await test();
  }
}

initBucketA()


// 测试成功
```







### 异步导出变量导致 undefined

[为何在 JavaScript 中使用顶层 await](https://mp.weixin.qq.com/s?__biz=MjM5MDc4MzgxNA==&mid=2458459629&idx=1&sn=ccff389bd895d95c8cf49d29a47a4560&chksm=b1c23dc486b5b4d2638ea755c914dadb6319519ec5bc9c130937d91e2bba2e55fc4fee328a64&scene=132#wechat_redirect)





举个例子：

```jsx
//------ library.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diagonal(x, y) {
    return sqrt(square(x) + square(y));
}


//------ middleware.js ------
import { square, diagonal } from './library.js';

console.log('From Middleware');

let squareOutput;
let diagonalOutput;

// IIFE
 (async () => {
  await delay(1000); // 延时 导致后面squareOutput 和 diagonalOutput 的计算也会延时
  squareOutput = square(13);
  diagonalOutput = diagonal(12, 5);
 })();

function delay(delayInms) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(console.log('❤️'));
    }, delayInms);
  });
}

// 一开始为 undefined 
// delay之后才有值
export {squareOutput,diagonalOutput}; 
```



解决办法：

**导出一个 Promise 表示初始化**

你可以导出一个 IIFE 并依靠它确定可以访问导出结果的时机。`async` 关键字可以异步化一个方法，并相应**返回一个 promise**。因此，下面的代码中，`async` IIFE 会返回一个 promise。

```js
//------ middleware.js ------
import { square, diagonal } from './library.js';

console.log('From Middleware');

let squareOutput;
let diagonalOutput;

//解决方案
export default (async () => {
 await delay(1000);
 squareOutput = square(13);
 diagonalOutput = diagonal(12, 5);
})();

function delay(delayInms) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(console.log('❤️'));
    }, delayInms);
  });
}

export {squareOutput,diagonalOutput};
```

当你在 `main.js` 中访问导出结果的时候，你可以静待 `async` IIFE 被 resolve，之后再去访问变量。

```js
//------ main.js ------
import promise, { squareOutput, diagonalOutput } from './middleware.js';

promise.then(()=>{
  console.log(squareOutput); // 169
  console.log(diagonalOutput); // 13
  console.log('From Main');

  setTimeout(() => console.log(squareOutput), 2000);// 169

  setTimeout(() => console.log(diagonalOutput), 2000);// 13
})
```

尽管这个方案可以生效，但它也引入了新的问题：

- 大家都必须将这种模式作为标准去遵循，而且必须要找到并等待合适的 promise；
- 倘若有另一个模块依赖 `main.js` 中的变量 `squareOutput` 和`diagonalOutput`，那么我们就需要再次书写类似的 IIFE promise 并导出，从而让另一个模块得以正确地访问变量。



为了解决这两个新问题，第二个方案应运而生。

**用导出的变量去 resolve IIFE promise**

```js
//------ middleware.js ------
import { square, diagonal } from './library.js';

console.log('From Middleware');

let squareOutput;
let diagonalOutput;

export default (async () => {
 await delay(1000);
 squareOutput = square(13);
 diagonalOutput = diagonal(12, 5);
 return {squareOutput,diagonalOutput};
})();

function delay(delayInms) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(console.log('❤️'));
    }, delayInms);
  });
}s
```








## 补充
```javascript
const synchronous = () => {
  const promise = Promise.resolve(0);
  console.log(promise === 0); // false
};

const asynchronous = async () => {
  const promise = Promise.resolve(0);
  const value = await promise;
  console.log(value === 0); // true
};
```




作者：鲁小夫
链接：https://www.zhihu.com/question/62254462/answer/196367712
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



### 精读
```javascript
a(() => {
  b(() => {
    c();
  });
});
```
为了减少嵌套结构太多对大脑造成的冲击，async/await 决定这么写：
```
await a();
await b();
await c();
```
虽然层级上一致了，但逻辑上还是嵌套关系，这不是另一个程度上增加了大脑负担吗？而且这个转换还是隐形的，所以许多时候，我们倾向于忽略它，所以造成了语法糖的滥用。



#### 理解语法糖

首先 async/await 只能实现一部分回调支持的功能，也就是仅能方便应对层层嵌套的场景。其他场景，就要动一些脑子了。
比如两对回调：
```
a(() => {
  b();
});

c(() => {
  d();
});
```
如果写成下面的方式，虽然一定能保证功能一致，但变成了最低效的执行方式：
```
await a();
await b();
await c();
await d();
```
因为翻译成回调，就变成了：
```
a(() => {
  b(() => {
    c(() => {
      d();
    });
  });
});
```
然而我们发现，原始代码中，函数 c 可以与 a 同时执行，但 async/await 语法会让我们倾向于在 b 执行完后，再执行 c。
所以当我们意识到这一点，可以优化一下性能：
```
const resA = a();
const resC = c();

await resA;
b();
await resC;
d();
```


但其实这个逻辑也无法达到回调的效果，虽然 a 与 c 同时执行了，但 d 原本只要等待 c 执行完，现在如果 a 执行时间比 c 长，就变成了:

```
a(() => {
  d();
});
```
看来只有完全隔离成两个函数：
```
(async () => {
  await a();
  b();
})();

(async () => {
  await c();
  d();
})();
```


或者利用 Promise.all:
```
async function ab() {
  await a();
  b();
}

async function cd() {
  await c();
  d();
}

Promise.all([ab(), cd()]);
```

作者：黄子毅
链接：https://juejin.im/post/5aefbb046fb9a07ab508cf25
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


