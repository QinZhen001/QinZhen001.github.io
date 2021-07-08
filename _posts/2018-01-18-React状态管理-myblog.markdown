---
layout:     post
title:      "react状态管理"
date:       2018-01-18 16:05:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Redux
---

> “Yeah It's on. ”



# Redux



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

### UI 组件

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

### 容器组件
容器组件的特征恰恰相反。

* 负责管理数据和业务逻辑，不负责 UI 的呈现
* 带有内部状态
* 使用 Redux 的 API

总之，只要记住一句话就可以了：**UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。**

你可能会问，如果一个组件既有 UI 又有业务逻辑，那怎么办？回答是，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。

### connect()
React-Redux 提供connect方法，


connect的作用就是将React和Redux中的store连接起来


1. 接收一个组件，将Redux中的store通过props传递给组件
2. 将Redux中的action也通过props传递给组件
3. 返回一个新的组件，这个组件可以通过this.props访问到store中的属性，也可能是触发action
4. 当store中的数据发生变化的时候，通知新的组件





```
import { connect } from 'react-redux'
const VisibleTodoList = connect()(TodoList);
```


```
import { connect } from 'react-redux'

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)
```

上面代码中，connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action。


#### mapStateToProps()
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

上面代码中，mapStateToProps是一个函数，它接受state作为参数，返回一个对象。这个对象有一个todos属性，代表 UI 组件的同名参数，后面的getVisibleTodos也是一个函数，可以从state算出 todos 的值。

下面就是getVisibleTodos的一个例子，用来算出todos。
```
const getVisibleTodos = (todos, filter) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed)
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed)
    default:
      throw new Error('Unknown filter: ' + filter)
  }
}
```
mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

mapStateToProps的第一个参数总是state对象，还可以使用第二个参数，代表容器组件的props对象。

```
// 容器组件的代码
//    <FilterLink filter="SHOW_ALL">
//      All
//    </FilterLink>

const mapStateToProps = (state, ownProps) => {
  return {
    active: ownProps.filter === state.visibilityFilter
  }
}
```
使用ownProps作为参数后，如果容器组件的参数发生变化，也会引发 UI 组件重新渲染。

>connect方法可以省略mapStateToProps参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

#### mapDispatchToProps()
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

#### connect常见的使用
们常见的写法，也就是ES7的decorator装饰器，也就是装饰者模式，这种写法比较简便：
```
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
```
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


### `<Provider>` 组件

connect方法生成容器组件以后，需要让容器组件拿到state对象，才能生成 UI 组件的参数。

一种解决方法是将state对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将state传下去就很麻烦。

React-Redux 提供Provider组件，可以让容器组件拿到state。


```

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





# MobX

https://zh.mobx.js.org/README.html



## mobx-react

[源码地址](https://github.com/mobxjs/mobx/blob/main/packages/mobx-react/src/index.ts)



###  observer

[observer 源码](https://github.com/mobxjs/mobx/blob/main/packages/mobx-react/src/observer.tsx)





## Mobx-react-lite 

Lightweight React bindings for MobX based on React 16.8 and Hooks

它是专门服务于react hooks的mobx-react轻量级版本

虽然mobx-react@6已经包含了mobx-react-lite，但官方是推荐在没有类组件的react项目中直接使用mobx-react-lite的。







