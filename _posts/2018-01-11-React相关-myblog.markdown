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



### this.props.children
this.props 对象的属性与组件的属性一一对应，但是有一个例外，就是 this.props.children

```
var NotesList = React.createClass({
  render: function() {
    return (
      <ol>
      {
        React.Children.map(this.props.children, function (child) {
          return <li>{child}</li>;
        })
      }
      </ol>
    );
  }
});

ReactDOM.render(
  <NotesList>
    <span>hello</span>
    <span>world</span>
  </NotesList>,
  document.body
);
```

上面代码的 NoteList 组件有两个 span 子节点，它们都可以通过 this.props.children 读取，运行结果如下。

![enter description here][1]

**这里需要注意， this.props.children 的值有三种可能：如果当前组件没有子节点，它就是 undefined ;如果有一个子节点，数据类型是 object ；如果有多个子节点，数据类型就是 array 。所以，处理 this.props.children 的时候要小心。**


React 提供一个工具方法 React.Children 来处理 this.props.children 。我们可以用 React.Children.map 来遍历子节点，而不用担心 this.props.children 的数据类型是 undefined 还是 object。

[https://reactjs.org/docs/react-api.html#react.children](https://reactjs.org/docs/react-api.html#react.children)

`React.Children.map(children, function[(thisArg)])`

上调用包含在每一个直系子的功能children与this设置为thisArg。如果children是键控片段或数组，它将被遍历：该函数永远不会传递容器对象。如果孩子null或undefined返回null或undefined而不是一个数组。


使用方法：
```
React.Children.map(this.props.children, function (child) {
    return <li>{child}</li>;
})
```


`React.Children.forEach(children, function[(thisArg)])`



### 获取真实的DOM节点
组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。

但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 ref 属性
```
var MyComponent = React.createClass({
  handleClick: function() {
    this.refs.myTextInput.focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input" onClick={this.handleClick} />
      </div>
    );
  }
});

ReactDOM.render(
  <MyComponent />,
  document.getElementById('example')
);
```
上面代码中，组件 MyComponent 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的 DOM 节点，虚拟 DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个 ref 属性，然后 this.refs.[refName] 就会返回这个真实的 DOM 节点。


需要注意的是，由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定 Click 事件的回调函数，确保了只有等到真实 DOM 发生 Click 事件之后，才会读取 this.refs.[refName] 属性。


### 表单
用户在表单填入的内容，属于用户跟组件的互动，所以不能用 this.props 读取


```
var Input = React.createClass({
  getInitialState: function() {
    return {value: 'Hello!'};
  },
  handleChange: function(event) {
    this.setState({value: event.target.value});
  },
  render: function () {
    var value = this.state.value;
    return (
      <div>
        <input type="text" value={value} onChange={this.handleChange} />
        <p>{value}</p>
      </div>
    );
  }
});

ReactDOM.render(<Input/>, document.body);
```

上面代码中，文本输入框的值，不能用 this.props.value 读取，而要定义一个 onChange 事件的回调函数，通过 event.target.value 读取用户输入的值。textarea 元素、select元素、radio元素都属于这种情况


### Ajax
组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 componentDidMount 方法设置 Ajax 请求，等到请求成功，再用 this.setState 方法重新渲染 UI

```
var UserGist = React.createClass({
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
          username: lastGist.owner.login,
          lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return (
      <div>
        {this.state.username}'s last gist is
        <a href={this.state.lastGistUrl}>here</a>.
      </div>
    );
  }
});

ReactDOM.render(
  <UserGist source="https://api.github.com/users/octocat/gists" />,
  document.body
);
```


上面代码使用 jQuery 完成 Ajax 请求，这是为了便于说明。React 本身没有任何依赖，完全可以不用jQuery，而使用其他库。



### React中constructor(props){}

在React Class中设置state的初始值或者绑定事件时
为什么在constructor(){} 中加上super()
如果去掉super()会怎样呢！
```
    constructor() {  
      this.state = {searchStr: ''};  
      this.handleChange = this.handleChange.bind(this);  
    }  
```
运行这段代码时，会发现编译错误：

提示没有在this之前加上super()
其实就是少了super()，导致了this的 Reference Error

```
    class MyComponent extends React.Component {  
      constructor() {  
        console.log(this); // Reference Error  
      }  
      
      render() {  
        return <div>Hello {this.props.name}</div>;  
      }  
    }  
```

----------

```
    constructor() {  
      super();  
      this.state = {searchStr: ''};  
      this.handleChange = this.handleChange.bind(this);  
    }  
```
那么React的官方例子中都是加上了props作为参数
```
    constructor(props) {  
      super(props);  
      this.state = {searchStr: ''};  
      this.handleChange = this.handleChange.bind(this);  
    }  
```



那它们的区别在哪儿呢
What's the difference between “super()” and “super(props)” in React when using es6 classes?
借用下stackoverflow上的解释

There is only one reason when one needs to pass props to super():
When you want to access this.props in constructor.
(Which is probably redundant since you already have a reference to it.)


**只有一个理由需要传递props作为super()的参数，那就是你需要在构造函数内使用this.props**
那官方提供学习的例子中都是写成super(props)，所以说写成super(props)是完全没问题的，也建议就直接这样写


### reducer为什么叫reducer
我们注意到redux的官方文档里专门有一句对reducer命名的解释：

It's called a reducer because it's the type of function you would pass to Array.prototype.reduce(reducer, ?initialValue)

中文版的文档把这一句话翻译成了：

之所以称作 reducer 是因为它将被传递给 Array.prototype.reduce(reducer, ?initialValue) 方法

我们要注意到这里的中文翻译理解其实是错误的。原文的本意并不是说redux里的reducer会被传入到 Array.prototype.reduce 这个方法中。真的要翻译的话，应该翻译为：

**之所以将这样的函数称之为reducer，是因为这种函数与被传入 Array.prototype.reduce(reducer, ?initialValue) 的回调函数属于相同的类型。**

为什么这么讲呢？我们来看一下array使用reduce方法的具体例子：
```
// 以下代码示例来自 MDN JavaScript 文档

/* 这里的callback是和reducer非常相似的函数
 * arr.reduce(callback, [initialValue])
 */

var sum = [0, 1, 2, 3].reduce(function(acc, val) {
  return acc + val;
}, 0);
// sum = 6

/* 注意这当中的回调函数 (prev, curr) => prev + curr
 * 与我们redux当中的reducer模型 (previousState, action) => newState 看起来是不是非常相似呢
 */
[0, 1, 2, 3, 4].reduce( (prev, curr) => prev + curr );
```


为了进一步加深理解，我们再了解一下reduce是什么东西，这个名词其实是函数式编程当中的一个术语，在更多的情况下，reduce操作被称为Fold折叠（下图来自维基百科）。



![enter description here][2]


直观起见，我们还是拿JavaScript来理解。reduce属于一种高阶函数，它将其中的回调函数reducer递归应用到数组的所有元素上并返回一个独立的值。这也就是“缩减”或“折叠”的意义所在了。


**总而言之一句话，redux当中的reducer之所以叫做reducer，是因为它和 Array.prototype.reduce 当中传入的回调函数非常相似。**



### JSX 中的 If-Else
你没法在JSX中使用 if-else 语句，因为 JSX 只是函数调用和对象创建的语法糖。看下面这个例子：

```
// This JSX:
<div id={if (condition) { 'msg' }}>Hello World!</div>

// Is transformed to this JS:
React.createElement("div", {id: if (condition) { 'msg' }}, "Hello World!");
```

这是不合语法的 JS 代码。不过你可以采用三元操作表达式：
```
React.render(<div id={condition ? 'msg' : ''}>Hello World!</div>, mountNode);
```


当三元操作表达式不够健壮，你也可以使用 if 语句来决定应该渲染那个组件。
```
var loginButton;
if (loggedIn) {
  loginButton = <LogoutButton />;
} else {
  loginButton = <LoginButton />;
}

return (
  <nav>
    <Home />
    {loginButton}
  </nav>
)
```





### JSX 根节点的最大数量
目前， 一个 component 的 render，只能返回一个节点。如果你需要返回一堆 div ， 那你必须将你的组件用 一个div 或 span 或任何其他的组件包裹。

切记，JSX 会被编译成常规的 JS； 因此返回两个函数也就没什么意义了，同样地，千万不要在三元操作符中放入超过一个子节点。


### Container Component模式
一个container负责数据的获取，然后渲染它对应的下级component。就这些而已。

“对应的”的意思是他们拥有共同的名称：

StockWidgetContainer => StockWidget
TagCloudContainer => TagCloud
PartyPooperListContainer => PartyPooperList

为什么要用Containers呢？

假设我们需要做一个展示评论的组建。在你不知道container components模式之前，你会把所有的东西都放在一个里面：
```
// CommentList.js
class CommentList extends React.Component {
  constructor() {
    super();
    this.state = { comments: [] }
  }
  componentDidMount() {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: function(comments) {
        this.setState({comments: comments});
      }.bind(this)
    });
  }
  render() {
    return <ul> {this.state.comments.map(renderComment)} </ul>;
  }
  renderComment({body, author}) {
    return <li>{body}—{author}</li>;
  }
}
```
你的这个组建要同时负责获取数据和展示数据。当然，这种做法没有什么错的，但是你没有很好的利用React的一些优势。

复用性
除非在一个一模一样的使用环境下，你无法重用CommentList组建。


数据结构
你的展示组建对需要的数据架构有具体的要求，而PropTypes能够很好地满足这个要求。
展示组建对数据结构有一定的要求，但是却没有办法限制数据类型。如果传入的json结构发生了改变，那么组建就会down掉，并不会抛出任何错误。


如果我们使用container
```
// CommentListContainer.js
class CommentListContainer extends React.Component {
  constructor() {
    super();
    this.state = { comments: [] }
  }
  componentDidMount() {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: function(comments) {
        this.setState({comments: comments});
      }.bind(this)
    });
  }
  render() {
    return <CommentList comments={this.state.comments} />;
  }
}
```
同时，我们修改一下CommentList让它可以接受一个comments的prop。
```
// CommentList.js
class CommentList extends React.Component {
  constructor(props) {
    super(props);
  }
  render() { 
    return <ul> {this.props.comments.map(renderComment)} </ul>;
  }
  renderComment({body, author}) {
    return <li>{body}—{author}</li>;
  }
}
```


所以，我们这么做获得了什么？

我们获取了很多东西…
我们分离了数据获取和数据渲染的逻辑。
我们让CommentList变成了可复用的组建。
我们允许CommentList可以通过PropTypes来限制props数据个格式。如果props格式出错，就会报错。





### 事件处理



[网页](https://doc.react-china.org/docs/handling-events.html)

React 元素的事件处理和 DOM元素的很相似。但是有一点语法上的不同:

* React事件绑定属性的命名采用驼峰式写法，而不是小写。
* 如果采用 JSX 的语法你需要传入一个函数作为事件处理函数，而不是一个字符串(DOM元素的写法)

例如，传统的 HTML：

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

React 中稍稍有点不同：

```html
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

在 React 中另一个不同是你不能使用返回 false 的方式阻止默认行为。你必须明确的使用 preventDefault。例如，传统的 HTML 中阻止链接默认打开一个新页面，你可以这样写：

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```

在 React，应该这样来写：

```jsx
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

在这里，e 是一个合成事件。React 根据 W3C spec 来定义这些合成事件，所以你不需要担心跨浏览器的兼容性问题。


当你使用 ES6 class 语法来定义一个组件的时候，事件处理器会成为类的一个方法。例如，下面的 Toggle 组件渲染一个让用户切换开关状态的按钮：

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

**你必须谨慎对待 JSX 回调函数中的 this，类的方法默认是不会绑定 this 的。如果你忘记绑定 this.handleClick 并把它传入 onClick, 当你调用这个函数的时候 this 的值会是 undefined。**

**这并不是 React 的特殊行为；它是函数如何在 JavaScript 中运行的一部分。通常情况下，如果你没有在方法后面添加 () ，例如 onClick={this.handleClick}，你应该为这个方法绑定 this.**

你可以在回调函数中使用 箭头函数：

```jsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

使用这个语法有个问题就是每次 LoggingButton 渲染的时候都会创建一个不同的回调函数。在大多数情况下，这没有问题。然而如果这个回调函数作为一个属性值传入低阶组件，这些组件可能会进行额外的重新渲染。我们通常建议在构造函数中绑定或使用属性初始化器语法来避免这类性能问题。


#### 向事件处理程序传递参数

常我们会为事件处理程序传递额外的参数。例如，若是 id 是一个内联 id，以下两种方式都可以向事件处理程序传递参数：

```jsx
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

上述两种方式是等价的，分别通过 arrow functions 和 Function.prototype.bind 来为特定事件类型添加事件处理程序。

上面两个例子中，参数 e 作为 React 事件对象将会被作为第二个参数进行传递。通过箭头函数的方式，事件对象必须显式的进行传递，但是通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递。

值得注意的是，通过 bind 方式向监听函数传参，在类组件中定义的监听函数，事件对象 e 要排在所传递参数的后面，例如:

```jsx
class Popper extends React.Component{
    constructor(){
        super();
        this.state = {name:'Hello world!'};
    }
    
    preventPop(name, e){    //事件对象e要放在最后
        e.preventDefault();
        alert(name);
    }
    
    render(){
        return (
            <div>
                <p>hello</p>
                {/* Pass params via bind() method. */}
                <a href="https://reactjs.org" onClick={this.preventPop.bind(this,this.state.name)}>Click</a>
            </div>
        );
    }
}
```





### 受控组件与非受控组件

#### 受控组件

```text
1、每当表单的状态发生变化时，都会被写入到组件的state中
2、在受控组件中，组件渲染出的状态与它的value或checked prop相对应
3、react受控组件更新state的流程

    流程：
    <1> 通过在初始state中设置表单的默认值
    <2> 每当表单的值发生变化时，调用onChange事件处理器
    <3> 事件处理器通过合成对象e拿到改变后的状态，并更新应用的state
    <4> SetState触发视图的重新渲染，完成表单组件值的更新
```

#### 非受控组件

```
1、如果一个表单组件没有value prop就可以称为非受控组件
2、非受控组件是一种反模式，它的值不受组件自身的state或props控制
3、通常需要为其添加ref来访问渲染后的底层DOM元素
4、可通过添加defaultValue指定value值
```



**受控组件需要主动维护一个内部state状态的，而非受控组件是无需维护组件的state状态的，二者有冲突。**

* 受控元素，一般用在需要动态设置其初始值的情况；例如某些form表单信息编辑时，input表单元素需要初始显示服务器返回的某个值然后进行编辑。
* 非受控元素， 一般用于无任何动态初始值信息的情况； 例如form表单创建信息时，input表单元素都没有初始值，需要用户输入的情况

**在大多数情况下，我们推荐使用 受控组件 来实现表单。**





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





### bind(this)

[网页链接](http://www.css88.com/react/docs/handling-events.html)


在React Component中为什么需要bind(this)?

**因为在class中声明函数，并不会自动绑定this对象(重点就是这里)**
在`onClick={this.handleEvent}`的时候，分解成两步：


```tsx
let handleEvent = this.handleEvent;
...onClick={handleEvent}...
```

所以，onClick调用的时候，handleEvent中的this会是undefined（根据文档）
所以，你需要bind一下, 那么里面的this就是当前组件啦。

```tsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // 这个绑定是必要的，使`this`在回调中起作用
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

在JSX回调中你必须注意 this 的指向。 在 JavaScript 中，类方法默认没有 绑定的。如果你忘记绑定 this.handleClick 并将其传递给onClick，那么在直接调用该函数时，this 会是 undefined 。

这不是 React 特有的行为；这是 JavaScript 中的函数如何工作的一部分。 一般情况下，如果你引用一个后面没跟 () 的方法，例如 onClick={this.handleClick} ，那你就应该 绑定(bind) 该方法。

如果调用 bind 令你烦恼，有两种方法可以解决这个问题。 如果您使用实验性的 属性初始化语法 ，那么你可以使用属性初始值设置来正确地 绑定(bind) 回调：

```tsx
class LoggingButton extends React.Component {
  // 这个语法确保 `this` 绑定在 handleClick 中。
  // 警告：这是 *实验性的* 语法。
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

这个语法在 创建 React App 中是默认开启的。

如果你没有使用属性初始化语法，可以在回调中使用一个 箭头函数：

```tsx
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // 这个语法确保 `this` 被绑定在 handleClick 中
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

这个语法的问题是，每次 LoggingButton 渲染时都创建一个不同的回调。在多数情况下，没什么问题。然而，如果这个回调被作为 prop(属性) 传递给下级组件，这些组件可能需要额外的重复渲染。**我们通常建议在构造函数中进行绑定，以避免这类性能问题。**



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





## React 16



### 新生命周期getDerivedStateFromProps





- `UNSAFE_componentWillReceiveProps(nextProps)` 在组件接收到新的参数时被触发.

当父组件导致子组件更新的时候, 即使接收的 props 并没有变化, 这个函数也会被调用.

> UNSAFE_componentWillReceiveProps()is invoked before a mounted component receives new props.
>  Note that if a parent component causes your component to re-render, this method will be called even if props have not changed.



-  `getDerivedStateFromProps(props, state)` 会在每次组件渲染前被调用

> getDerivedStateFromProps is invoked right before calling the render method, both on the initial mount and on subsequent updates.



getDerivedStateFromProps 会在每次组件被重新渲染前被调用, 这意味着无论是**父组件的更新, props 的变化, 或是组件内部执行了 setState(), 它都会被调用**.





### react16错误处理(componentDidCatch)

[网页链接](https://segmentfault.com/a/1190000011379425)

React 早就需要一个处理错误的解决方案。
在 React V15 中使用 handle_unstableError

从早期的 React 开发以来，一个小的组件抛出错误将会破坏整个应用程序，这种情况相当普遍。

React 16 将提供一个内置函数 componentDidCatch，如果 render() 函数抛出错误，则会触发该函数。在下面的示例中，你可以点击 “button will throw” 来抛出一个错误。

[https://codesandbox.io/s/lOo5AV12M](https://codesandbox.io/s/lOo5AV12M)



```tsx
export default class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            hasError: false
        }
    }


    componentDidCatch() {
        this.setState({
            hasError: true
        })
    }

    render() {
         return this.state.hasError?出错的情况:正常情况
    }

}    
```

>把componentDidCatch放在App.js中就可以处理全局错误了



**错误在渲染阶段中被捕获，但在事件处理程序中不会被捕获。**




下面是一个基本的 PotentialError 组件

```tsx
class PotentialError extends React.Component {   
  constructor(props) {     
    super(props);     
    this.state = { error: false };
  }
  componentDidCatch(error, info) {     
    this.setState({ error, info });
  }
  render() {
    if (this.state.error) {
      return <h1>Error: {this.state.error.toString()}</h1>;
    }
    return this.props.children;   
  } 
}
```

然后在顶部或任何地方，你可以这样使用它：

```
<PotentialError><AwesomeApp /></PotentialError>
```


另一个令人敬畏的特性 componentDidCatch 是包含错误堆栈的 info 对象！

```
{this.state.info && this.state.info.componentStack}
```

这将告诉你组件在哪里失效！
让我知道你正在使用错误边界！



错误边界介绍

[https://zhuanlan.zhihu.com/p/29706036](https://zhuanlan.zhihu.com/p/29706036)
在 UI 部分发生的 JavaScript 异常不应该阻断整个应用。为了解决这一问题，React 16 引入了“错误边界（error boundary）”这一新概念。

错误边界作为 React 组件，用以**捕获在子组件树中任何地方的 JavaScript 异常，打印这些错误，并展示备用 UI 而非让组件树崩溃**。错误边界会捕获渲染期间，在生命周期方法中以及在其整个树的构造函数中的异常。



**错误边界仅可以捕获组件树中的后代组件错误。**

一个错误边界无法捕获其自身的错误。若错误边界在渲染错误信息时失败，则该错误会传递至上一层最接近的错误边界。而这也类似与 JavaScript 中的 catch {} 块的工作方式。





### forceUpdate

[https://react.docschina.org/docs/hooks-faq.html#is-there-something-like-forceupdate](https://react.docschina.org/docs/hooks-faq.html#is-there-something-like-forceupdate)

如果前后两次的值相同，`useState` 和 `useReducer` Hook [都会放弃更新](https://react.docschina.org/docs/hooks-reference.html#bailing-out-of-a-state-update)。

通常，你不应该在 React 中修改本地 state。然而，作为一条出路，你可以用一个增长的计数器来在 state 没变的时候依然强制一次重新渲染：

```tsx
  const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

  function handleClick() {
    forceUpdate();
  }
```

可能的话尽量避免这种模式。



### 测量 DOM 节点

[https://react.docschina.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node](https://react.docschina.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)

获取 DOM 节点的位置或是大小的基本方式是使用 [callback ref](https://react.docschina.org/docs/refs-and-the-dom.html#callback-refs)。每当 ref 被附加到一个另一个节点，React 就会调用 callback

例子：

```tsx
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = useCallback(node => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  );
}
```

在这个案例中，我们没有选择使用 `useRef`，**因为当 ref 是一个对象时它并不会把当前 ref 的值的 *变化* 通知到我们**。使用 callback ref 可以确保 [即便子组件延迟显示被测量的节点](https://codesandbox.io/s/818zzk8m78) (比如为了响应一次点击)，我们依然能够在父组件接收到相关的信息，以便更新测量结果。



注意到我们传递了 `[]` 作为 `useCallback` 的依赖列表。这确保了 ref callback 不会在再次渲染时改变，因此 React 不会在非必要的时候调用它。



> 在此示例中，当且仅当组件挂载和卸载时，callback ref 才会被调用，因为渲染的 `<h1>` 组件在整个重新渲染期间始终存在。如果你希望在每次组件调整大小时都收到通知，则可能需要使用 [`ResizeObserver`](https://developer.mozilla.org/zh-CN/docs/Web/API/ResizeObserver) 或基于其构建的第三方 Hook。



### 实现类似class 中的 this

 [使用一个 ref](https://react.docschina.org/docs/hooks-faq.html#is-there-something-like-instance-variables) 来保存一个可变的变量

例子：

```tsx
function Example(props) {
  // 把最新的 props 保存在一个 ref 中
  const latestProps = useRef(props);
  useEffect(() => {
    latestProps.current = props;
  });

  useEffect(() => {
    function tick() {
      // 在任何时候读取最新的 props
      console.log(latestProps.current);
    }

    const id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []); // 这个 effect 从不会重新执行
}
```

**仅当你实在找不到更好办法的时候才这么做，因为依赖于变更会使得组件更难以预测**







### Refs

[https://zh-hans.reactjs.org/docs/refs-and-the-dom.html](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)

> `ref` 会在 `componentDidMount` 或 `componentDidUpdate` 生命周期钩子触发前更新。

下面是几个适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

避免使用 refs 来做任何可以通过声明式实现来完成的事情。



#### Refs 与函数组件

默认情况下，**你不能在函数组件上使用 `ref` 属性**，因为它们没有实例：

```tsx
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // This will *not* work!
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```

如果要在函数组件中使用 `ref`，你可以使用 [`forwardRef`](https://zh-hans.reactjs.org/docs/forwarding-refs.html)（可与 [`useImperativeHandle`](https://zh-hans.reactjs.org/docs/hooks-reference.html#useimperativehandle) 结合使用），或者可以将该组件转化为 class 组件。

不管怎样，你可以**在函数组件内部使用 `ref` 属性**，只要它指向一个 DOM 元素或 class 组件：

```tsx
function CustomTextInput(props) {
  // 这里必须声明 textInput，这样 ref 才可以引用它
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```

#### DOM Refs 暴露给父组件

如果你使用 16.3 或更高版本的 React, 这种情况下我们推荐使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。**Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref**。关于怎样对父组件暴露子组件的 DOM 节点，在 [ref 转发文档](https://zh-hans.reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)中有一个详细的例子。



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









### render prop

```tsx
class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          使用 `render`prop 动态决定要渲染的内容，
          而不是给出一个 <Mouse> 渲染结果的静态表示
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}
```

使用

```tsx
class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```

更具体地说，**render prop 是一个用于告知组件需要渲染什么内容的函数 prop。**









### Hooks



#### useEffect



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

[useMemo 官方文档](https://zh-hans.reactjs.org/docs/hooks-reference.html#usememo)

[useMemo和useEffect有什么区别](https://blog.csdn.net/hsany330/article/details/106122228)

[React Hooks: 深入剖析 useMemo 和 useEffect](https://zhuanlan.zhihu.com/p/268802571)

[useEffect的陷阱](https://zhuanlan.zhihu.com/p/84697185)





* **当你调用 useEffect 时，就是在告诉 React 在完成对 DOM 的更改后运行你的“副作用”函数**。**effect只能在DOM更新后再触发**.
* useMemo 的函数会在渲染期间执行，所以使用`useMemo`就能解决怎么在`DOM`改变的时候，控制某些函数不被触发。



> 不能在useMemo中操作DOM之类的副作用操作，不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 useEffect 的适用范畴，而不是 useMemo



> 在`useMemo`中使用`setState`你会发现会产生死循环，并且会有警告，因为`useMemo`是在渲染中进行的，你在其中操作`DOM`后，又会导致触发`memo`



* **memo是在DOM更新前触发的，就像官方所说的，类比生命周期就是shouldComponentUpdate**
* useEffect 可以看成 componentDidMount / componentDidUpdate / componentWillUnmount 这 3 个生命周期函数的替代。



> useEffect 是官方推荐拿来代替 componentDidMount / componentDidUpdate / componentWillUnmount 这 3 个生命周期函数的，但其实他们并不是完全等价，useEffect 是在浏览器渲染结束之后才执行的，而这三个生命周期函数是在浏览器渲染之前同步执行的，React 还有一个官方的 hook 是完全等价于这三个生命周期函数的，叫 useLayoutEffect。



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





#### useHistory

> 从React Router v5.1.0开始，新增了useHistory钩子（hook），如果是使用React >16.8.0，使用useHistory即可实现编程时页面跳转导航。

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





#### usePrevious 

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












[1]: http://www.ruanyifeng.com/blogimg/asset/2015/bg2015033110.png
[2]: https://pic1.zhimg.com/80/v2-a32cb02859ea4ac0f8f50f1ec885d85c_hd.jpg

