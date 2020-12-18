---
layout:     post
title:      "受控组件和非受控组件"
date:       2018-01-19 11:20:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


## 正文

[https://www.cnblogs.com/wonyun/p/6023363.html](https://www.cnblogs.com/wonyun/p/6023363.html)

[https://doc.react-china.org/docs/uncontrolled-components.html](https://doc.react-china.org/docs/uncontrolled-components.html)


就形式上来说
* 受控组件就是为某个form表单组件添加value属性；
* 非受控组件就是没有添加value属性的组件；

### 受控组件

受控组件的形式如下形式：
```
render: function() {
    return <input type="text" value="Hello!" />;
  }
```

添加了value 属性的表单组件元素其内部是不会维护自己状态state，组件的value值一旦设置某个具体值就始终是这个值，所以需要调用者来控制组件value的改变。


这种写法带来一个问题：**渲染后的input组件的用户交互，用户输入的任何值将不起作用，input输入框中的值始终为Hello!**。这与HTML中input表现不一致。

为此，为了控制该组件，就需要能能够控制input组件的值，需要借助其内部的状态state，即组件内部要维护一个状态state以便配合input组件的onChange和setState方法来完成对组件的控制；例如对上面形式可以进行封装一个inputItem组件，其内部维护一个state状态，具体代码如下：
```
export default class InputItem extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            value: ""
        }
    }

    componentWillReceiveProps(nextProps){
        this.setState({
            value: nextProps.value
        })
    }

    _onChange(evt){
        this.setState({
            value: evt.target.value
        })
    }

    render(){
        return (
            <input type="text" 
                value={this.state.value} 
                onChange={this._onChange.bind(this)}/>
        );
    }
}
```

### 非受控组件

表现形式上，react中没有添加value属性的表单组件元素就是非受控组件。表现形式如下：

`<input type="text" />`

非受控组件在底层实现时是在其内部维护了自己的状态state；这样表现出用户输入任何值都能反应到元素上。

要编写一个非受控组件，而非为每个状态更新编写事件处理程序，你可以 使用 ref 从 DOM 获取表单值。

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={(input) => this.input = input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

在 React 的生命周期中，表单元素上的 value 属性将会覆盖 DOM 中的值。使用非受控组件时，通常你希望 React 可以为其指定初始值，但不再控制后续更新。要解决这个问题，你可以指定一个 defaultValue 属性而不是 value。

```
render() {
  return (
    <form onSubmit={this.handleSubmit}>
      <label>
        Name:
        <input
          defaultValue="Bob"
          type="text"
          ref={(input) => this.input = input} />
      </label>
      <input type="submit" value="Submit" />
    </form>
  );
}
```



## 总结
**受控组件需要主动维护一个内部state状态的，而非受控组件是无需维护组件的state状态的，二者有冲突。**

* 受控元素，一般用在需要动态设置其初始值的情况；例如某些form表单信息编辑时，input表单元素需要初始显示服务器返回的某个值然后进行编辑。
* 非受控元素， 一般用于无任何动态初始值信息的情况； 例如form表单创建信息时，input表单元素都没有初始值，需要用户输入的情况



>**在大多数情况下，我们推荐使用 受控组件 来实现表单。**
