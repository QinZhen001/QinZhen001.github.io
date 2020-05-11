---
layout:     post
title:      "React中PureComponent"
date:       2018-01-31 21:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


## 正文
[网页链接](https://www.jianshu.com/p/33cda0dc316a)


### 默认渲染行为的问题

在React Component的生命周期中，有一个shouldComponentUpdate方法。这个方法默认返回值是true。

这意味着就算没有改变组件的props或者state，也会导致组件的重绘。这就经常导致组件因为不相关数据的改变导致重绘，这极大的降低了React的渲染效率。比如下面的例子中，任何options的变化，甚至是其他数据的变化都可能导致所有cell的重绘。
```
//Table Component
{this.props.items.map(i =>
    <Cell data={i} option={this.props.options[i]} />
)}
```

### 重写shouldComponentUpdate

为了避免这个问题，我们可以在Cell中重写shouldComponentUpdate方法，只在option发生改变时进行重绘。

```
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

### 使用PureComponent与immutable.js

因为上面的情况十分通用，React创建了PureComponent组件创建了默认的shouldComponentUpdate行为。这个默认的shouldComponentUpdate行为会一一比较props和state中所有的属性，只有当其中任意一项发生改变是，才会进行重绘。

**需要注意的是，PureComponent使用浅比较判断组件是否需要重绘**

因此，下面对数据的修改并不会导致重绘（假设Table也是PureComponent)
```
options.push(new Option())
options.splice(0, 1)
options[i].name = "Hello"
```

这些例子都是在原对象上进行修改，由于浅比较是比较指针的异同，所以会认为不需要进行重绘。


为了避免出现这些问题，推荐使用immutable.js。

**immutable.js会在每次对原对象进行添加，删除，修改时返回新的对象实例。任何对数据的修改都会导致数据指针的变化。**


### 陷阱

#### Literal Array与Literal Object
```
{this.props.items.map(i =>
    <Cell data={i} options={this.props.options || []} />
)}
```
若options为空，则会使用[]。[]每次会生成新的Array，因此导致Cell每次的props都不一样，导致需要重绘。解决方法如下:
```
const default = [];
{this.props.items.map(i =>
  <Cell data={i} options={this.props.options || default} />
)}
```

#### 内联函数
函数也经常作为props传递，由于每次需要为内联函数创建一个新的实例，所以每次function都会指向不同的内存地址。比如：
```
render() {
     <MyInput onChange={e => this.props.update(e.target.value)} />;
}
```

以及：
```
update(e) {
     this.props.update(e.target.value);
}
render() {
     return <MyInput onChange={this.update.bind(this)} />;
}
```

注意第二个例子也会导致创建新的函数实例。

**为了解决这个问题，需要提前绑定this指针：**
```
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









作者：橙子_80c3
链接：https://www.jianshu.com/p/33cda0dc316a
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。