---
layout:     post
title:      "React相关"
date:       2018-01-07 13:59:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


## 基础
[https://doc.react-china.org/](https://doc.react-china.org/)

这里只写一些比较重要的基础知识，查看更多知识请移步react官网



### 受控组件与非受控组件

#### 受控组件

[https://zh-hans.legacy.reactjs.org/docs/forms.html#controlled-components](https://zh-hans.legacy.reactjs.org/docs/forms.html#controlled-components)

受控组件是指表单元素的值由React组件的state来控制。当用户输入数据时，React组件的state会更新，然后通过props将新值传递给表单元素。这样，每次输入变化都会触发组件的重新渲染。受控组件提供了更精确的控制和验证，可以通过表单元素的值来实时更新其他组件状态。

#### 非受控组件

[https://zh-hans.legacy.reactjs.org/docs/uncontrolled-components.html](https://zh-hans.legacy.reactjs.org/docs/uncontrolled-components.html)

**外部无法直接控制 state 的方式，我们称为非受控**

非受控组件是指表单元素的值不受React组件state的控制。通常使用非受控组件可以减少一些冗余的代码。**在非受控组件中，可以通过ref属性来获取表单元素的值，然后在需要时进行处理。**

---



**受控组件需要主动维护一个内部state状态的，而非受控组件是无需维护组件的state状态的，二者有冲突。**

* 受控元素，一般用在需要动态设置其初始值的情况；例如某些form表单信息编辑时，input表单元素需要初始显示服务器返回的某个值然后进行编辑。
* 非受控元素， 一般用于无任何动态初始值信息的情况； 例如form表单创建信息时，input表单元素都没有初始值，需要用户输入的情况

**在大多数情况下，我们推荐使用 受控组件 来实现表单。**



### React.ReactNode vs React.ReactNode

React.ReactNode => 这是可以在 JSX 中作为子元素传递的所有可能类型的并集

React.ReactElement => 它只包括 JSX 元素，而不包括 JavaScript 原始类型，如 string 或 number



### defaultValue vs value

Controlled vs. Uncontrolled

If you're using state to manage the value of an input, you'd use the value attribute to make it a controlled component. This means React would manage the input's state.

如果您使用state来管理输入的值，那么您将使用value属性使其成为受控组件。这意味着React将管理输入的状态。

However, since you're not using state, you can use defaultValue. This means the native input will manage its own state. This is okay since you're saving the search query to the URL instead of state.

但是，由于没有使用state，所以可以使用defaultValue。这意味着本机输入将管理自己的状态。这是可以的，因为您将搜索查询保存到URL而不是状态。

```html
<input
  placeholder={placeholder}
  onChange={(e) => {
    handleSearch(e.target.value);
  }}
  defaultValue={searchParams.get('query')?.toString()}
/>
```



在非受控组件中，你经常希望 React 能赋予组件一个初始值，但是不去控制后续的更新。 在这种情况下, 你可以指定一个 `defaultValue` 属性，而不是 `value`。在一个组件已经挂载之后去更新 `defaultValue` 属性的值，不会造成 DOM 上值的任何更新。

```html
 <input
   defaultValue="Bob"
   type="text"
   ref={this.input} />
```

同样，`<input type="checkbox">` 和 `<input type="radio">` 支持 `defaultChecked`，`<select>` 和 `<textarea>` 支持 `defaultValue`。



### PureComponent

默认渲染行为的问题

在React Component的生命周期中，有一个shouldComponentUpdate方法。这个方法默认返回值是true。

这意味着就算没有改变组件的props或者state，也会导致组件的重绘。这就经常导致组件因为不相关数据的改变导致重绘，这极大的降低了React的渲染效率。比如下面的例子中，任何options的变化，甚至是其他数据的变化都可能导致所有cell的重绘

```jsx
//Table Component
{this.props.items.map(i =>
    <Cell data={i} option={this.props.options[i]} />
)}
```

为了避免这个问题，我们可以在Cell中重写shouldComponentUpdate方法，只在option发生改变时进行重绘。

```jsx
class Cell extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.option === nextProps.option) {
      return false;
    } else {
      return true;
    }
  }
}
```

这样每个Cell只有在关联option发生变化时进行重绘。



----



使用PureComponent与immutable.js

因为上面的情况十分通用，React创建了PureComponent组件创建了默认的shouldComponentUpdate行为。这个默认的shouldComponentUpdate行为会一一比较props和state中所有的属性，只有当其中任意一项发生改变是，才会进行重绘。

**需要注意的是，PureComponent使用浅比较判断组件是否需要重绘**

因此，下面对数据的修改并不会导致重绘（假设Table也是PureComponent)

```js
options.push(new Option())
options.splice(0, 1)
options[i].name = "Hello"
```

这些例子都是在原对象上进行修改，由于浅比较是比较指针的异同，所以会认为不需要进行重绘。

为了避免出现这些问题，推荐使用immutable.js。

**immutable.js会在每次对原对象进行添加，删除，修改时返回新的对象实例。任何对数据的修改都会导致数据指针的变化。**





### displayName

HOC 创建的容器组件会与任何其他组件一样，会显示在 [React Developer Tools](https://github.com/facebook/react-devtools) 中。为了方便调试，请选择一个显示名称，以表明它是 HOC 的产物。



最常见的方式是用 HOC 包住被包装组件的显示名称。比如高阶组件名为 `withSubscription`，并且被包装组件的显示名称为 `CommentList`，显示名称应该为 `WithSubscription(CommentList)`：



```tsx
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
  return WithSubscription;
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}
```



### style

[https://zh-hans.reactjs.org/docs/dom-elements.html#style](https://zh-hans.reactjs.org/docs/dom-elements.html#style)

**在react中style是在{{}}中的**

```jsx
 <div  style={{ height: 12px}}></div>
```

**`style` 接受一个采用小驼峰命名属性的 JavaScript 对象，而不是 CSS 字符串**

这与 DOM 中 `style` 的 JavaScript 属性是一致的，同时会更高效的，且能预防跨站脚本（XSS）的安全漏洞。



例子：

```jsx
const divStyle = {
  color: 'blue',
  backgroundImage: 'url(' + imgUrl + ')',
};

function HelloWorldComponent() {
  return <div style={divStyle}>Hello World!</div>;
}
```





### unmountComponentAtNode

```tsx
import { unmountComponentAtNode } from 'react-dom'
```

举个例子:

创建一个弹窗

```tsx
const a = document.createElement('div');
document.body.appendChild(a);
ReactDOM.render(<Modal>, a);
```

如果要关闭弹窗则同时还要卸载渲染在a里面的组件

```tsx
const unmountResult = ReactDOM.unmountComponentAtNode(a);
if (unmountResult && a.parentNode) {
       a.parentNode.removeChild(a);
}
```





### findDomNode

react中获取真实dom有两种方法，一种是 react 的ref，一种是 ReactDOM 的 findDomNode

```tsx
let  Btn = document.getElementById('btn')
ReactDOM.findDOMNode(Btn).style.color = 'red'
```





### createPortal

[https://zh-hans.reactjs.org/docs/react-dom.html#createportal](https://zh-hans.reactjs.org/docs/react-dom.html#createportal)

```
createPortal(child, container)
```

创建 portal。[Portal](https://zh-hans.reactjs.org/docs/portals.html) 提供了一种将子节点渲染到已 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。

用处：我们渲染dialog到指定的container中



###  [ReactNode vs ReactElement](https://stackoverflow.com/questions/58123398/when-to-use-jsx-element-vs-reactnode-vs-reactelement)

```tsx
type ReactText = string | number;
type ReactChild = ReactElement | ReactText;

type ReactNode = ReactChild | ReactFragment | ReactPortal | boolean | null | undefined;

interface ReactElement<P = any, T extends string | JSXElementConstructor<any> = string | JSXElementConstructor<any>> {
        type: T;
        props: P;
        key: Key | null;
    }
```



ReactNode 是包括ReactElement 同时可以为null





### ReactDOMServer

[https://reactjs.org/docs/react-dom-server.html](https://reactjs.org/docs/react-dom-server.html)



## 动画



### rc-queue-anim

[网页链接](https://www.npmjs.com/package/rc-queue-anim)


Animate React Component in queue, thanks to rc-animate and enter-animation.


Usage

```tsx
import QueueAnim from 'rc-queue-anim';
import React from 'react';
import ReactDom from 'react-dom';
 
ReactDom.render(
  <QueueAnim>
    <div key="1">enter in queue</div>
    <div key="2">enter in queue</div>
    <div key="3">enter in queue</div>
  </QueueAnim>
, mountNode);
```

### react-transition-group

[http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit](http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit)

#### [`unmountOnExit`](http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit)

By default the child component stays mounted after it reaches the `'exited'` state. Set `unmountOnExit` if you'd prefer to unmount the component after it finishes exiting.

* type: `boolean`
* default: `false`

#### [`appear`](http://reactcommunity.org/react-transition-group/transition#Transition-prop-appear)

By default the child component does not perform the enter transition when it first mounts, regardless of the value of `in`. If you want this behavior, set both `appear` and `in` to `true`.

> **Note**: there are no special appear states like `appearing`/`appeared`, this prop only adds an additional enter transition. However, in the `<CSSTransition>` component that first enter transition does result in additional `.appear-*` classes, that way you can choose to style it differently.

* type: `boolean`
* default: `false`

#### 测试

[https://reactcommunity.org/react-transition-group/testing](https://reactcommunity.org/react-transition-group/testing)



> 当我们使用测试框架事关闭react-transition-group的动画过渡效果

In some situations, like visual snapshot testing, it's helpful to disable transitions so they don't complicate the test, or introduce abitrary waits. To make this easier `react-transition-group` exposes a way to globally toggle transitions. When set, **all** transitions, when toggled, will immediately switch to their entered or exited states as appropriate.



```js
import { config } from 'react-transition-group

config.disabled = true
```









### Refs

[https://zh-hans.reactjs.org/docs/refs-and-the-dom.html](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)

> `ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

下面是几个适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。





#### Ref with ts

[https://www.designcise.com/web/tutorial/how-to-fix-useref-react-hook-cannot-assign-to-read-only-property-typescript-error](https://www.designcise.com/web/tutorial/how-to-fix-useref-react-hook-cannot-assign-to-read-only-property-typescript-error)

```tsx
function useRef<T>(initialValue: T): MutableRefObject<T>;
function useRef<T>(initialValue: T|null): RefObject<T>;
function useRef<T = undefined>(): MutableRefObject<T | undefined>;
```



```tsx
interface RefObject<T> {
    readonly current: T | null;   // tip： readonly 只读
}
```

```tsx
interface MutableRefObject<T> {
    current: T;
}
```

所以我们在ts中使用ref 可以使用MutableRefObject







### context

[https://dmitripavlutin.com/react-context-and-usecontext/](https://dmitripavlutin.com/react-context-and-usecontext/)









### render prop 惰性初始化

`someExpensiveComputation` 是一个相对耗时的操作。如果我们直接采用

```ts
const initialState = someExpensiveComputation(props);
const [state, setState] = useState(initialState);
```

注意，虽然 `initialState` 只在初始化时有其存在的价值，但是 `someExpensiveComputation` 在每一帧都被调用了。只有当使用惰性初始化的方法：

```ts
const [state, setState] = useState(() => {
    const initialState = someExpensiveComputation(props);
    return initialState;
});

```

因 `someExpensiveComputation` 运行在一个匿名函数下，该函数当且仅当初始化时被调用，从而优化性能。



### css 样式隔离 



#### css module

[https://github.com/css-modules/css-modules](https://github.com/css-modules/css-modules)

探究了一些方式，最终采用 css module 的方式 

```tsx
import style from './index.module.scss'

const WelcomePage = () => {
  return <div className={style.welcomePage}></div>
}
```

vite.config.ts

```tsx
export default defineConfig({
  plugins: [react()],
  css: {
  	// 可配置 css module 相关
    // https://vitejs.dev/config/shared-options.html#css-modules
    modules: {
      globalModulePaths: [
        /.*\\.global\\..*/
      ]
    },
  }
})
```



##### Multiple Classes

[Using Multiple Classes With React CSS Modules](https://www.codeconcisely.com/posts/react-css-modules-multiple-classes/)

```tsx
// 使用多个styles

<button type="button" className={`${styles.button} ${styles.filled}`}>
  Click here
</button>
```



##### 全局作用域

凡是使用global声明的class，都不会按照我们定义的格式进行转换，简单点说就是css module 不去处理

使用gloabl 此时我们就不能再这样些了className={style.demo}，二是写出这样 className='demo'

例如

```css
:global(.demo) {
  margin: 50px;
  border: 1px solid red;
  width: 300px;
  height: 40px;
  height: 300px;
}
```

等同于

```css
:global {
  .demo {
    margin: 50px;
    border: 1px solid red;
    width: 300px;
    height: 40px;
    height: 300px;
  }
}
```



##### hot reload

[Configure CSS module class ](https://stackoverflow.com/questions/77072008/configure-css-module-class-name-in-vue-project-with-vite-js)

热更新导致 代码中的class name 和 style 的 class name 对应不上来，导致页面样式丢失

解决：

```tsx
  css: {
    ...
    modules: {
      generateScopedName: "[name]__[local]__[hash:base64:2]"
    }
  },
```





### Hooks

1. 更简洁的代码：使用Hooks可以使代码更加简洁，不再需要编写class组件，可以直接在函数组件中定义state和effect，减少了冗余的代码。
2. 更好的代码复用：Hooks可以将逻辑相关的代码封装成自定义的Hook函数，以便在不同的组件中复用。
3. 更好的可读性和可维护性：Hooks使得代码逻辑更加清晰，将关联的代码放在一起，便于理解和修改。
4. 更好的性能优化：Hooks提供了更细粒度的控制，可以通过useMemo、useCallback等钩子函数来优化性能。
5. 更好的测试性：由于Hooks是纯JavaScript函数，可以更方便地进行单元测试。
6. 更好的适配性：Hooks不依赖于Class语法，可以更好地处理函数组件和类组件之间的转换。

总之，使用React Hooks可以提高开发效率、改善代码质量和提供更好的用户体验。



#### useEffect

```tsx
const countRef = useRef(0);

useEffect(() => {
  console.log('useEffect triggered',countRef.current);
  // ref.current 的值发生变更不会触发re-render
}, [countRef.current]);

const handleIncrement = () => {
    countRef.current += 1;
};
```

不需要吧Ref相关的东西放到useEffect依赖之中





#### useRef

`useRef` 的初始值通常设置为 `null`：

 ```ts
  const spanRef = useRef<HTMLSpanElement | null>(null); 
 ```

设置初始值为 `null` 的主要原因是：

- 在初次渲染时，引用的 DOM 元素还没有被挂载到 DOM 中，`useRef` 的初始值为 `null` 是一个合理的初始状态。
- `null` 是一个常用的初始值，表示引用对象还没有被赋值。



#### useMemo

**你可以把 `useMemo` 作为一种性能优化的手段，但不要把它当做一种语义上的保证。**

记住，传给 `useMemo` 的函数是在渲染期间运行的。不要在其中做任何你通常不会在渲染期间做的事。举个例子，副作用属于 `useEffect`，而不是 `useMemo`。



#### React.memo 和 useMemo

[http://www.ptbird.cn/react-hook-useMemo-purerender.html](http://www.ptbird.cn/react-hook-useMemo-purerender.html)

```js
const MyComponent = React.memo(function MyComponent(props) {
  /* 使用 props 渲染 */
});
```

`React.memo` 为[高阶组件](https://zh-hans.reactjs.org/docs/higher-order-components.html)。



如果你的组件在相同 props 的情况下渲染相同的结果，那么你可以通过将其包装在 `React.memo` 中调用，以此通过记忆组件渲染结果的方式来提高组件的性能表现。这意味着在这种情况下，React 将跳过渲染组件的操作并直接复用最近一次渲染的结果。



`React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 [`useState`](https://zh-hans.reactjs.org/docs/hooks-state.html) 或 [`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext) 的 Hook，当 context 发生变化时，它仍会重新渲染。



默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现。



```js
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
export default React.memo(MyComponent, areEqual);
```

#### useMemo 和 useEffect 的区别

> 不能在useMemo中操作DOM之类的副作用操作，不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 useEffect 的适用范畴，而不是 useMemo

> 在`useMemo`中使用`setState`你会发现会产生死循环，并且会有警告，因为`useMemo`是在渲染中进行的，你在其中操作`DOM`后，又会导致触发`memo`

>  useEffect 是官方推荐拿来代替 componentDidMount / componentDidUpdate / componentWillUnmount 这 3 个生命周期函数的，但其实他们并不是完全等价，useEffect 是在浏览器渲染结束之后才执行的，而这三个生命周期函数是在浏览器渲染之前同步执行的，React 还有一个官方的 hook 是完全等价于这三个生命周期函数的，叫 useLayoutEffect。

[useMemo 官方文档](https://zh-hans.reactjs.org/docs/hooks-reference.html#usememo)

[useMemo和useEffect有什么区别](https://blog.csdn.net/hsany330/article/details/106122228)

[React Hooks: 深入剖析 useMemo 和 useEffect](https://zhuanlan.zhihu.com/p/268802571)

[useEffect的陷阱](https://zhuanlan.zhihu.com/p/84697185)



* **当你调用 useEffect 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数**。**effect只能在DOM更新后再触发**.
* useMemo 的函数会在渲染期间执行，所以使用`useMemo`就能解决怎么在`DOM`改变的时候，控制某些函数不被触发。

* **
* useEffect 可以看成 componentDidMount / componentDidUpdate / componentWillUnmount 这 3 个生命周期函数的替代。



> 



#### useLayoutEffect

[https://zhuanlan.zhihu.com/p/348701319](https://zhuanlan.zhihu.com/p/348701319)

**`useLayoutEffect` 是渲染之前同步执行的，所以会等它执行完再渲染上去，就避免了闪烁现象。也就是说我们最好把操作 dom 的相关操作放到 `useLayouteEffect` 中去，避免导致闪烁。**

在ssr中不要使用useLayoutEffect  （`useLayoutEffect` 是不会在服务端执行的，所以就有可能导致 ssr 渲染出来的内容和实际的首屏内容并不一致。）



#### useContext

https://zhuanlan.zhihu.com/p/260789887

**`useContext`可以帮助我们跨越组件层级直接传递变量，实现共享。**







#### useCallback

[https://zhuanlan.zhihu.com/p/56975681](https://zhuanlan.zhihu.com/p/56975681)

乍一看使用了 Hooks 的 React 代码，可能会疑惑创建了这么多的 inline 函数会不会很影响性能？React 之前不是一直建议避免在 callback 里新建函数吗？首先可以看一下[官方的解释](https://link.zhihu.com/?target=https%3A//reactjs.org/docs/hooks-faq.html%23are-hooks-slow-because-of-creating-functions-in-render)，里面提到在 JavaScript 中闭包函数的性能是非常快的，并且得益于相对于 class 更轻量的函数组件，以及避免了 HOC，renderProps 等等额外层级，性能差不到那里去。



**有些人可能会误以为 `useCallback` 可以用来解决创建函数造成的性能问题，其实恰恰相反，单从这个组件看的话 `useCallback` 只会更慢，因为 inline 函数是无论如何都会创建的，还增加了 `useCallback` 内部对 inputs 变化的检测。**



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

`useCallback` 的真正目的还是在于缓存了每次渲染时 inline callback 的实例，这样方便配合上子组件的 `shouldComponentUpdate` 或者 `React.memo` 起到减少不必要的渲染的作用。需要不断提醒自己注意的是，在大部分 `callback` 都会是 `inline callback` 的未来，`React.memo` 和 `React.useCallback` 一定记得需要配对使用，缺了一个都可能导致性能不升反“降”，毕竟无意义的浅比较也是要消耗那么一点点点的性能。



回到 Hooks 总结一下，**`useCallback` 的作用在于利用 `memoize` 减少无效的 `re-render`，来达到性能优化的作用。**还是那句老生常谈的话，“不要过早的性能优化”。从实际开发的经验来看，在做这类性能优化时，一定得观察比较优化的结果，因为某个小角落的 `callback` 就可能导致优化前功尽弃，甚至是适得其反。



----



看完上面的疑问，你可能觉得 `useCallback` 也挺清晰的，那其实是你忘了第二个参数 `inputs` 而产生的错觉。有一个比较复杂的问题是，在当前的实现下，如果一个 `callback` 依赖于一个经常变化的 `state`，这个 `callback` 的引用是无法缓存的。React 文档的 FAQ 里也提到了这个[问题](https://link.zhihu.com/?target=https%3A//reactjs.org/docs/hooks-faq.html%23how-to-read-an-often-changing-value-from-usecallback)，还原一下问题的场景：

```jsx
function Form() {
  const [text, updateText] = useState('');

  const handleSubmit = useCallback(() => {
    console.log(text);
  }, [text]); // 每次 text 变化时 handleSubmit 都会变

  return (
    <>
      <input value={text} onChange={(e) => updateText(e.target.value)} />
      <ExpensiveTree onSubmit={handleSubmit} /> // 很重的组件，不优化会死的那种
    </>
  );
}
```

这个问题无解的原因在于，`callback` 内部对 `state` 的访问依赖于 JavaScript 函数的闭包。如果希望 `callback` 不变，那么访问的之前那个 `callback` 函数闭包中的 `state` 会永远是当时的值。那让我们看一下 React 文档里的答案吧：

```jsx
function Form() {
  const [text, updateText] = useState('');
  const textRef = useRef();

  useLayoutEffect(() => {
    textRef.current = text; // 将 text 写入到 ref
  });

  const handleSubmit = useCallback(() => {
    const currentText = textRef.current; // 从 ref 中读取 text
    alert(currentText);
  }, [textRef]); // handleSubmit 只会依赖 textRef 的变化。不会在 text 改变时更新

  return (
    <>
      <input value={text} onChange={e => updateText(e.target.value)} />
      <ExpensiveTree onSubmit={handleSubmit} />
    </>
  );
}

```

文档里给出的解法乍一看可能不太好理解，我们一步步慢慢来。首先，因为在函数式组件里没有了 `this` 来存放一些实例的变量，所以 React 建议使用 `useRef` 来存放一些会发生变化的值，`useRef` 并不再单单是为了 DOM 的 ref 准备的，同时也会[用来存放组件实例的属性](https://link.zhihu.com/?target=https%3A//reactjs.org/docs/hooks-faq.html%23is-there-something-like-instance-variables)。在 `updateText` 完成对 `text` 的更新后，再在 `useLayoutEffect` (等效于 `didMount` 和 `didUpdate`) 里写入 `textRef.current` 中。这样，在 `handleSubmit` 里取出的 `textRef` 中存放的值就永远是新值了。

是不是有一种恍然大悟的感觉。本质上我们想要达成的目标是以下几点：

1. 能充分利用一个函数式组件多次 `render` 时产生的相同功能的 `callback`
2. `callback` 能不受闭包限制，访问到这个函数式组件内部最新的状态



##### 在自定义 hooks 中

useCallback 在自定义 hooks 中的作用是用来缓存一个回调函数。在 React 组件中，每次渲染时，函数组件的所有代码都会重新执行一遍，因此如果每次渲染时都创建新的函数，可能会导致性能问题。

使用 useCallback 可以保证在依赖没有改变的情况下，每次渲染都返回同一个函数引用。这样可以减少子组件的不必要重新渲染，提高性能。

例如，我们可以在自定义的 hooks 中使用 useCallback 来缓存一个回调函数：

```tsx
import { useCallback } from 'react';

function useCustomHook() {
  const handleClick = useCallback(() => {
    console.log('Button clicked');
  }, []);

  return { handleClick };
}
```

**在上述的示例中，无论组件何时重新渲染，handleClick 回调函数都会保持相同的引用。这样，当传递给子组件时，子组件就不会因为父组件的重新渲染而重新触发渲染逻辑。**





#### useHistory

> 从React Router v5.1.0开始，新增了useHistory钩子（hook），如果是使用React >16.8.0，使用useHistory即可实现编程时页面跳转导航。
>
> **React Router 6 已经废弃 使用useLocation代替**

```js
const history = useHistory();
history.push("/home")
```







#### useReducer

[https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer)

[`useState`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usestate) 的替代方案。它接收一个形如 `(state, action) => newState` 的 reducer，并返回当前的 state 以及与其配套的 `dispatch` 方法。（如果你熟悉 Redux 的话，就已经知道它如何工作了。）



在某些场景下，`useReducer` 会比 `useState` 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。并且，使用 `useReducer` 还能给那些会触发深更新的组件做性能优化，因为[你可以向子组件传递 `dispatch` 而不是回调函数](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-avoid-passing-callbacks-down) 。



```jsx
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  // Tip: `dispatch` 不会在多次渲染时改变
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}

```

因为 `reducer` 其实是在下次 `render` 时才执行的，所以在 `reducer` 里，访问到的永远是新的 `props` 和 `state`。

> `useReducer` 返回的 `dispatch` 函数是自带了 `memoize` 的，不会在多次渲染时改变。所以如果你想同时把 `state` 作为 context 传递下去，请分成两个 context 来声明。





#### useInsertionEffect

[https://react.dev/reference/react/useInsertionEffect](https://react.dev/reference/react/useInsertionEffect)

`useInsertionEffect` is for CSS-in-JS library authors. Unless you are working on a CSS-in-JS library and need a place to inject the styles, you probably want [`useEffect`](https://react.dev/reference/react/useEffect) or [`useLayoutEffect`](https://react.dev/reference/react/useLayoutEffect) instead.

useInsertionEffect是为CSS-in-JS库作者准备的。除非你正在做一个CSS-in-JS库，并且需要一个地方来注入样式，否则你可能会想要useEffect或uselayouteeffect。



#### forwardRef

React.forwardRef:

`React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。这种技术并不常见，但在以下两种场景中特别有用：

- [转发 refs 到 DOM 组件](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
- [在高阶组件中转发 refs](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-in-higher-order-components)

 `React.forwardRef` 接受渲染函数作为参数。React 将使用 `props` 和 `ref` 作为参数来调用此函数。此函数应返回 React 节点。

```jsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

在上述的示例中，React 会将 `<FancyButton ref={ref}>` 元素的 `ref` 作为第二个参数传递给 `React.forwardRef` 函数中的渲染函数。该渲染函数会将 `ref` 传递给 `<button ref={ref}>` 元素。

因此，当 React 附加了 ref 属性之后，`ref.current` 将直接指向 `<button>` DOM 元素实例。





#### createRef和useRef

[精读《useRef 与 createRef 的区别》](https://juejin.cn/post/6844904079164964878)

[https://zh-hans.reactjs.org/docs/react-api.html#reactcreateref](https://zh-hans.reactjs.org/docs/react-api.html#reactcreateref)

`React.createRef` 创建一个能够通过 ref 属性附加到 React 元素的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)。

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.inputRef = React.createRef();
  }

  render() {
    return <input type="text" ref={this.inputRef} />;
  }

  componentDidMount() {
    this.inputRef.current.focus();
  }
}

```



useRef:

`useRef` 返回一个可变的 ref 对象，其 `.current` 属性被初始化为传入的参数（`initialValue`）。返回的 ref 对象在组件的整个生命周期内保持不变。

一个常见的用例便是命令式地访问子组件：

```jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```



两者的区别：

**createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用。**

**`useRef` 仅能用在 FunctionComponent，`createRef` 仅能用在 ClassComponent**

> createRef  并没有 Hooks 的效果，其值会随着 FunctionComponent 重复执行而不断被初始化：



#### useImperativeHandle

[https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle)

```tsx
useImperativeHandle(ref, createHandle, [deps])
```

`useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。在大多数情况下，应当避免使用 ref 这样的命令式代码。`useImperativeHandle` 应当与 [`forwardRef`](https://zh-hans.reactjs.org/docs/react-api.html#reactforwardref) 一起



#### useTransition

> react 18

低优先级执行

```ts
  const [isPending, startTransition] = useTransition()
```

---

[https://react.dev/reference/react/startTransition](https://react.dev/reference/react/startTransition)

startTransition lets you update the state without blocking the UI.

startTransition 的作用是告诉 React 在下一个渲染周期中，有一些优先级较低的更新，可以推迟执行，以便在同一渲染周期内将多个更新批量处理，以提高性能。

使用 startTransition 在函数组件内部对某个代码块进行包装，可以将该代码块标记为优先级较低，React 将在下一个渲染周期内异步执行这个代码块，以避免阻塞更重要的任务。

通过使用 startTransition，React 可以根据需求进行优化，比如将多个状态更新批量执行，减少不必要的渲染，并与浏览器的空闲时间协作，以提升用户体验。



#### useDeferredValue

> react 18

延迟值







### 自定义hook





#### useCatchError

监听全局错误信息

```ts
export const useCatchError = () => {
  const TOAST_DURATION = 5000

  const handleError = (e: ErrorEvent) => {
    const { error } = e
    if (error?.message) {
      Toast.fail({
        message: error.message,
        duration: TOAST_DURATION,
      })
    }
  }

  const unhandledRejection = (e: PromiseRejectionEvent) => {
    const { reason } = e
    if (reason?.message) {
      Toast.fail({
        message: reason.message,
        duration: TOAST_DURATION,
      })
    }
  }

  useEffect(() => {
    window.addEventListener("error", handleError, true)
    window.addEventListener("unhandledrejection", unhandledRejection)

    return () => {
      window.removeEventListener("error", handleError, true)
      window.removeEventListener("unhandledrejection", unhandledRejection)
    }
  }, [])
}
```



#### useScreen 

监听屏幕大小信息

```ts
export const useScreen = () => {
  const dispatch = useAppDispatch()

  const onResize = () => {
    dispatch(
      // redux store 
      setScreenInfo({
        width: window.innerWidth,
        height: window.innerHeight,
      }),
    )
  }

  useEffect(() => {
    onResize()
    window.addEventListener("resize", onResize)
    return () => {
      window.removeEventListener("resize", onResize)
    }
  }, [])
}
```





#### useRouteNavigation

```ts
export const useRouteNavigation = () => {
  const navigate = useNavigate()
  const location = useLocation()
  // 维护一个路由栈
  const [locationArr, setLocationArr] = useState<Location[]>([])
  const [locationIndex, setLocationIndex] = useState(0)

  const calc = (location: Location) => {
    const index = locationArr.findIndex(
      (item) => item.pathname === location.pathname,
    )
    if (index === -1) {
      setLocationArr((pre) => [...pre, location])
      setLocationIndex((pre) => pre + 1)
    } else {
      setLocationIndex(index)
    }
  }

  const canGoBack = useMemo(() => {
    return locationIndex > 0 && locationArr.length > 1
  }, [locationIndex, locationArr])

  const canGoForward = useMemo(() => {
    return locationIndex < locationArr.length - 1 && locationArr.length > 1
  }, [locationIndex, locationArr])

  useEffect(() => {
    calc(location)
  }, [location])

  const goBack = () => {
    if (canGoBack) {
      navigate(-1)
    }
  }

  const goForward = () => {
    if (canGoForward) {
      navigate(1)
    }
  }

  return {
    canGoBack,
    canGoForward,
    goBack,
    goForward,
  }
}
```







#### usePrevious 

[uidotdev/usehooks 是怎么实现 usePrevious 的？](https://blog.shadowfish0.top/2023-06-10-useprevious.html)

[https://react.docschina.org/docs/hooks-faq.html#how-to-get-the-previous-props-or-state](https://react.docschina.org/docs/hooks-faq.html#how-to-get-the-previous-props-or-state)

> 自定义 hook

获取上一轮的 props 或 state

```tsx
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}
```

测试：

```tsx
function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);
  return <h1>Now: {count}, before: {prevCount}</h1>;
}
```

`usePrevious`就是利用了`useRef`的值更改后不会触发重渲染的特性，来实现的保存变量的上个状态

并且`useEffect`内的函数是在完成了渲染后再执行

由于`useRef`的值不会触发重新渲染，所以页面上渲染的previous的值和它现在实际存的值是**不一样**的。





#### useClassroomStyle

> 自定义hook

当网页放大缩小时 节点伴随着动画效果同时放大缩小

根据传入参数计算出width height minWidth minHeight 同时加入动画transition

```tsx
export const useClassroomStyle = ({
  minimumHeight,
  minimumWidth,
  delayBeforeScale,
}: {
  minimumHeight: number;
  minimumWidth: number;
  delayBeforeScale: number;
}) => {
  const minimumSize = useMemo(
    () => ({ height: minimumHeight, width: minimumWidth }),
    [minimumHeight, minimumWidth],
  );
  const { shareUIStore } = useStore();

  const { classroomViewportSize } = shareUIStore;

  const postStyle = useMemo(
    () =>
      classroomViewportSize.width !== 0 && classroomViewportSize.height !== 0
        ? Object.assign(
            {
              width: classroomViewportSize.width,
              height: classroomViewportSize.height,
              minWidth: minimumSize.width,
              minHeight: minimumSize.height,
            },
            { transition: 'all .3s' },
          )
        : {},
    [
      classroomViewportSize.width,
      classroomViewportSize.height,
      minimumSize.width,
      minimumSize.height,
    ],
  );

  return postStyle;
};

```

使用： 

```tsx
const FixedAspectRatioContainer: React.FC<FixedAspectRatioProps> = observer(
  ({ children, minimumWidth = 0, minimumHeight = 0, delayBeforeScale = 500 }) => {
    
    // 注意这个
    const style = useClassroomStyle({ minimumHeight, minimumWidth, delayBeforeScale });

    return (
      <div className="flex bg-black justify-center items-center h-screen w-screen">
        <div style={style} className="w-full h-full relative">
          {children}
        </div>
      </div>
    );
  },
);
```









#### useMap

中央区域保持恒定的宽高比

```ts
// page
export const PAGE_RATIO = 1.777778 // page ratio  16:9
export const PAGE_MIN_WIDTH = 1440 // page min width 1440
export const PAGE_MIN_HEIGHT = Math.floor(PAGE_MIN_WIDTH / PAGE_RATIO) // page min height
export const PAGE_MAX_WIDTH = PAGE_MIN_WIDTH * 2 // page max width
export const PAGE_MAX_HEIGHT = Math.floor(PAGE_MAX_WIDTH / PAGE_RATIO) // page max height
```



```ts
export const useMap = () => {
  const { width: pageWidth, height: pageHeight } = useSelector((state: RootState) => state.globalInfo.pageInfo);
  const position = useSelector((state: RootState) => state.globalInfo.position)

  let mapWidth = 0
  let mapHeight = 0
  let scale = 1 // 和 min width/height 的比例

  if (pageWidth / pageHeight >= PAGE_RATIO) {
    mapWidth = pageHeight * PAGE_RATIO;
    mapHeight = pageHeight;
    scale = pageHeight / PAGE_MIN_HEIGHT
  } else {
    mapWidth = pageWidth;
    mapHeight = pageWidth / PAGE_RATIO;
    scale = pageWidth / PAGE_MIN_WIDTH
  }

  if (mapWidth < PAGE_MIN_WIDTH || mapHeight < PAGE_MIN_HEIGHT) {
    mapWidth = PAGE_MIN_WIDTH
    mapHeight = PAGE_MIN_HEIGHT
    scale = 1
  } else if (mapWidth > PAGE_MAX_WIDTH || mapHeight > PAGE_MAX_HEIGHT) {
    mapWidth = PAGE_MAX_WIDTH
    mapHeight = PAGE_MAX_HEIGHT
    scale = mapWidth / PAGE_MIN_WIDTH
  }

  mapWidth = Math.floor(mapWidth)
  mapHeight = Math.floor(mapHeight)

  const { bgPositionX, bgPositionY } = useMemo(() => {
    let [x, y] = position
    // bgPositionX/bgPositionY 均需要负值
    let bgPositionX = 0
    let bgPositionY = 0
    if (pageHeight && mapHeight) {
      if (pageWidth < mapWidth) {
        // map need move
        let centerX = pageWidth / 2 // 到达 centerX 时背景开始移动
        let max = -(mapWidth - pageWidth) // 负值
        if (x > centerX) {
          bgPositionX = centerX - x // 负值
          bgPositionX = bgPositionX <= max ? max : bgPositionX
        }
      }
      if (pageHeight < mapHeight) {
        // map need move
        let centerY = pageHeight / 2 // 到达 centerY 时背景开始移动
        let max = -(mapHeight - pageHeight) / 2 // 负值
        bgPositionY = -max
        if (y > centerY) {
          const dis = centerY - y// 负值
          bgPositionY = bgPositionY + dis
          bgPositionY = bgPositionY <= max ? max : bgPositionY
        }
      }
    }

    return {
      bgPositionX,
      bgPositionY
    }

  }, [mapWidth, mapHeight, position, pageWidth, pageHeight])



  return {
    // page
    pageWidth: pageWidth,  // 页面宽度
    pageHeight: pageHeight, // 页面高度
    // map
    mapWidth: mapWidth,  // 中央地图宽度
    mapHeight: mapHeight, // 中央地图高度
    bgPositionX, // 中央地图背景X偏移值 
    bgPositionY, // 中央地图背景X偏移值 
 		// TIP: when scale, width/height will change,figure x/y will not change
    scale: Number(scale.toFixed(4)) // 地图缩放值
  }

}
```











### React.lazy

`React.lazy()` 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。



```jsx
// 这个组件是动态加载的
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

> 请注意，渲染 `lazy` 组件依赖该组件渲染树上层的 `<React.Suspense>` 组件。这是指定加载指示器（loading indicator）的方式。



### React.Suspense

`React.Suspense` 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。目前，懒加载组件是 `<React.Suspense>` 支持的**唯一**用例：

```jsx
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```





### React.cloneElement

[https://zh-hans.reactjs.org/docs/react-api.html#cloneelement](https://zh-hans.reactjs.org/docs/react-api.html#cloneelement)



```tsx
React.cloneElement(
  element,
  [props],
  [...children]
)
```



以 `element` 元素为样板克隆并返回新的 React 元素。**返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果**。新的子元素将取代现有的子元素，而来自原始元素的 `key` 和 `ref` 将被保留。



`React.cloneElement()` 几乎等同于：

```
<element.type {...element.props} {...props}>{children}</element.type>
```







### 陷阱Literal Array与Literal Object



```jsx
{this.props.items.map(i =>
    <Cell data={i} options={this.props.options || []} />
)}
```



若options为空，则会使用[]。[]每次会生成新的Array，因此导致Cell每次的props都不一样，导致需要重绘。解决方法如下:

```jsx
const default = [];
{this.props.items.map(i =>
  <Cell data={i} options={this.props.options || default} />
)}
```





### 内联函数





函数也经常作为props传递，由于每次需要为内联函数创建一个新的实例，所以每次function都会指向不同的内存地址。比如：

```jsx
render() {
     <MyInput onChange={e => this.props.update(e.target.value)} />;
}
```

以及：

```jsx
update(e) {
     this.props.update(e.target.value);
}
render() {
     return <MyInput onChange={this.update.bind(this)} />;
}
```

注意第二个例子也会导致创建新的函数实例。

**为了解决这个问题，需要提前绑定this指针：**

```jsx
constructor(props) {
    super(props);
    this.update = this.update.bind(this);
  }
  update(e) {
    this.props.update(e.target.value);
  }
  render() {
    return <MyInput onChange={this.update} />;
  }
```

>这也是性能优化中要做到的，因为render()可能会调用多次，所以要把把绑定this操作移到render()外面



### `useState` 不会自动合并更新对象

与 class 组件中的 `setState` 方法不同，`useState` 不会自动合并更新对象。你可以用函数式的 `setState` 结合展开运算符来达到合并更新对象的效果。

```tsx
setState(prevState => {
  // 也可以使用 Object.assign
  return {...prevState, ...updatedValues};
});
```





## React 18

### **useTransition**

[https://juejin.cn/post/7038402899860258847](https://juejin.cn/post/7038402899860258847)

[https://reactjs.org/docs/concurrent-mode-patterns.html](https://reactjs.org/docs/concurrent-mode-patterns.html)

使用 **useTransition** 时，**react** 会以 **Concurrent** 模式来协调 **fiber tree**。**Concurrent** 模式下，**协调**过程是**并行可中断**的，**渲染进程**不会长时间被阻塞，使得用户操作可以及时得到响应，极大提升了用户体验。

对比**防抖 - debounce**、**节流 - throttle**，**useTransition** 有如下优势:

- 更新协调过程是**可中断**的，**渲染引擎**不会长时间被**阻塞**，用户可以及时得到响应；
- 不需要开发人员去做额外的考虑，整个优化过程交给 **react** 和**浏览器**即可；



###  SSR

[Next.js 14 Tutorial - 48 - Suspense for SSR](https://www.youtube.com/watch?v=NdSthd1Ek8Q&list=PLC3y8-rFHvwjOKd6gdf4QtV1uYNiQnruI&index=48)
Use the `<Suspense>` component to unlock two major SSR features:

1. HTML streaming on the server  (Suspense)
1. Selective hydration on the client

---

HTML streaming on the Server

You don't have to fetch everything befor you can show anything 

If a particular section delays the initial HIML, it can be seamlessly integrated into the stream later

**This is the essence of how Suspense facilitates server-side HTML streaming**

---

Selective Hydration on the Client contd  (选择性水合)

Selective Hydration offers a solution to the third issue: the necessity to "hydrate everything to interact with anything'

React begins hydrating as soon as possible, enabling interactions with elements like the header and side navigation without waiting for the main content to be hydrated.(无需等待主要内容水合) This process is managed automatically by React

In scenarios where multiple components are awaiting hydration, React prioritizeshydration based on user interactions (React 根据用户交互优先考虑水合作用)



### React Server Components(RSC)

> CSR => SSR => Suspense for SSR

Suspense for SSR brought us closer to a seamless rendering experience
Challenges:

* Increased bundle sizes leading to excessive downloads for users  (捆绑包大小增加导致用户下载过多)
* Unnecessary hydration delaying interactivity  (不必要的水合会延迟互动)
* Extensive client-side processing that could result in poor performance (大量的客户端处理可能会导致性能不佳)

----

This approach aims to leverage the strengths of both server and clientenvironments, optimizing for efficiency, load times, and interactivity

这种方法旨在利用服务器和客户端环境的优势，优化效率、加载时间和交互性

The architecture introduces a dual-component model

* Client Components  **(rendered once on the server and then on the client)**
* Server Components  **(rendered only on the server)**

This distinction is not based on the functionality of the components but rather onwhere they execute and the specific environments they are designed to interact with (这种区别不是基于组件的功能，而是基于它们执行的位置以及它们设计用于交互的特定环境)

#### Benefits 优点

**Reduced Bundle Sizes** (减少打包体积)

* code to the client, allowing large dependencies ServerComponents do not send to remain server-side (服务器组件不向客户端发送代码，从而允许较大的依赖关系保持在服务器端)
* This benefits users with slower internet connections or less capable devices by eliminating the need to download parse, and execute JavaScript for these components (这使互联网连接速度较慢或设备性能较差的用户无需为这些组件下载、解析和执行JavaScript，从而受益匪浅)

**Direct Access to server-side Resources** (直接访问服务器端资源)

**Enhanced Security** (增强的安全性)

* Server Components' exclusive server-side execution enhances security by keeping sensitive data and logic, including tokens and APl keys, away from the client-side

**Improved Data Fetching** (改进的数据获取)

**Caching** (高速缓存)

**Faster Initial Page Load and First Contentful Paint** (更快的初始页面加载和第一次内容绘制)

**Improved SEO**

**Efficient Streaming** (高效流媒体)







# Performance Tools

[https://legacy.reactjs.org/docs/perf.html](https://legacy.reactjs.org/docs/perf.html)

```ts
import Perf from 'react-addons-perf'; // ES6
```







## 遇到的问题



### state初始化问题

[ https://blog.csdn.net/LiuChengYu520/article/details/82867513 ]( https://blog.csdn.net/LiuChengYu520/article/details/82867513 )



```
Warning: Can't call setState on a component that is not yet mounted.

This is a no-op, but it might indicate a bug in your application.

Instead, assign to `this.state` directly or define a `state = {};` 
```





不能再一个组件尚未mounted时调用 setState() 方法，这是一个空操作，但是可能会导致bug，所以， 直接给`this.state` 赋值，或者定义成`state = {};`



**问题出现在直接给this.state赋值了**



```js
  constructor(props){
    super(props)
    this.state = { date: new Date(),counter: 1  };
  }
```





要注意这一点







### Warning: Legacy context API

[ https://github.com/JedWatson/react-select/issues/3703 ]( https://github.com/JedWatson/react-select/issues/3703 )



发现一个warning



```js
Warning: Legacy context API has been detected within a strict-mode tree: 
    in StrictMode
    in ErrorBoundary
    in Unknown (created by HotExportedComponent)
    in AppContainer (created by HotExportedComponent)
    in HotExportedComponent

Please update the following components: LocaleReceiver

Learn more about this warning here:
https://fb.me/react-strict-mode-warnings
```



----



原因：



From React 16.3 the old Context API was deprecated and
it's rising warnings when runng in strict-mode. 



 在React 16.3中，旧的上下文API被弃用了，当以严格模式运行时，它发出了不断上升的警告。 

<details class="details-overlay details-reset dropdown hx_dropdown-fullscreen position-relative float-left d-inline-block reaction-popover-container reactions-menu js-reaction-popover-container" style="box-sizing: border-box; display: inline-block !important; position: relative; float: left !important;"><summary class="btn-link reaction-summary-item add-reaction-btn" aria-label="Add your reaction" aria-haspopup="menu" role="button" style="box-sizing: border-box; display: inline-block; cursor: pointer; padding: 9px 15px 7px; font-size: inherit; color: rgb(3, 102, 214); text-decoration: none; white-space: nowrap; user-select: none; background-color: initial; border: 0px; -webkit-appearance: none; opacity: 0; transition: opacity 0.1s ease-in-out 0s; float: left; line-height: 18px; list-style: none;"><svg class="octicon octicon-smiley" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8s3.58 8 8 8 8-3.58 8-8-3.58-8-8-8zm4.81 12.81a6.72 6.72 0 01-2.17 1.45c-.83.36-1.72.53-2.64.53-.92 0-1.81-.17-2.64-.53-.81-.34-1.55-.83-2.17-1.45a6.773 6.773 0 01-1.45-2.17A6.59 6.59 0 011.21 8c0-.92.17-1.81.53-2.64.34-.81.83-1.55 1.45-2.17.62-.62 1.36-1.11 2.17-1.45A6.59 6.59 0 018 1.21c.92 0 1.81.17 2.64.53.81.34 1.55.83 2.17 1.45.62.62 1.11 1.36 1.45 2.17.36.83.53 1.72.53 2.64 0 .92-.17 1.81-.53 2.64-.34.81-.83 1.55-1.45 2.17zM4 6.8v-.59c0-.66.53-1.19 1.2-1.19h.59c.66 0 1.19.53 1.19 1.19v.59c0 .67-.53 1.2-1.19 1.2H5.2C4.53 8 4 7.47 4 6.8zm5 0v-.59c0-.66.53-1.19 1.2-1.19h.59c.66 0 1.19.53 1.19 1.19v.59c0 .67-.53 1.2-1.19 1.2h-.59C9.53 8 9 7.47 9 6.8zm4 3.2c-.72 1.88-2.91 3-5 3s-4.28-1.13-5-3c-.14-.39.23-1 .66-1h8.59c.41 0 .89.61.75 1z"></path></svg></summary></details>
Please update the following components: LocaleReceiver



一开始以为要更新组件，结果这个组件在anti组件库里面，后面发现React 16.3默认添加

```jsx
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```





 

解决：

[ https://github.com/ant-design/ant-design/issues/22604 ]( https://github.com/ant-design/ant-design/issues/22604 )



删除了React.StrictMode就好了，没有warning了









### ComponentType and ReactType and ElementType

[React.ComponentType<Props> ](https://flow.org/en/docs/react/types/#toc-react-componenttype)

This is a union of a class component or a stateless functional component. This is the type you want to use for functions that receive or return React components such as higher-order components or other utilities.

```tsx
type Props = {
  foo: number,
  bar: number,
};

function createMyElement<C: React.ComponentType<Props>>(
  Component: C,
): React.Element<C> {
  return <Component foo={1} bar={2} />;
}
```

---

'ReactType' is deprecated.ts(6385)

ReactType 被干掉了 不建议使用

---

ElementType

Similar to [`React.ComponentType`](https://flow.org/en/docs/react/types/#toc-react-componenttype) except it also includes JSX intrinsics (strings).

```tsx
type ElementType =
  | string
  | React.ComponentType<any>;

```







### StrictMode导致组件渲染两次

[https://juejin.cn/post/6858508463274885134](https://juejin.cn/post/6858508463274885134)

```tsx
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

`StrictMode`它帮助我们检测到渲染期生命周期的预期之外的副作用。

`React.StrictMode` 不能马上检测到副作用，但是它可以通过故意调用一些关键函数两次，来帮助我们发现副作用。

这个行为肯定对性能有一些影响，但我们不应该担心，因为它只在开发而不是生产环境中发生。 

**(可以关掉)**



### useEffect 陷阱

[https://zhuanlan.zhihu.com/p/84697185](https://zhuanlan.zhihu.com/p/84697185)

**使用useEffect，不要调用函数层次太多，代码应该一眼看清楚哪些函数和变量会被useEffect调用（被使用到的变量一定要加到deps依赖中）**。





### DetailedHTMLProps

[https://stackoverflow.com/questions/58418950/whats-the-difference-between-detailedhtmlprops-and-htmlattributes](https://stackoverflow.com/questions/58418950/whats-the-difference-between-detailedhtmlprops-and-htmlattributes)

[https://github.com/DefinitelyTyped/DefinitelyTyped/blob/1349b640d4d07f40aa7c1c6931f18e3fbf667f3a/types/react/index.d.ts#L1342](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/1349b640d4d07f40aa7c1c6931f18e3fbf667f3a/types/react/index.d.ts#L1342)

```tsx
 type DetailedHTMLProps<E extends HTMLAttributes<T>, T> = ClassAttributes<T> & E;
```





### custom HTML attributes

[https://stackoverflow.com/questions/40093655/how-do-i-add-attributes-to-existing-html-elements-in-typescript-jsx/56109822#56109822](https://stackoverflow.com/questions/40093655/how-do-i-add-attributes-to-existing-html-elements-in-typescript-jsx/56109822#56109822)



React type definition file (by default - `index.d.ts` when staring with `create-react-app`) contain list of all the standard HTML elements, as well as known attributes.

In order to allow custom HTML attributes, you need to define it's typing. Do that by expanding `HTMLAttributes` interface:

```tsx
declare module 'react' {
  interface HTMLAttributes<T> extends AriaAttributes, DOMAttributes<T> {
    // extends React's HTMLAttributes
    custom?: string;
  }
```





### 非必要渲染

[https://ant-design.antgroup.com/docs/blog/render-times-cn](https://ant-design.antgroup.com/docs/blog/render-times-cn)

在示例中，虽然 `prop1` 和 `prop2` 并没有变化，但是显然 MyContext 里的 `value` 是一个新的 Object 导致子组件即便 `prop1` 没有变化也会重新渲染。因而我们需要对 Context `value` 进行 Memo：

```tsx
const context = React.useMemo(() => ({ prop1, prop2 }), [prop1, prop2]);

return (
  <MyContext.Provider value={context}>
    <Child />
  </MyContext.Provider>
);
```







### useRef不能作为依赖项

[useRef不能作为依赖项](https://juejin.cn/s/useref%20%E8%83%BD%E4%BD%9C%E4%B8%BA%E4%BE%9D%E8%B5%96%E9%A1%B9%E5%90%97)

React 中的 `useRef` 不能作为依赖项。

`useRef` 返回的是一个稳定的对象引用，不会在组件重新渲染时发生变化。因此，在函数组件中使用 `useRef` 创建的引用不会触发组件重新渲染。与此相反，依赖项应该是组件中可能会导致重新渲染的状态变量、props、函数等等。**如果使用 `useRef` 作为依赖项，React 将无法检测到变化并触发重新渲染，这会导致问题。**





## react-scripts



### test nowatch

[https://github.com/facebook/create-react-app/issues/1137](https://github.com/facebook/create-react-app/issues/1137)

Allow `npm test` to not run in watch mode for better cross-platform / tooling CI.



最后解决方案：

在package.json中的scripts增加

```

  "scripts": {
    "test:nowatch": "cross-env CI=true react-scripts test",
  },
```





## **rc-util**

[https://util.vercel.app/](https://util.vercel.app/)

Common Utils For React Component










[1]: http://www.ruanyifeng.com/blogimg/asset/2015/bg2015033110.png
[2]: https://pic1.zhimg.com/80/v2-a32cb02859ea4ac0f8f50f1ec885d85c_hd.jpg

