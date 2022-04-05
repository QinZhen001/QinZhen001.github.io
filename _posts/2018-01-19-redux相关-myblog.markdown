---

layout:     post
title:      "redux相关"
date:       2018-01-19 22:27:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Redux 
---

> “Yeah It's on. ”


## 正文


[网页链接](http://www.redux.org.cn/)

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

可以让你构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。

Redux 除了和 React 一起用外，还支持其它界面库。
它体小精悍（只有2kB）且没有任何依赖。


### 要点
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

### 三大原则
Redux 可以用这三个基本原则来描述：

#### 单一数据源
整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。

这让同构应用开发变得非常容易。来自服务端的 state 可以在无需编写更多代码的情况下被序列化并注入到客户端中。由于是单一的 state tree ，调试也变得非常容易。在开发中，你可以把应用的 state 保存在本地，从而加快开发速度。此外，受益于单一的 state tree ，以前难以实现的如“撤销/重做”这类功能也变得轻而易举。

```
console.log(store.getState())

/* Prints
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*/
```

#### State 是只读的
惟一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。


这样确保了视图和网络请求都不能直接修改 state，相反它们只能表达想要修改的意图。因为所有的修改都被集中化处理，且严格按照一个接一个的顺序执行，因此不用担心 race condition 的出现。 Action 就是普通对象而已，因此它们可以被日志打印、序列化、储存、后期调试或测试时回放出来。

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
});

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
});
```

#### 使用纯函数来执行修改
为了描述 action 如何改变 state tree ，你需要编写 reducers。

Reducer 只是一些纯函数，它接收先前的 state 和 action，并返回新的 state。刚开始你可以只有一个 reducer，随着应用变大，你可以把它拆成多个小的 reducers，分别独立地操作 state tree 的不同部分，因为 reducer 只是函数，你可以控制它们被调用的顺序，传入附加数据，甚至编写可复用的 reducer 来处理一些通用任务，如分页器。

```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```

## 基础

### Action

Action 是把数据从应用（译者注：这里之所以不叫 view 是因为这些数据有可能是服务器响应，用户输入或其它非 view 的数据 ）传到 store 的有效载荷。它是 store 数据的**唯一来源**。一般来说你会通过 store.dispatch() 将 action 传到 store。

添加新 todo 任务的 action 是这样的：

```js
const ADD_TODO = 'ADD_TODO'

{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

Action 本质上是 JavaScript 普通对象。我们约定，action 内必须使用一个字符串类型的 type 字段来表示将要执行的动作。多数情况下，type 会被定义成字符串常量。当应用规模越来越大时，建议使用单独的模块或文件来存放 action。

`import { ADD_TODO, REMOVE_TODO } from '../actionTypes'`

这时，我们还需要再添加一个 action type 来表示用户完成任务的动作。因为数据是存放在数组中的，所以我们通过下标 index 来引用特定的任务。而实际项目中一般会在新建数据的时候生成唯一的 ID 作为数据的引用标识。

```
{
  type: TOGGLE_TODO,
  index: 5
}
```
我们应该尽量减少在 action 中传递的数据。比如上面的例子，传递 index 就比把整个任务对象传过去要好。

最后，再添加一个 action type 来表示当前的任务展示选项。

```
{
  type: SET_VISIBILITY_FILTER,
  filter: SHOW_COMPLETED
}
```

#### Action 创建函数
在 Redux 中的 action 创建函数只是简单的返回一个 action:

```
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

这样做将使 action 创建函数更容易被移植和测试。

在 传统的 Flux 实现中，当调用 action 创建函数时，一般会触发一个 dispatch，像这样：

```
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

不同的是，Redux 中只需把 action 创建函数的结果传给 dispatch() 方法即可发起一次 dispatch 过程。

```
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

或者创建一个 **被绑定的 action 创建**函数 来自动 dispatch：

```
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
```

```
boundAddTodo(text);
boundCompleteTodo(index);
```

store 里能直接通过 store.dispatch() 调用 dispatch() 方法，但是多数情况下你会使用 react-redux 提供的 connect() 帮助器来调用。bindActionCreators() 可以自动把多个 action 创建函数 绑定到 dispatch() 方法上。

### Reducer
Action 只是描述了**有事情发生了**这一事实，并没有指明应用如何更新 state。而这正是 reducer 要做的事情。

#### 设计 State 结构
在 Redux 应用中，所有的 state 都被保存在一个单一对象中。建议在写代码前先想一下这个对象的结构。如何才能以最简的形式把应用的 state 用对象描述出来？

#### Action 处理

现在我们已经确定了 state 对象的结构，就可以开始开发 reducer。reducer 就是一个纯函数，接收旧的 state 和 action，返回新的 state。

`(previousState, action) => newState`


之所以称作 reducer 是因为它将被传递给 `Array.prototype.reduce(reducer, ?initialValue)` 方法。保持 reducer 纯净非常重要。永远不要在 reducer 里做这些操作：

* 修改传入参数；
* 执行有副作用的操作，如 API 请求和路由跳转；
* 调用非纯函数，如 Date.now() 或 Math.random()。


>只要传入参数相同，返回计算得到的下一个 state 就一定相同。没有特殊情况、没有副作用，没有 API 请求、没有变量修改，单纯执行计算。

```js
import { VisibilityFilters } from './actions'

const initialState = {
  visibilityFilter: VisibilityFilters.SHOW_ALL,
  todos: []
};

function todoApp(state, action) {
  if (typeof state === 'undefined') {
    return initialState
  }

  // 这里暂不处理任何 action，
  // 仅返回传入的 state。
  return state
}
```
这里一个技巧是使用 ES6 参数默认值语法 来精简代码。

```js
function todoApp(state = initialState, action) {
  // 这里暂不处理任何 action，
  // 仅返回传入的 state。
  return state
}
```

现在可以处理 SET_VISIBILITY_FILTER。需要做的只是改变 state 中的 visibilityFilter。

```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    default:
      return state
  }
}
```

**注意:**
1. 不要修改 state。 使用 Object.assign() 新建了一个副本。不能这样使用 Object.assign(state, { visibilityFilter: action.filter })，因为它会改变第一个参数的值。你必须把第一个参数设置为空对象。你也可以开启对ES7提案对象展开运算符的支持, 从而使用 { ...state, ...newState } 达到相同的目的。
2. 在 default 情况下返回旧的 state。遇到未知的 action 时，一定要返回旧的 state。

#### 处理多个 action
还有两个 action 需要处理。让我们先处理 ADD_TODO。
```js
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    default:
      return state
  }
}
```
如上，不直接修改 state 中的字段，而是返回新对象。新的 todos 对象就相当于旧的 todos 在末尾加上新建的 todo。而这个新的 todo 又是基于 action 中的数据创建的。

最后，TOGGLE_TODO 的实现也很好理解：

```js
case TOGGLE_TODO:
  return Object.assign({}, state, {
    todos: state.todos.map((todo, index) => {
      if (index === action.index) {
        return Object.assign({}, todo, {
          completed: !todo.completed
        })
      }
      return todo
    })
  })
```

#### 拆分 Reducer
目前的代码看起来有些冗长：
```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
      return Object.assign({}, state, {
        todos: [
          ...state.todos,
          {
            text: action.text,
            completed: false
          }
        ]
      })
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: state.todos.map((todo, index) => {
          if(index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            })
          }
          return todo
        })
      })
    default:
      return state
  }
}
```



## 中间件

为了理解中间件，让我们站在框架作者的角度思考问题：如果要添加功能，你会在哪个环节添加？

1. Reducer：纯函数，只承担计算 State 的功能，不合适承担其他功能，也承担不了，因为理论上，纯函数不能进行读写操作。
2. View：与 State 一一对应，可以看作 State 的视觉层，也不合适承担其他功能。
3. Action：存放数据的对象，即消息的载体，只能被别人操作，自己不能进行任何操作。





### applyMiddlewares

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







### redux-thunk

http://www.redux.org.cn/docs/advanced/AsyncActions.html

Action 发出以后，Reducer 立即算出 State，这叫做同步；Action 发出以后，过一段时间再执行 Reducer，这就是异步。

怎么才能 Reducer 在异步操作结束后自动执行呢？这就要用到新的工具：中间件（middleware）。

>**中间件:它提供的是位于 action 被发起之后，到达 reducer 之前的扩展点。**

redux-thunk中间件可以让action创建函数先不返回一个action对象，而是返回一个函数，函数传递两个参数(dispatch,getState),在函数体内进行业务逻辑的封装



```js
function add() {
    return {
        type: 'ADD',
    }
}

function addIfOdd() {
    return (dispatch, getState) => {
        const currentValue = getState();
        if (currentValue % 2 == 0) {
            return false;
        }
        //分发一个任务
        dispatch(add())
    }
}
```



使用：

```js
let store = createStore(reducer函数，applyMiddleware(thunk))
```



完整的例子：

```jsx
import {createStore, applyMiddleware} from 'redux';
import thunk from 'redux-thunk';

function count(state = 0, action) {
    switch (action.type) {
        case 'ADD':
            return state + 1;
        case 'REDUCER':
            return state - 1;
        default:
            return state;
    }
}
const store = createStore(count,applyMiddleware(thunk));


//action创建函数
function add() {
    return {
        type: 'ADD',
    }
}

function reducer() {
    return {
        type: 'REDUCER'
    }
}


function addIfOdd() {
    return (dispatch, getState) => {
        const currentValue = getState();
        if (currentValue % 2 == 0) {
            return false;
        }
        dispatch(add())
    }
}

function addAsy(delay = 2000) {
    return (dispatch, getState) => {
        setTimeout(() => {
            dispatch(add())
        }, delay)
    }
}

//获取当前值
let currentValue = store.getState();
//创建一个监听
store.subscribe(() => {
    const previosValue = currentValue;
    currentValue = store.getState();
    console.log('上一个值:', previosValue, '当前值:', currentValue)
});

//分发任务
store.dispatch(add());
store.dispatch(add());
store.dispatch(add());
store.dispatch(add());
store.dispatch(reducer());
store.dispatch(addIfOdd());
store.dispatch(addAsy());
```

当 action creator 返回函数时，这个函数会被 Redux Thunk middleware 执行。这个函数并不需要保持纯净；它还可以带有副作用，包括执行异步 API 请求。这个函数还可以 dispatch action，就像 dispatch 前面定义的同步 action 一样。



```js
import fetch from 'isomorphic-fetch'

export const REQUEST_POSTS = 'REQUEST_POSTS'
function requestPosts(subreddit) {
  return {
    type: REQUEST_POSTS,
    subreddit
  }
}

export const RECEIVE_POSTS = 'RECEIVE_POSTS'
function receivePosts(subreddit, json) {
  return {
    type: RECEIVE_POSTS,
    subreddit,
    posts: json.data.children.map(child => child.data),
    receivedAt: Date.now()
  }
}

// 来看一下我们写的第一个 thunk action creator！
// 虽然内部操作不同，你可以像其它 action creator 一样使用它：
// store.dispatch(fetchPosts('reactjs'))

export function fetchPosts(subreddit) {

  // Thunk middleware 知道如何处理函数。
  // 这里把 dispatch 方法通过参数的形式传给函数，
  // 以此来让它自己也能 dispatch action。

  return function (dispatch) {

    // 首次 dispatch：更新应用的 state 来通知
    // API 请求发起了。

    dispatch(requestPosts(subreddit))        // 可以多次 dispatch！

    // thunk middleware 调用的函数可以有返回值，
    // 它会被当作 dispatch 方法的返回值传递。

    // 这个案例中，我们返回一个等待处理的 promise。
    // 这并不是 redux middleware 所必须的，但这对于我们而言很方便。

    return fetch(`http://www.subreddit.com/r/${subreddit}.json`)
      .then(response => response.json())
      .then(json =>

      )

      // 在实际应用中，还需要
      // 捕获网络请求的异常。
  }
}
```

thunk 的一个优点是它的结果可以再次被 dispatch



使用双箭头简化函数的写法

```js
const addToCartUnsafe = productId => ({
  type: types.ADD_TO_CART,
  productId
})


export const addToCart = productId => (dispatch, getState) => {
  if (getState().products.byId[productId].inventory > 0) {
    dispatch(addToCartUnsafe(productId))
  }
}
```



注意点：

1. createStore方法可以接受整个应用的初始状态作为参数，那样的话，applyMiddleware就是第三个参数了。

```js
    const store = createStore(
      reducer,
      initial_state,
      applyMiddleware(logger)
    );
```

2. 中间件的次序有讲究。

```js
    const store = createStore(
      reducer,
      applyMiddleware(thunk, promise, logger)
    );
```

上面代码中，applyMiddleware方法的三个参数，就是三个中间件。有的中间件有次序要求，使用前要查一下文档。比如，logger就一定要放在最后，否则输出结果会不正确。







### redux-undo

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



## 补充

张著名的 flux 的单向数据流图
```
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





### bindActionCreators

[网页链接](http://blog.csdn.net/liwusen/article/details/54138854)

简要介绍：Redux中的bindActionCreators，是通过dispatch将action包裹起来，这样可以通过bindActionCreators创建的方法，直接调用dispatch(action)(隐式调用）。

主要用处：一般情况下，我们可以通过Provider将store通过React的connext属性向下传递，bindActionCreators的唯一用处就是需要传递action creater到子组件，并且改子组件并没有接收到父组件上传递的store和dispatch。

bindActionCreators的参数
`let newAction = bindActionCreators(oldActionCreator,dispatch)`

来看一下形参所表示的意思：



**oldActionCreator**

这个参数就是创建的action的集合：

```js
//action.js

function action1(){
  return {
   type:'type1'
  }
}
function action2(){
  return {
   type:'type2'
  }
}
```

```js
import * as oldActionCreator from './action.js'

let newAction = bindActionCreators(oldActionCreator,dispatch)
```

从上述的例子中我们可以看到oldActionCreator的形式为key:function的形式，其中function必须返回一个action(包含type标识的唯一对象）。



**dispatch**

这里的dispatch，等同于store中的store.dispatch，用于组合action



`<child {...newAction}></child>`


我们将组合oldAction和dispatch的对象传递给子组件，在子组件中，调用newAction.action1,相当于实现了dispatch(action1)。于是我们就实现了在没有store和dispatch组件中，如何调用dispatch(action)

后面发现这个接口并没有什么用，**因为一般都会import react-redux….** 目前还未发现bindActionCreators的用处，估计唯一的用处就是在子组件未察觉redux的情况下，将dispatch传递给子组件。。。。。。。。











## 总结

* store 由 Redux 的 createStore(reducer) 生成
* state 通过 store.getState() 获取，本质上一般是一个存储着整个应用状态的对象
* action 本质上是一个包含 type 属性的普通对象，由 Action Creator (函数) 产生
* 改变 state 必须 dispatch 一个 action
* reducer 本质上是根据 action.type 来更新 state 并返回 nextState 的函数
* reducer 必须返回值，否则 nextState 即为 undefined
* 实际上，state 就是所有 reducer 返回值的汇总

