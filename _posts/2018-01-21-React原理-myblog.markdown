---
layout:     post
title:      "React原理"
date:       2018-01-21 23:15:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”

[网页链接](https://github.com/react-guide/react-basic)



## React何以称为React

React虽然并不像Rx.js那样高举Reactive Programming（响应式编程）的大旗，但是依然体现了Reactive Programming的思想。

Reactive Programming通俗说就是这样的编程风格：改变一个东西，另一个东西会做出响应发生改变，而不用我们的Code去主动让另一个东西做出改变。





### 变换（Transformation）

设计 React 的核心前提是认为 UI 只是把数据通过映射关系变换成另一种形式的数据。同样的输入必会有同样的输出。这恰好就是纯函数。
```
function NameBox(name) {
  return { fontWeight: 'bold', labelContent: name };
}
```


```
'Sebastian Markbåge' ->
{ fontWeight: 'bold', labelContent: 'Sebastian Markbåge' };
```

### 抽象（Abstraction）
你不可能仅用一个函数就能实现复杂的 UI。重要的是，你需要把 UI 抽象成多个隐藏内部细节，又可复用的函数。通过在一个函数中调用另一个函数来实现复杂的 UI，这就是抽象。

```
function FancyUserBox(user) {
  return {
    borderStyle: '1px solid blue',
    childContent: [
      'Name: ',
      NameBox(user.firstName + ' ' + user.lastName)
    ]
  };
}
```

```
{ firstName: 'Sebastian', lastName: 'Markbåge' } ->
{
  borderStyle: '1px solid blue',
  childContent: [
    'Name: ',
    { fontWeight: 'bold', labelContent: 'Sebastian Markbåge' }
  ]
};
```


### 组合（Composition）
为了真正达到重用的特性，只重用叶子然后每次都为他们创建一个新的容器是不够的。你还需要可以包含其他抽象的容器再次进行组合。我理解的“组合”就是将两个或者多个不同的抽象合并为一个。
```
function FancyBox(children) {
  return {
    borderStyle: '1px solid blue',
    children: children
  };
}

function UserBox(user) {
  return FancyBox([
    'Name: ',
    NameBox(user.firstName + ' ' + user.lastName)
  ]);
}
```

### 状态（State）
UI 不单单是对服务器端或业务逻辑状态的复制。实际上还有很多状态是针对具体的渲染目标。举个例子，在一个 text field 中打字。它不一定要复制到其他页面或者你的手机设备。滚动位置这个状态是一个典型的你几乎不会复制到多个渲染目标的。

我们倾向于使用不可变的数据模型。我们把可以改变 state 的函数串联起来作为原点放置在顶层。

```
function FancyNameBox(user, likes, onClick) {
  return FancyBox([
    'Name: ', NameBox(user.firstName + ' ' + user.lastName),
    'Likes: ', LikeBox(likes),
    LikeButton(onClick)
  ]);
}

// 实现细节

var likes = 0;
function addOneMoreLike() {
  likes++;
  rerender();
}

// 初始化

FancyNameBox(
  { firstName: 'Sebastian', lastName: 'Markbåge' },
  likes,
  addOneMoreLike
);
```

>注意：本例更新状态时会带来副作用（addOneMoreLike 函数中）。我实际的想法是当一个“update”传入时我们返回下一个版本的状态，但那样会比较复杂。此示例待更新


### Memoization
对于纯函数，使用相同的参数一次次调用未免太浪费资源。我们可以创建一个函数的 memorized 版本，用来追踪最后一个参数和结果。这样如果我们继续使用同样的值，就不需要反复执行它了。
```
function memoize(fn) {
  var cachedArg;
  var cachedResult;
  return function(arg) {
    if (cachedArg === arg) {
      return cachedResult;
    }
    cachedArg = arg;
    cachedResult = fn(arg);
    return cachedResult;
  };
}

var MemoizedNameBox = memoize(NameBox);

function NameAndAgeBox(user, currentTime) {
  return FancyBox([
    'Name: ',
    MemoizedNameBox(user.firstName + ' ' + user.lastName),
    'Age in milliseconds: ',
    currentTime - user.dateOfBirth
  ]);
}
```

### 列表（Lists）
大部分 UI 都是展示列表数据中不同 item 的列表结构。这是一个天然的层级。

为了管理列表中的每一个 item 的 state ，我们可以创造一个 Map 容纳具体 item 的 state。

```
function UserList(users, likesPerUser, updateUserLikes) {
  return users.map(user => FancyNameBox(
    user,
    likesPerUser.get(user.id),
    () => updateUserLikes(user.id, likesPerUser.get(user.id) + 1)
  ));
}

var likesPerUser = new Map();
function updateUserLikes(id, likeCount) {
  likesPerUser.set(id, likeCount);
  rerender();
}

UserList(data.users, likesPerUser, updateUserLikes);
```

>注意：现在我们向 FancyNameBox 传了多个不同的参数。这打破了我们的 memoization 因为我们每次只能存储一个值。更多相关内容在下面。



### 连续性（Continuations）

不幸的是，自从 UI 中有太多的列表，明确的管理就需要大量的重复性样板代码。

我们可以通过推迟一些函数的执行，进而把一些模板移出业务逻辑。比如，使用“柯里化”（JavaScript 中的 bind）。然后我们可以从核心的函数外面传递 state，这样就没有样板代码了。

下面这样并没有减少样板代码，但至少把它从关键业务逻辑中剥离。
```
function FancyUserList(users) {
  return FancyBox(
    UserList.bind(null, users)
  );
}

const box = FancyUserList(data.users);
const resolvedChildren = box.children(likesPerUser, updateUserLikes);
const resolvedBox = {
  ...box,
  children: resolvedChildren
};
```

### State Map
之前我们知道可以使用组合避免重复执行相同的东西这样一种重复模式。我们可以把执行和传递 state 逻辑挪动到被复用很多的低层级的函数中去。
```
function FancyBoxWithState(
  children,
  stateMap,
  updateState
) {
  return FancyBox(
    children.map(child => child.continuation(
      stateMap.get(child.key),
      updateState
    ))
  );
}

function UserList(users) {
  return users.map(user => {
    continuation: FancyNameBox.bind(null, user),
    key: user.id
  });
}

function FancyUserList(users) {
  return FancyBoxWithState.bind(null,
    UserList(users)
  );
}

const continuation = FancyUserList(data.users);
continuation(likesPerUser, updateUserLikes);
```

### Memoization Map
一旦我们想在一个 memoization 列表中 memoize 多个 item 就会变得很困难。因为你需要制定复杂的缓存算法来平衡调用频率和内存占有率。

还好 UI 在同一个位置会相对的稳定。相同的位置一般每次都会接受相同的参数。这样以来，使用一个集合来做 memoization 是一个非常好用的策略。

我们可以用对待 state 同样的方式，在组合的函数中传递一个 memoization 缓存。
```
function memoize(fn) {
  return function(arg, memoizationCache) {
    if (memoizationCache.arg === arg) {
      return memoizationCache.result;
    }
    const result = fn(arg);
    memoizationCache.arg = arg;
    memoizationCache.result = result;
    return result;
  };
}

function FancyBoxWithState(
  children,
  stateMap,
  updateState,
  memoizationCache
) {
  return FancyBox(
    children.map(child => child.continuation(
      stateMap.get(child.key),
      updateState,
      memoizationCache.get(child.key)
    ))
  );
}

const MemoizedFancyNameBox = memoize(FancyNameBox);
```

### 代数效应（Algebraic Effects）
多层抽象需要共享琐碎数据时，一层层传递数据非常麻烦。如果能有一种方式可以在多层抽象中快捷地传递数据，同时又不需要牵涉到中间层级，那该有多好。React 中我们把它叫做“context”。

有时候数据依赖并不是严格按照抽象树自上而下进行。举个例子，在布局算法中，你需要在实现他们的位置之前了解子节点的大小。

现在，这个例子有一点超纲。我会使用 代数效应 这个由我发起的 ECMAScript 新特性提议。如果你对函数式编程很熟悉，它们 在避免由 monad 强制引入的仪式一样的编码。
```
function ThemeBorderColorRequest() { }

function FancyBox(children) {
  const color = raise new ThemeBorderColorRequest();
  return {
    borderWidth: '1px',
    borderColor: color,
    children: children
  };
}

function BlueTheme(children) {
  return try {
    children();
  } catch effect ThemeBorderColorRequest -> [, continuation] {
    continuation('blue');
  }
}

function App(data) {
  return BlueTheme(
    FancyUserList.bind(null, data.users)
  );
}
```



### 重新渲染

* 当 state 或者 prop 发生变化的时候，组件就会重渲染自己
* **当组件的父组件重渲染时，组件也会重渲染自己**



### **Capture Value**

[useEffect 完全指南](https://github.com/ascoders/weekly/blob/master/%E5%89%8D%E6%B2%BF%E6%8A%80%E6%9C%AF/96.%E7%B2%BE%E8%AF%BB%E3%80%8AuseEffect%20%E5%AE%8C%E5%85%A8%E6%8C%87%E5%8D%97%E3%80%8B.md)

每次 Render 都有自己的 Props 与 State.

**可以认为每次 Render 的内容都会形成一个快照并保留下来，因此当状态变更而 Rerender 时，就形成了 N 个 Render 状态，而每个 Render 状态都拥有自己固定不变的 Props 与 State。(闭包)**

---

**由于 useEffect 符合 Capture Value 的特性，不要对useEffect Dependencies 撒谎。**





### 事件机制原理

[https://toutiao.io/posts/28of14w/preview](https://toutiao.io/posts/28of14w/preview)

react自身实现了一套自己的事件机制，包括事件注册、事件的合成、事件冒泡、事件派发等，虽然和原生的是两码事，但也是基于浏览器的事件机制下完成的。

**我们都知道react 的所有事件并没有绑定到具体的dom节点上而是绑定在了document 上，然后由统一的事件处理程序来处理，同时也是基于浏览器的事件机制（冒泡），所有节点的事件都会在 document 上触发。**



#### 优点

1. 性能优化：DOM 事件处理函数会在每次事件触发时创建一个新的函数实例，如果有大量的 DOM 事件处理函数，会造成大量的函数对象的创建和销毁，导致内存消耗和性能下降。而 React 的合成事件系统会通过事件委托的方式，将事件绑定到顶层的 document 或者最近的 DOM 节点上，所有的事件都会通过一个统一的事件处理函数进行处理，减少了内存消耗和事件绑定的数量，提升了性能。
2. 跨浏览器兼容性：React 的合成事件系统提供了对浏览器差异性的统一处理，使开发者不需要关心不同浏览器的兼容性问题。React 会根据浏览器的类型和版本，自动选择最佳的方式来创建事件对象，并提供一致的接口给开发者使用。
3. 事件池管理：React 的合成事件系统利用了事件池管理的概念，将事件对象用过后会进行重置和复用，减少了垃圾回收的压力，提升了性能。在事件处理函数中，获取到的事件对象是一个合成事件对象，而非原生的事件对象。





#### 合成和原生事件

如果一个节点上同时绑定了合成和原生事件，那么禁止冒泡后执行关系是怎样的呢？

因为合成事件的触发是基于浏览器的事件机制来实现的，通过冒泡机制冒泡到最顶层元素，然后再由 dispatchEvent统一去处理。

得出的结论：

* **原生事件阻止冒泡肯定会阻止合成事件的触发。**
* **合成事件的阻止冒泡不会影响原生事件。**



解释：

浏览器事件的执行需要经过三个阶段，捕获阶段-目标元素阶段-冒泡阶段。

节点上的原生事件的执行是在目标阶段，然而合成事件的执行是在冒泡阶段，所以原生事件会先合成事件执行，然后再往父节点冒泡。

既然原生都阻止冒泡了，那合成还执行个啥嘞。

好，轮到合成的被阻止冒泡了，那原生会执行吗？当然会了。

因为原生的事件先于合成的执行，所以合成事件内阻止的只是合成的事件冒泡。

* 原生事件（阻止冒泡）会阻止合成事件的执行
* 合成事件（阻止冒泡）不会阻止原生事件的执行

两者最好不要混合使用，避免出现一些奇怪的问题



#### 思考

```js
handleClick = (e) => {
    console.log('react click',e)
}
```

上面代码是给一个元素添加 click事件的回调方法,方法中的参数 e，其实不是原生事件对象而是react包装过的对象，同时原生事件对象被放在了属性 e.nativeEvent内。



#### 事件注册机制

react 事件注册过程其实主要做了2件事：事件注册、事件存储。

* 事件注册 - 组件挂载阶段，根据组件内的声明的事件类型-onclick，onchange 等，给 document 上添加事件 -addEventListener，并指定统一的事件处理程序 dispatchEvent。
* 事件存储 - 就是把 react 组件内的所有事件统一的存放到一个对象里，缓存起来，为了在触发事件的时候可以查找到对应的方法去执行。





### 错误捕获机制



#### Error Boundary（错误边界）

[https://zh-hans.reactjs.org/docs/error-boundaries.html#introducing-error-boundaries](https://zh-hans.reactjs.org/docs/error-boundaries.html#introducing-error-boundaries)

部分 UI 的 JavaScript 错误不应该导致整个应用崩溃，为了解决这个问题，React 16 引入了一个新的概念 —— 错误边界。



错误边界是一种 React 组件，这种组件**可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI**，而不是渲染那些崩溃了的子组件树。错误边界在渲染期间、生命周期方法和整个组件树的构造函数中捕获错误。





[https://mp.weixin.qq.com/s/2ZYz0dFWpa5dW3p7gjaqsA](https://mp.weixin.qq.com/s/2ZYz0dFWpa5dW3p7gjaqsA)

为了实现这个特性，就一定需要捕获到错误。

所以在`React`源码中，所有`用户代码`都被包裹在一个方法中执行。



类似如下：

```jsx
function wrapper(func) {
  try {
    func();
  } catch(e) {
    // ...处理错误
  }
}
```

本来一切都很完美，但是`React`作为世界级前端框架，受众广泛，凡事都讲究做到极致。

这不，有人提issue：

> 你们这样在`try catch`中执行`用户代码`会让浏览器调试工具的`Pause on exceptions`失效。



对用户来说，我写在`componentDidMount`中的代码明明未捕获错误，可是错误发生时`Pause on exceptions`却失效了，确实有些让人困惑。

所以，在生产环境，`React`继续使用`try catch`实现`wrapper`。

而在开发环境，为了更好的调试体验，需要重新实现一套`try catch`机制，包含如下功能：

- 捕获`用户代码`抛出的错误，使`Error Boundary`功能正常运行
- 不捕获`用户代码`抛出的错误，使`Pause on exceptions`不失效



#### 捕获用户代码抛出的错误

但是不能使用`try catch`，因为这会让`Pause on exceptions`失效。

解决办法是：监听`window`的`error`事件。



根据**GlobalEventHandlers.onerror MDN**[1]，该事件可以监听到两类错误：

- js运行时错误（包括语法错误）。`window`会触发`ErrorEvent`接口的`error`事件
- 资源（如`<img>`或`<script>`）加载失败错误。加载资源的元素会触发`Event`接口的`error`事件，可以在`window`上捕获该错误



实现开发环境使用的`wrapperDev`：

```jsx
// 开发环境wrapper
function wrapperDev(func) {
  function handleWindowError(error) {
    // 收集错误交给Error Boundary处理
  }

  window.addEventListener('error', handleWindowError);
  func();
  window.removeEventListener('error', handleWindowError);
}

```

开发环境`func`内如果抛出错误，代码的执行会中断。

比如执行如下代码，`finish`会被打印。

```js
wrapperPrd(() => {throw Error(123)})
console.log('finish');
```

但是执行如下代码，代码执行中断，`finish`不会被打印。

```js
wrapperDev(() => {throw Error(123)})
console.log('finish');
```

如何在不捕获`用户代码`抛出错误的前提下，又能让后续代码的执行不中断呢？



#### 如何让代码执行不中断

答案是：通过`dispatchEvent`触发`事件回调`，在`回调`中调用`用户代码`。





根据**EventTarget.dispatchEvent MDN**[2]：

不同于`DOM`节点触发的事件（比如`click`事件）回调是由`event loop`异步触发。

通过`dispatchEvent`触发的事件是同步触发，并且在事件回调中抛出的`错误`不会影响`dispatchEvent`的调用者（caller）。

让我们继续改造`wrapperDev`。

首先创建虚构的`DOM`节点、事件对象、虚构的事件类型：

```js
// 创建虚构的DOM节点
const fakeNode = document.createElement('fake');
// 创建event
const event = document.createEvent('Event');
// 创建虚构的event类型
const evtType = 'fake-event';

```

初始化事件对象，监听事件。在事件回调中调用`用户代码`。触发事件：

```js
function wrapperDev(func) {
  function handleWindowError(error) {
    // 收集错误交给Error Boundary处理
  }
  
  function callCallback() {
    fakeNode.removeEventListener(evtType, callCallback, false); 
    func();
  }
  
  const event = document.createEvent('Event');
  const fakeNode = document.createElement('fake');
  const evtType = 'fake-event';

  window.addEventListener('error', handleWindowError);
  fakeNode.addEventListener(evtType, callCallback, false);

  event.initEvent(evtType, false, false);
  

  fakeNode.dispatchEvent(event);
  
  window.removeEventListener('error', handleWindowError);
}
```



### render函数

> react16中 render函数允许返回一个数组，单个字符串等，不在只限制为一个顶级DOM节点，可以减少很多不必要的div

render函数会插入jsx生成的dom结构，react会生成一份虚拟dom树，在每一次组件更新时，在此react会通过其diff算法比较更新前后的新旧DOM树，比较以后，找到**最小的有差异的DOM节点**，并重新渲染



意思你现在可以这样：

```tsx
render () {
  return " "
}
```

或者这样：

```tsx
render () {
  return [
    <div></div>
    <div></div>
    ]
}
```





### 时间切片原理

[时间切片原理](https://react.iamkasong.com/concurrent/scheduler.html#%E6%97%B6%E9%97%B4%E5%88%87%E7%89%87%E5%8E%9F%E7%90%86)

`Scheduler`的`时间切片`功能是通过`task`（宏任务）实现的。

最常见的`task`当属`setTimeout`了。但是有个`task`比`setTimeout`执行时机更靠前，那就是[MessageChannel](https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel)。

所以`Scheduler`将需要被执行的回调函数作为`MessageChannel`的回调执行。如果当前宿主环境不支持`MessageChannel`，则使用`setTimeout`。







### 父子组件更新

[https://www.jianshu.com/p/ee122bb5b14b](https://www.jianshu.com/p/ee122bb5b14b)

父子组件 componentWillMount生命周期是先进入父组件还是子组件？
componentDidMount呢？

在react的组件挂载及render过程中，最底层的子组件是最先完成挂载及更新的。



**constructor()构造函数、componentWillMount执行顺序：**
顶层父组件--子组件--子组件--...--底层子组件



**render、componentDidMount顺序：**
底层子组件--子组件--子组件--...--顶层父组件





### setState 同步还是异步

[https://zhuanlan.zhihu.com/p/350332132](https://zhuanlan.zhihu.com/p/350332132)

只要你进入了 `react` 的调度流程，那就是异步的。只要你没有进入 `react` 的调度流程，那就是同步的。什么东西不会进入 `react` 的调度流程？ `setTimeout` `setInterval` ，直接在 `DOM` 上绑定原生事件等。这些都不会走 `React` 的调度流程，你在这种情况下调用 `setState` ，那这次 `setState` 就是同步的。 否则就是异步的。



### 为什么 React 的 Diff 不采用双端对比

[https://juejin.cn/post/7116141318853623839](https://juejin.cn/post/7116141318853623839)





### 不需要 useMemo 和 useCallback

[为什么你可以删除 90% 的 useMemo 和 useCallback ？](https://juejin.cn/post/7251802404877893689?utm_source=gold_browser_extension)

* `useCallback` 和 `useMemo` 仅仅在后续渲染（也就是重渲染）中起作用，在初始渲染中它们反而是有害的

* `useCallback` 和 `useMemo` 作用于 `props` 并不能避免组件重渲染。只有当每一个 `prop` 都被缓存，且组件本身也被缓存的情况下，重渲染才能被避免。只要有一丁点疏忽，那么你做的一切努力就打水漂了。所以说，简单点，把它们都删了吧。

- 把包裹了“纯 js 操作“的 `useMemo` 也都删了吧。与组件本身的渲染相比，它缓存数据带来的耗时减少是微不足道的，并且会在初始渲染时消耗额外的内存，造成可以被观察到的延迟。



### JSX

[https://www.typescriptlang.org/docs/handbook/jsx.html](https://www.typescriptlang.org/docs/handbook/jsx.html)

[JSX](https://facebook.github.io/jsx/) is an embeddable XML-like syntax. It is meant to be transformed into valid JavaScript, though the semantics of that transformation are implementation-specific. JSX rose to popularity with the [React](https://reactjs.org/) framework, but has since seen other implementations as well. TypeScript supports embedding, type checking, and compiling JSX directly to JavaScript.

