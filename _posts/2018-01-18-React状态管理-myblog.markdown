---
layout:     post
title:      "react状态管理"
date:       2018-01-18 16:05:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”



# Redux

[网页链接](http://www.redux.org.cn/)

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

可以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。

Redux 除了和 React 一起用外，还支持其它界面库。
它体小精悍（只有2kB）且没有任何依赖。

应用中所有的 state 都以一个对象树的形式储存在一个单一的 store 中。
惟一改变 state 的办法是触发 action，一个描述发生什么的对象。
为了描述 action 如何改变 state 树，你需要编写 reducers。

```js
import { createStore } from 'redux';

/**
 * 这是一个 reducer，形式为 (state, action) => state 的纯函数。
 * 描述了 action 如何把 state 转变成下一个 state。
 *
 * state 的形式取决于你，可以是基本类型、数组、对象、
 * 甚至是 Immutable.js 生成的数据结构。惟一的要点是
 * 当 state 变化时需要返回全新的对象，而不是修改传入的参数。
 *
 * 下面例子使用 `switch` 语句和字符串来做判断，但你可以写帮助类(helper)
 * 根据不同的约定（如方法映射）来判断，只要适用你的项目即可。
 */
function counter(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1;
  case 'DECREMENT':
    return state - 1;
  default:
    return state;
  }
}

// 创建 Redux store 来存放应用的状态。
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 惟一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch({ type: 'INCREMENT' });
// 1
store.dispatch({ type: 'INCREMENT' });
// 2
store.dispatch({ type: 'DECREMENT' });
// 1
```


你应该把要做的修改变成一个普通对象，这个对象被叫做 action，而不是直接修改 state。然后编写专门的函数来决定每个 action 如何改变应用的 state，这个函数被叫做 reducer。

如果你以前使用 Flux，那么你只需要注意一个重要的区别。Redux 没有 Dispatcher 且不支持多个 store。相反，只有一个单一的 store 和一个根级的 reduce 函数（reducer）。随着应用不断变大，你应该把根级的 reducer 拆成多个小的 reducers，分别独立地操作 state 树的不同部分，而不是添加新的 stores。这就像一个 React 应用只有一个根级的组件，这个根组件又由很多小组件构成。



## 三大原则

* 单一数据源 （整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。）
* State 是只读的。惟一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。
* 使用纯函数来执行修改。为了描述 action 如何改变 state tree ，你需要编写 reducers。



## Provider组件

connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。

一种解决方法是将state对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将state传下去就很麻烦。

React-Redux 提供Provider组件，可以让容器组件拿到state。


```tsx
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

上面代码中，Provider在根组件外面包了一层，这样一来，App的所有子组件就默认都可以拿到state了。

[http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html)



## reducer

reducer为什么叫reducer

我们注意到redux的官方文档里专门有一句对reducer命名的解释：

It's called a reducer because it's the type of function you would pass to Array.prototype.reduce(reducer, ?initialValue)

中文版的文档把这一句话翻译成了：

之所以称作 reducer 是因为它将被传递给 Array.prototype.reduce(reducer, ?initialValue) 方法

直观起见，我们还是拿JavaScript来理解。reduce属于一种高阶函数，它将其中的回调函数reducer递归应用到数组的所有元素上并返回一个独立的值。这也就是“缩减”或“折叠”的意义所在了。

**总而言之一句话，redux当中的reducer之所以叫做reducer，是因为它和 Array.prototype.reduce 当中传入的回调函数非常相似。**



## applyMiddlewares

applyMiddlewares这个方法到底是干什么的？

它是 Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行

```js
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {
    var store = createStore(reducer, preloadedState, enhancer);
    var dispatch = store.dispatch;
    var chain = [];

    var middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    };
    chain = middlewares.map(middleware => middleware(middlewareAPI));
    dispatch = compose(...chain)(store.dispatch);

    return {...store, dispatch}
  }
}
```

上面代码中，所有中间件被放进了一个数组chain，然后嵌套执行，最后执行store.dispatch。可以看到，中间件内部（middlewareAPI）可以拿到getState和dispatch这两个方法。



## bindActionCreators

[https://www.reduxjs.cn/api/bindactioncreators/](https://www.reduxjs.cn/api/bindactioncreators/)

把一个 value 为不同 [action creator](https://www.reduxjs.cn/understanding/thinking-in-redux/glossary#action-creator) 的对象，转成拥有同名 key 的对象。同时使用 [`dispatch`](https://www.reduxjs.cn/api/store#dispatchaction) 对每个 action creator 进行包装，以便可以直接调用它们。



## connect

React-Redux 提供connect方法，


connect的作用就是将React和Redux中的store连接起来


1. 接收一个组件，将Redux中的store通过props传递给组件
2. 将Redux中的action也通过props传递给组件
3. 返回一个新的组件，这个组件可以通过this.props访问到store中的属性，也可能是触发action
4. 当store中的数据发生变化的时候，通知新的组件



```tsx
import { connect } from 'react-redux'
const VisibleTodoList = connect()(TodoList);
```


```tsx
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```

上面代码中，connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。

### mapStateToProps

mapStateToProps(state, ownProps) : stateProps

**表示：哪些 Redux 全局的 state 是我们组件想要通过 props 获取的？**

它是一个函数。它的作用就是像它的名字那样，建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系。

作为函数，mapStateToProps执行后应该返回一个对象，里面的每一个键值对就是一个映射。请看下面的例子。

```
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
```

>connect方法可以省略mapStateToProps参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

### mapDispatchToProps

mapDispatchToProps(dispatch, [ownProps])

**代表：哪些 action 创建函数是我们想要通过 props 获取的？**

mapDispatchToProps是connect函数的第二个参数，用来建立 UI 组件的参数到store.dispatch方法的映射。也就是说，它定义了哪些用户的操作应该当作 Action，传给 Store。它可以是一个函数，也可以是一个对象。

如果mapDispatchToProps是一个函数，会得到dispatch和ownProps（容器组件的props对象）两个参数。

```
const mapDispatchToProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```

从上面代码可以看到，mapDispatchToProps作为函数，应该返回一个对象，该对象的每个键值对都是一个映射，定义了 UI 组件的参数怎样发出 Action。

如果mapDispatchToProps是一个对象，它的**每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，返回的 Action 会由 Redux 自动发出。**举例来说，上面的mapDispatchToProps写成对象就是下面这样。

```
const mapDispatchToProps = {
  onClick: (filter) => {
    type: 'SET_VISIBILITY_FILTER',
    filter: filter
  };
}
```

### 结合decorator

们常见的写法，也就是ES7的decorator装饰器，也就是装饰者模式，这种写法比较简便：

```tsx
@connect(
  state=>({ num: state}),
  { add, remove, addAsync }
)
class App extends React.Component{
  render(){
    return (
      <div>
        <h2>现在有物品{this.props.num}件</h2>
        <button onClick={this.props.add}>add</button>
        <button onClick={this.props.remove}>remove</button>
        <button onClick={this.props.addAsync}>addAsync</button>
      </div>
    )
  }
}
export default App;
```

或者是更容易理解的高阶函数的写法：

```tsx
class App extends React.Component{
  render(){
    return (
      <div>
        <h2>现在有物品{this.props.num}件</h2>
        <button onClick={this.props.add}>add</button>
        <button onClick={this.props.remove}>remove</button>
        <button onClick={this.props.addAsync}>addAsync</button>
      </div>
    )
  }
}
App = connect(
    state => ({num: state),
    { add, remove, addAsync }
)(App)
```





## redux-thunk

http://www.redux.org.cn/docs/advanced/AsyncActions.html

Action 发出以后，Reducer 立即算出 State，这叫做同步；Action 发出以后，过一段时间再执行 Reducer，这就是异步。

怎么才能 Reducer 在异步操作结束后自动执行呢？这就要用到新的工具：中间件（middleware）。

>**中间件:它提供的是位于 action 被发起之后，到达 reducer 之前的扩展点。**

redux-thunk中间件可以让action创建函数先不返回一个action对象，而是返回一个函数，函数传递两个参数(dispatch,getState),在函数体内进行业务逻辑的封装





## react-redux

在react里面用redux，一般我们会用到react-redux，其中包括Provider和connect接口

Provider接收redux的createStore()的结果，并且放到context里，让子组件可以通过context属性直接获取到这个
createStore的结果，这个createStore的结果是啥呢，如下几个函数:
```
return {
        //真正的返回，执行createStore其实返回的就是这些东东
        dispatch,       //触发action去执行reducer，更新state
        subscribe,     //订阅state改变，state改变时会执行subscribe的参数（自己定义的一个函数）
        getState,      //获取state树
        replaceReducer,       //替换reducer
 }
```

而connect，接收到mapStateToProps，会在内部subscribe全局state的改变，来判断props是否更改，如果需要更新，才触发更新。

**react-redux就是不需要你自己去subscribe全局state的变化，以及去getState，还有判断组件是否需要更新。也是它存在的意义**



React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。

|                | 容器组件              | 展示组件              |
| -------------- | --------------------- | --------------------- |
| Location       | 最顶层，路由处理      | 中间和子组件          |
| Aware of Redux | 是                    | 否                    |
| 读取数据       | 从 Redux 获取 state   | 从 props 获取数据     |
| 修改数据       | 向 Redux 派发 actions | 从 props 调用回调函数 |

## redux-undo

redux-undo是一个reducer增强组件,它提供了undoable函数,这个函数接收已经存在的reducer和配置对象,使用undo函数增强已经存在的reducer.

```js
 // Redux utility functions 
import { combineReducers } from 'redux';
// redux-undo higher-order reducer 
import undoable from 'redux-undo';

combineReducers({
  counter: undoable(counter)
})
```

包装你的reducer 像这样

```js
 {
  past: [...pastStatesHere...],
  present: {...currentStateHere...},
  future: [...futureStatesHere...]
}
```

**现在你必须使用state.present获取当前的state
获取所有过去的state使用state.past.**

**Undo/Redo**

```js
store.dispatch(ActionCreators.undo()) // undo the last action  (撤销 last action)
store.dispatch(ActionCreators.redo()) // redo the last action  (再次执行 last action)
 
store.dispatch(ActionCreators.jumpToPast(index)) // jump to requested index in the past[] array 
store.dispatch(ActionCreators.jumpToFuture(index)) // jump to requested index in the future[] array
```

**配置**

配置对象传递给undoable()(值是默认值)

```js
undoable(reducer, {
  limit: false, // set to a number to turn on a limit for the history 
 
  filter: () => true, // see `Filtering Actions` section 
 
  undoType: ActionTypes.UNDO, // define a custom action type for this undo action 
  redoType: ActionTypes.REDO, // define a custom action type for this redo action 
 
  jumpToPastType: ActionTypes.JUMP_TO_PAST, // define custom action type for this jumpToPast action 
  jumpToFutureType: ActionTypes.JUMP_TO_FUTURE, // define custom action type for this jumpToFuture action 
 
  initialState: undefined, // initial state (e.g. for loading) 
  initTypes: ['@@redux/INIT', '@@INIT'] // history will be (re)set upon init action type 
  initialHistory: { // initial history (e.g. for loading) 
    past: [],
    present: config.initialState,
    future: []
  },
 
  debug: false, // set to `true` to turn on debugging 
})
```



过滤Actions

如果你不想包含每一步的action,可以传递一个函数到undoable

```js
undoable(reducer, function filterActions(action, currentState, previousState) {
  return action.type === SOME_ACTION; // only add to history if action is SOME_ACTION只有some_action的action才能记录 
})
 
// or you could do... 
 
undoable(reducer, function filterState(action, currentState, previousState) {
  return currentState !== previousState; // only add to history if state changed只有state变化的才能记录重做 
})
```

或者你可以使用distinctState,includeAction,excludeAction助手函数

```js
import undoable, { distinctState, includeAction, excludeAction } from 'redux-undo';
```

现在你可以使用助手函数了,相当简单

```js
undoable(reducer, { filter: includeAction(SOME_ACTION) })
undoable(reducer, { filter: excludeAction(SOME_ACTION) })
 
// or you could do... 
 
undoable(reducer, { filter: distinctState() })
```

甚至还支持数组

```js
undoable(reducer, { filter: includeAction([SOME_ACTION, SOME_OTHER_ACTION]) })
undoable(reducer, { filter: excludeAction([SOME_ACTION, SOME_OTHER_ACTION]) })
```







## **redux-toolkit**

[https://redux-toolkit.js.org/introduction/getting-started](https://redux-toolkit.js.org/introduction/getting-started)

1. **简化配置**：
   - Redux Toolkit 提供了一个 `configureStore` 函数，用于创建 Redux store。这使得配置中间件（如 Redux Thunk）、整合 reducers 更加方便。
2. **Slice 概念**：
   - Slice 是 Redux Toolkit 的核心概念，通过 `createSlice` 方法可以创建 reducer 和 actions，自动将两者关联。每个 slice 管理一部分 Redux state，这种划分使得状态和逻辑更易于管理。
3. **自动生成的 Actions 和 Reducers**：
   - 使用 `createSlice` 时，Redux Toolkit 会根据定义的 reducers 自动生成对应的 action creators。这样开发者可以直接使用这些 action，而不需要手动定义。
4. **Immutable 更新**：
   - **Redux Toolkit 使用了 Immer 库来简化 state 的不可变更新。在 reducers 中，开发者可以直接“修改” state，实际上 Immer 会处理不可变更新。**
5. **批量 Dispatch**：
   - Redux Toolkit 允许在一个 dispatch 中批量处理多个 action，提高了性能和代码的清晰度。
6. **内置的开发者工具支持**：
   - 通过 `configureStore` 创建的 store 默认集成了 Redux DevTools，方便开发者调试。
7. **异步操作**：
   - Redux Toolkit 提供 `createAsyncThunk` 来处理异步操作，它简化了处理异步请求的流程，并且自动处理了 loading 和 error 状态。





### in next.js

[https://redux-toolkit.js.org/usage/nextjs](https://redux-toolkit.js.org/usage/nextjs)

```tsx
import { configureStore } from '@reduxjs/toolkit'

export const makeStore = () => {
  return configureStore({
    reducer: {},
  })
}

// Infer the type of makeStore
export type AppStore = ReturnType<typeof makeStore>
// Infer the `RootState` and `AppDispatch` types from the store itself
export type RootState = ReturnType<AppStore['getState']>
export type AppDispatch = AppStore['dispatch']

```

We don't have a `store` variable exported, but we can infer the `RootState` and `AppDispatch` types from the return type of `makeStore`.

---

- **Create a Redux store per request by using `configureStore` wrapped in a `makeStore` function**
- **Provide the Redux store to the React application components using a "client" component**
- **Only interact with the Redux store in client components** because only client components have access to React context
- **Use the store as you normally would using the hooks provided in React-Redux**
- **You need to account for the case where you have per-route state in a global store located in the layout**





### 

# MobX

https://zh.mobx.js.org/README.html

[https://coggle.it/diagram/XfXJw2j7x59QruDW/t/mobx](https://coggle.it/diagram/XfXJw2j7x59QruDW/t/mobx)



## observe

[observe 用法](https://cn.mobx.js.org/refguide/observe.html#observe)

该函数返回一个 `disposer` 函数，当调用时可以取消观察者。 注意，`transaction` 不影响 `observe` 方法工作。 意味着即使在一个 `transaction` 中，`observe` 也会触发每个变化的监听器。 因此，`autorun` 通常是一个更强大的和更具声明性的 `observe` 替代品。





## reaction

[https://cn.mobx.js.org/refguide/reaction.html](https://cn.mobx.js.org/refguide/reaction.html)

粗略地讲，reaction 是 `computed(expression).observe(action(sideEffect))` 或 `autorun(() => action(sideEffect)(expression))` 的语法糖。





## MobXProviderContext

为什么需要MobXProviderContext？不可以用React 的 Context吗？

[https://github.com/mobxjs/mobx-react/issues/689](https://github.com/mobxjs/mobx-react/issues/689)

The `MobXProviderContext` is only useful for code using `inject` and migrating to hooks. That way you can mix both in a single app and share the same context. If you have your own Context, you can forget about the one exported from the package :)









### reaction 只执行一次

```tsx
const counter = observable({ count: 0 });

// 只调用一次并清理掉 reaction : 对 observable 值作出反应。
const reaction3 = reaction(
    () => counter.count,
    (count, reaction) => {
        console.log("reaction 3: invoked. counter.count = " + count);
        reaction.dispose();
    }
);

counter.count = 1;
// 输出:
// reaction 3: invoked. counter.count = 1

counter.count = 2;
// 输出:
// (There are no logging, because of reaction disposed. But, counter continue reaction)

console.log(counter.count);
// 输出:
// 2

```





## mobx-react

[源码地址](https://github.com/mobxjs/mobx/blob/main/packages/mobx-react/src/index.ts)

Package with React component wrapper for combining React with MobX



###  observer

[observer 源码](https://github.com/mobxjs/mobx/blob/main/packages/mobx-react/src/observer.tsx)

使用了高阶组件的模式，并在内部封装了React.memo，将传入的函数组件转换为监听者。





## mobx-react-lite

而mobx-react-lite是一个轻量级的实现，为了更好地支持React的函数组件。

**它依赖于React版本^16.8.0，并且只支持React的函数组件。**



## mobx-keystone

[https://mobx-keystone.js.org/](https://mobx-keystone.js.org/)

`mobx-keystone` is a state container that combines the *simplicity and ease of mutable data* with the *traceability of immutable data* and the *reactiveness and performance of observable data*, all with a fully compatible TypeScript syntax.



## extendObservable

[https://cn.mobx.js.org/refguide/extend-observable.html#extendobservable](https://cn.mobx.js.org/refguide/extend-observable.html#extendobservable)

ExtendObservable 用来向已存在的目标对象添加 observable 属性。 属性映射中的所有键值对都会导致目标上的新的 observable 属性被初始化为给定值。 属性映射中的任意 getters 都会转化成计算属性。

```
extendObservable(target, properties, decorators?, options?)
```





## mobx-react-lite 

Lightweight React bindings for MobX based on React 16.8 and Hooks

它是专门服务于react hooks的mobx-react轻量级版本

虽然mobx-react@6已经包含了mobx-react-lite，但官方是推荐在没有类组件的react项目中直接使用mobx-react-lite的。





## mobx-state-tree

> 如果想要在 MobX 上实现时间旅行，建议使用 MobX-state-tree

Technically speaking, mobx-state-tree (also known as MST) is a state container system built on [MobX](https://github.com/mobxjs/mobx), a functional reactive state library.





## 其他

### 配置babel

```tsx

"presets": [
	// ...
    ],
"plugins": [
    ["@babel/plugin-proposal-decorators", {"legacy": true }],
    ["@babel/plugin-proposal-class-properties", { "loose" : true }]
```





### **mobx6.0为什么移除装饰器**

https://www.yuque.com/mosinng/blog/gmq6ra

主要的原因是跟随js的class语言标准不兼容才迫不得已修改的

在 Mobx 6 以前，一直提倡的是装饰器语法，但是自从 Mobx 6 开始不再建议使用装饰器，以便最大程度的贴近 JS 标准



**TIP：这个可能到导致mobx 6 使用装饰器语法，store无法响应式更新，导致bug  （这里耗时：3h）**

[[mobx6] @action.bound decorator not binding for function reference](https://github.com/mobxjs/mobx/issues/3215)

### 升级到 mobx 6

[https://mobx.js.org/migrating-from-4-or-5.html#getting-started](https://mobx.js.org/migrating-from-4-or-5.html#getting-started)

[https://michel.codes/blogs/mobx6](https://michel.codes/blogs/mobx6)

1. Remove all decorators and call `makeObservable` in the `constructor` and explicitly define which field should be made observable using which decorator. For example: `makeObservable(this, { count: observable, tick: action, elapsedTime: computed })` (note that the second argument corresponds to what would be passed to `decorate`). This is the recommended approach if you want to drop decorators in your code base, and the project isn't yet too big.
2. Leave all the decorators and call `makeObservable(this)` in the `constructor`. This will pick up the metadata generated by the decorators. This is the recommended way if you want to limit the impact of a MobX 6 migration.
3. Remove decorators and use `makeAutoObservable(this)` in the class `constructor`'s.



我们也可以用一个npm包做到自动转换

[https://www.npmjs.com/package/mobx-undecorate](https://www.npmjs.com/package/mobx-undecorate)

Update MobX 4/5 code to be conformant to MobX 6.



### 不作出反应

https://cn.mobx.js.org/best/react.html

MobX 不会对其作出反应:

- 从 observable 获取的值，但是在追踪函数之外
- 在异步调用的代码块中读取的 observable
- map 这种结构添加和删除不作出反应
- array 这种结构添加和删除不作出反应



### 获取observable新旧值

[computed 函数](https://cn.mobx.js.org/refguide/computed-decorator.html#computedexpression-%E5%87%BD%E6%95%B0)

```tsx
import {observable, computed} from "mobx";
var name = observable.box("John");

var upperCaseName = computed(() =>
    name.get().toUpperCase()
);

// change 是  { newValue, oldValue }   可以拿到新旧值
var disposer = upperCaseName.observe({ newValue, oldValue } => console.log(newValue,oldValue));
// 该函数返回一个 disposer 函数，当调用时可以取消观察者。

name.set("Dave");
// 输出: 'DAVE'
```









# Recoil

[https://zhuanlan.zhihu.com/p/400235192](https://zhuanlan.zhihu.com/p/400235192)

使用场景

- 大量需要共享状态的场景
- 大量需要派生状态(基于某些状态计算出一个新的状态)的场景
- 状态可以被持久化，进而通过被持久化的状态恢复当时场景



# zustand

[https://awesomedevin.github.io/zustand-vue/docs/introduce/what-is-zustand](https://awesomedevin.github.io/zustand-vue/docs/introduce/what-is-zustand)

[https://zustand-demo.pmnd.rs/](https://zustand-demo.pmnd.rs/)

基于 `Flux` 模型实现的小型、快速和可扩展的状态管理解决方案，拥有基于 `hooks` 的舒适的API，非常地灵活且有趣.



## 瞬时更新

[https://awesomedevin.github.io/zustand-vue/docs/advanced/transiend-updates](https://awesomedevin.github.io/zustand-vue/docs/advanced/transiend-updates)







# 补充



## Immer

[https://immerjs.github.io/immer/zh-CN/](https://immerjs.github.io/immer/zh-CN/)

[Immer 简介：简单的方法实现不变性](https://medium.com/hackernoon/introducing-immer-immutability-the-easy-way-9d73d8f71cb3)

Immer 简化了不可变数据结构的处理

Immer 可以在需要使用不可变数据结构的任何上下文中使用。例如与 React state、React 或 Redux reducers 或者 configuration management 结合使用。不可变的数据结构允许（高效）的变化检测：如果对对象的引用没有改变，那么对象本身也没有改变。此外，它使克隆对象相对便宜：数据树的未更改部分不需要复制，并且在内存中与相同状态的旧版本共享

一般来说，这些好处可以通过确保您永远不会更改对象、数组或映射的任何属性来实现，而是始终创建一个更改后的副本。



### Immer 和 Immutable

[Immutable.js了解一下](https://juejin.cn/post/6844903587458334733#heading-75)

[immer.js:也许更适合你的immutable js库](https://juejin.cn/post/6844904111402385422)

Immer.js 是一个基于 Proxy 实现的简化版不可变数据库，它提供了一种更简单的方式来创建和修改不可变数据。

主要思想：

通过使用 immer 函数，可以在修改不可变数据时编写可变代码。它允许你直接修改数据，而无需手动创建和返回新的数据对象。Immer.js 在内部利用了 JavaScript 的 Proxy 特性来捕获对象属性的修改，并对这些修改进行跟踪和记录，从而保证原始数据对象不会被修改。**使用 Immer.js 可以使代码更简洁、易于理解和维护**

实现原理：

使用了一个 ES6 的新特性 Proxy 对象

immer 的做法就是维护一份 state 在内部，劫持所有操作，内部来判断是否有变化从而最终决定如何返回。

----

Immutable.js 是一个强大的库，用于创建不可变的数据结构。它提供了多种不可变数据类型，如 List（列表）、Map（映射）、Set（集合）等。

核心思想：

不可变的数据结构可以提高性能和可预测性，因为它们保证了数据不能被意外修改。Immutable.js 的数据操作是通过创建和返回新的不可变数据来实现的，这意味着每次操作都会生成一个新的数据对象，而不会修改原始数据对象。

缺点：

Immutable的API设计的和原生对象类似，而且非常多，容易混淆操作

---

**immutable逐渐被immer取代，redux-toolkit集成了immer**

**与 immutable-js 最大的不同，immer 是使用原生数据结构的 API 而不是像 immutable-js 那样转化为内置对象之后使用内置的 API**

Immer 最大的好处就在这里，我们的学习没有太多成本，因为它的 API 很少，无非就是把我们之前的操作放置到 produce 函数的第二参数函数中去执行。



## Redux vs Mobx

[https://juejin.cn/post/6844903466029023246](https://juejin.cn/post/6844903466029023246)



Redux ：

* 数据流流动很自然，因为任何 dispatch 都会导致广播，需要依据对象引用是否变化来控制更新粒度。
* 有时间回溯的特征，可以增强业务的可预测性与错误定位能力。
* 对 typescript 支持困难
* 有中间件机制
* 使用纯函数修改状态，无副作用
* JavaScript对象
* **行为稳定可预测**，**易于测试**

Mobx:

* 数据流流动不自然，只有用到的数据才会引发绑定，局部精确更新，但免去了粒度控制烦恼。
* 没有时间回溯能力，因为数据只有一份引用。
* 完美支持 typescript
* 没有中间件机制
* 面向对象编程，可以直接赋值更新状态对象，简单直接
* 可观察对象 （是包裹的对象）
* 存在Derivations(派生）  
* Mobx 使用了 Object.defineProperty 拦截 getter 和 setter

**前端数据流不太复杂的情况，使用 Mobx，因为更加清晰，也便于维护；如果前端数据流极度复杂，建议谨慎使用 Redux，通过中间件减缓巨大业务复杂度**

---

Derivations(派生)：

* *Reactions*, 当 State 改变时需要自动运行的副作用
* *Computed values*,总是可以通过纯函数从当前的可观测 State 中派生。







## UI 组件

UI 组件有以下几个特征。

* 只负责 UI 的呈现，不带有任何业务逻辑
* 没有状态（即不使用this.state这个变量）
* 所有数据都由参数（this.props）提供
* 不使用任何 Redux 的 API

下面就是一个 UI 组件的例子。

```
const Title =
  value => <h1>{value}</h1>;
```

因为不含有状态，UI 组件又称为"纯组件"，即它纯函数一样，纯粹由参数决定它的值。

## 容器组件

容器组件的特征恰恰相反。

* 负责管理数据和业务逻辑，不负责 UI 的呈现
* 带有内部状态
* 使用 Redux 的 API

总之，只要记住一句话就可以了：**UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。**

你可能会问，如果一个组件既有 UI 又有业务逻辑，那怎么办？回答是，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。





## flux 单向数据流图

```tsx
                 _________               ____________               ___________
                |         |             |            |             |           |
                | Action  |------------▶| Dispatcher |------------▶| callbacks |
                |_________|             |____________|             |___________|
                     ▲                                                   |
                     |                                                   |
                     |                                                   |
 _________       ____|_____                                          ____▼____
|         |◀----|  Action  |                                        |         |
| Web API |     | Creators |                                        |  Store  |
|_________|----▶|__________|                                        |_________|
                     ▲                                                   |
                     |                                                   |
                 ____|________           ____________                ____▼____
                |   User       |         |   React   |              | Change  |
                | interactions |◀--------|   Views   |◀-------------| events  |
                |______________|         |___________|              |_________|


```

