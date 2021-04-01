---
layout:     post
title:      "React Hooks相关"
date:       2019-11-11 11:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”

# 正文

[https://react.docschina.org/docs/hooks-intro.html](https://react.docschina.org/docs/hooks-intro.html)



**Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。**





**React 需要为共享状态逻辑提供更好的原生途径**



你可以使用 Hook 从组件中提取状态逻辑，使得这些逻辑可以单独测试并复用。**Hook 使你在无需修改组件结构的情况下复用状态逻辑。**



在多数情况下，不可能将组件拆分为更小的粒度，因为状态逻辑无处不在。这也给测试带来了一定挑战。同时，这也是很多人将 React 与状态管理库结合使用的原因之一。但是，这往往会引入了很多抽象概念，需要你在不同的文件之间来回切换，使得复用变得更加困难。

为了解决这个问题，**Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分**,你还可以使用 reducer 来管理组件的内部状态，使其更加可预测。

## Hook 使用规则

Hook 就是 JavaScript 函数，但是使用它们会有两个额外的规则：



* **只能在函数最外层调用 Hook**。不要在循环、条件判断或者子函数中调用。
* **只能在 React 的函数组件中调用 Hook**。不要在其他 JavaScript 函数中调用。（还有一个地方可以调用 Hook —— 就是自定义的 Hook 中，我们稍后会学习到。）







## useMemo 和 useCallback

https://zhuanlan.zhihu.com/p/56975681

`useCallback(fn, inputs)` === `useMemo(() => fn, inputs)`





有些人可能会误以为 `useCallback` 可以用来解决创建函数造成的性能问题，其实恰恰相反，单从这个组件看的话 `useCallback` 只会更慢，因为 inline 函数是无论如何都会创建的，还增加了 `useCallback` 内部对 inputs 变化的检测。



```js
function A() {
  // ...
  const cb = () => {}/* 创建了 */;
}

function B() {
  // ...
  const cb = React.useCallback(() => {}/* 还是创建了 */, [a, b]);
}
```


























