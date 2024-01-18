---
layout:     post
title:      "rxjs相关"
date:       2022-01-13 17:32:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”
>



# 正文

[探索 RxJS - Observable](https://github.com/ecmadao/Coding-Guide/blob/master/Notes/RxJS/%E6%8E%A2%E7%B4%A2RxJS-Observable.md)

[80 行代码实现简易 RxJS](https://mp.weixin.qq.com/s/ECI3tniwucE2Tpv4eWw7iA)

RxJS 是一个响应式的库，它接收从事件源发出的一个个事件，经过处理管道的层层处理之后，传入最终的接收者，这个处理管道是由操作符组成的，**开发者只需要选择和组合操作符就能完成各种异步逻辑，极大简化了异步编程**。除此以外，RxJS 的设计还遵循了函数式、流的理念。





响应式:

因为是对事件源做监听和一系列处理的，这种编程模式就叫做响应式。

函数式:

因为每一步 operator 都是纯函数，返回一个新的 Observable，这符合函数式的不可变，修改后返回一个新的的理念。

流:

因为一个个事件是动态产生和传递的，这种数据的动态产生和传递就可以叫做流。





## 使用 RxJS

```tsx
const source = new Observable((observer) => {
    let i = 0;
    const timer = setInterval(() => {
        observer.next(++i);
    }, 1000);
    return function unsubscribe() {
        clearInterval(timer);
    };
});
const subscription = source.pipe(
    map((i) => ++i),
    map((i) => i * 10)
).subscribe({
    next: (v) => console.log(v),
    error: (err) => console.error(err),
    complete: () => console.log('complete'),
});

setTimeout(() => {
    subscription.unsubscribe();
}, 4500);

```



## 监听对象set

```ts
import { Subject } from 'rxjs';

const subject = new Subject()
const obj = {
  aaa: "aaa"
}
const handler = {
  set(target: any, key: string, value: any) {
    target[key] = value;
    subject.next(target);
    return true;
  },
};
const proxy = new Proxy(obj, handler);

subject.subscribe((val) => {
  console.log("subject", JSON.stringify(val))
})

proxy.aaa = "asdads"
proxy.ccc = "ccc"
```

