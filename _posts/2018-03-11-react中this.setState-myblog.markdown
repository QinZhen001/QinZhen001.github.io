---
layout:     post
title:      "react中this.setState"
date:       2018-03-11 16:42:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


## setState这个API的设计
[setState这个API的设计](https://zhuanlan.zhihu.com/p/25954470)


### this.setState后获取最新数据

this.setState是异步，所以在this.setState之后立即调用this.state是获取不到最新的数据的，那么怎么获取最新的数据呢？下面介绍三个方法：

1.回调函数callback
```
this.setState({
  val: this.state.val+1
}, () => {
  console.log(this.state.val)
});
```
2.componentDidUpdate
```
componentDidUpdate(){
    console.log(this.state.val);
}
```
在this.setState之后去componentDidUpdate函数中调用，此时的this.state已经更新

3.将this.setState放入setTimeout函数中
```
let self = this;
setTimeout(function () {
  self.setState({
    val:self.state.val+1
  });
  console.log(self.state.val);
})
```
在setTimeout函数中，在this.setState之后this.state是立即更新的，所以也可以获取到更新后的数据。


### setState这个API的设计

React抽象来说，就是一个公式

`UI=f(state)`
我们把最终绘制出来的UI当做一个函数f运行的结果，f就是React和我们基于React写得代码，而f的输入参数就是state。

作为React管理state的一个重要方法，setState肯定非常重要，如果只是简单用法，也不会有任何问题，但是如果用得深，就会发现很……尴尬。

我刚开始接触React的时候，就意识到React相当于一个jQuery的替代品，但是就像单独依靠jQuery难以管理大型项目，所以也需要给配合使用的MVC框架找一个替代品，我选择的替代品是Redux，我很早就将React和Redux配合使用；现在，回过头来看看React的setState，发现坑真的不少，不禁感叹自己还是挺走运的。

对setState用得深了，就容易犯错，所以我们开门见山先把理解setState的关键点列出来。

* setState不会立刻改变React组件中state的值；
* setState通过引发一次组件的更新过程来引发重新绘制；
* 多次setState函数调用产生的效果会合并。


#### setState不会立刻改变React组件中state的值
在React中，一个组件中要读取当前状态用是访问this.state，但是更新状态却是用this.setState，不是直接在this.state上修改，为什么呢？
```
//读取状态
const count = this.state.count；

//更新状态
this.setState({count: count + 1}）；

//无意义
this.state.count = count + 1;
```


因为this.state说到底只是一个对象，单纯去修改一个对象的值是没有意义的，去驱动UI的更新才是有意义的，想想看，如果只是改了this.state这个对象，但是没有让React组件重新绘制一遍，那有什么用？你可以尝试在代码中直接修改this.state的值，会发现的确能够改变状态，但是却不会引发重新渲染。

所以，需要用一个函数去更改状态，这个函数就是setState，当setState被调用时，能驱动组件的更新过程，引发componentDidUpdate、render等一系列函数的调用。

当然，如果使用Object的setter功能，实际上也可以通过对this.state对象的直接修改来实现setState一样的功能，但是，如果React真的这么设计的话，我敢肯定，那样的API设计会更让人晕头转向，因为不管是谁，第一眼也看不出来修改一个this.state对象居然会引发重新渲染的副作用。

这么看来，React提供setState这个API是一个挺合理的决定。

因为setState并不会立刻修改this.state的值，所以下面的code可能产生很不直观的结果。
```
function incrementMultiple() {
  this.setState({count: this.state.count + 1});
  this.setState({count: this.state.count + 1});
  this.setState({count: this.state.count + 1});
}
```
直观上来看，当上面的incrementMultiple函数被调用时，组件状态的count值被增加了3次，每次增加1，那最后count被增加了3，但是，实际上的结果只给state增加了1。

原因并不复杂，就是因为调用this.setState时，并没有立即更改this.state，所以this.setState只是在反复设置同一个值而已，上面的code等同下面这样。
```
function incrementMultiple() {
  const currentCount = this.state.count;
  this.setState({count: currentCount + 1});
  this.setState({count: currentCount + 1});
  this.setState({count: currentCount + 1});
}
```
currentCount就是一个快照结果，重复地给count设置同一个值，不要说重复3次，哪怕重复一万次，得到的结果也只是增加1而已。

既然this.setState不会立即修改this.state的值，那在什么时候修改this.state的值呢？这就要说一下React的更新生命周期。

### setState通过引发一次组件的更新过程来引发重新绘制
setState调用引起的React的更新生命周期函数4个函数（比修改prop引发的生命周期少一个componentWillReceiveProps函数），这4个函数依次被调用。

* shouldComponentUpdate
* componentWillUpdate
* render
* componentDidUpdate

**当shouldComponentUpdate函数被调用的时候，this.state没有得到更新。
当componentWillUpdate函数被调用的时候，this.state依然没有得到更新。**


**直到render函数被调用的时候，this.state才得到更新。**

>或者，当shouldComponentUpdate函数返回false，这时候更新过程就被中断了，render函数也不会被调用了，这时候React不会放弃掉对this.state的更新的，所以虽然不调用render，依然会更新this.state。


那就可以简单认为，直到下一次render函数调用时（或者下一次shouldComponentUpdate返回false时）才得到更新的this.state。

### 多次setState函数调用产生的效果会合并
比如下面的代码。
```
function updateName() {
  this.setState({FirstName: 'Morgan'});
  this.setState({LastName: 'Cheng'});
}
```
连续调用了两次this.setState，但是只会引发一次更新生命周期，不是两次，因为React会将多个this.setState产生的修改放在一个队列里，缓一缓，攒在一起，觉得差不多了再引发一次更新过程。

在每次更新过程中，会把积攒的setState结果合并，做一个merge的动作，所以上面的代码相当于这样。
```
function updateName() {
  this.setState({FirstName: 'Morgan', LastName: 'Cheng'});
}
```

如果每一个this.setState都引发一个更新过程的话，那就太浪费了！
对于开发者而言，也可以放心多次调用this.setState，每一次只要关注当前修改的那一个字段就行，反正其他字段会合并保留，丢不掉。

**总结一下，setState最招骂的就是不会立即修改this.state。**


不过，最近一个this.setState函数的隐藏功能进入了大家的视野，那就是：**原来this.setState可以接受一个函数作为参数啊！**


### 函数式的setState用法
如果传递给this.setState的参数不是一个对象而是一个函数，那游戏规则就变了。

这个函数会接收到两个参数，第一个是当前的state值，第二个是当前的props，这个函数应该**返回一个对象(注意它的ES6写法)**，这个对象代表想要对this.state的更改，换句话说，之前你想给this.setState传递什么对象参数，在这种函数里就返回什么对象，不过，计算这个对象的方法有些改变，不再依赖于this.state，而是依赖于输入参数state。

比如，对于上面增加state上count的例子，可以这么写一个函数。
```
function increment(state, props) {
  return {count: state.count + 1};
}
```

可以看到，同样是把状态中的count加1，但是状态的来源不是this.state，而是输入参数state。

对应incrementMultiple的函数就是这么写。
```
function incrementMultiple() {
  this.setState(increment);
  this.setState(increment);
  this.setState(increment);
}
```

对于多次调用函数式setState的情况，React会保证调用每次increment时，state都已经合并了之前的状态修改结果。

简单说，加入当前this.state.count的值是0，第一次调用this.setState(increment)，传给increment的state参数是0，第二调用时，state参数是1，第三次调用是，参数是2，最终incrementMultiple的效果，真的就是让this.state.count变成了3，这个函数incrementMultiple终于实至名归。

**值得一提的是，在increment函数被调用时，this.state并没有被改变，依然，要等到render函数被重新执行时（或者shouldComponentUpdate函数返回false之后）才被改变。**

>让setState接受一个函数的API设计很棒！因为这符合函数式编程的思想，让开发者写出没有副作用的函数，我们的increment函数并不去修改组件状态，只是把“希望的状态改变”返回给React，维护状态这些苦力活完全交给React去做。

让我们再往前推进一步，试着如果把两种setState的用法混用，那会有什么效果？

我们把incrementMultiple改成这样。
```
function incrementMultiple() {
  this.setState(increment);
  this.setState(increment);
  this.setState({count: this.state.count + 1});
  this.setState(increment);
}
```
在几个函数式setState调用中插入一个传统式setState调用（嗯，我们姑且这么称呼以前的setState使用方式），最后得到的结果是让this.state.count增加了2，而不是增加4。

原因也很简单，因为React会依次合并所有setState产生的效果，虽然前两个函数式setState调用产生的效果是count加2，但是半路杀出一个传统式setState调用，一下子强行把积攒的效果清空，用count加1取代。

**注意:传统式setState的存在，会把函数式setState拖下水**


举些函数式setState栗子
```
this.setState(preState => ({
  books: preState.books.concat(['React Guide']);
}))
```


```
this.setState(preState => ({
  books: [...preState.books, 'React Guide'];
}))

```


```
var books = this.state.books; 
this.setState({
  books: books.slice(1,3);
})
```


```
this.setState(preState => ({
  books: preState.books.slice(1,3);
}))
```

**注意：({ }) ES6写法，返回的要是一个对象**



**注意不要使用push、pop、shift、unshift、splice等方法修改数组类型的状态，因为这些方法都是在原数组的基础上修改，而concat、slice、filter会返回一个新的数组。**


## setState为什么不会同步更新组件状态
假设，我们现在有机会来对React做一个重大设计调整，把setState的功能设定为同步更改this.state，也就是说，当setState函数返回的时候，this.state已经体现了状态的改变。

那就有两个设计的问题就直接摆在我们面前。
1. setState更新状态之后要不要触发一次更新过程？
2. 如何去触发更新过程？

对这两个问题，我们有三个选择答案。
1. setState自动触发**同步**的组件更新过程；
2. setState自动触发**异步**的组件更新过程；
3. 干脆，setState根本**不触发**组件更新过程，让开发者显示驱动更新过程。

我们逐个看看各种选择，看他们这些选择是不是行得通，有什么优劣。

### 第一个选择：setState自动触发同步的组件更新过程
如果这样，也就是setState调用返回时，一个完整更新过程已经走完了，这样的设计，应该是不行的，因为每一次setState都会引发一次组件更新太浪费了啊！

setState引发的组件更新过程，包含生命周期函数有四个。
* shouldComponentUpdate
* componentWillUpdate
* render
* componentDidUpdate

每一次setState调用都走一圈生命周期，光是想一想也会觉得会带来性能的问题，其实这四个函数都是纯函数，性能应该还好，但是render函数返回的结果会拿去做Virtual DOM比较和更新DOM树，这个就比较费时间。

目前React会将setState的效果放在队列中，积攒着一次引发更新过程，为的就是把Virtual DOM和DOM树操作降到最小，用于提高性能。

好吧，我们退一步，就当我们对React和当今CPU和浏览器的性能充满信心，不在乎这点性能损耗，但是，这样的设计还是问题。

一个shouldComponentUpdate函数的参数是这样。
```
shouldComponentUpdate(nextProps, nextState) {
  //在这个函数被调用时，this.state还没有被改变
}
```


目前的React设计，shouldComponentUpdate被调用时，this.state并没有被改变，setState产生的状态改变是通过参数nextState来体现的，componentWillUpdate也是一样。

所以，为了让基于现有React体系的很多代码实现不要完蛋，我们肯定不能在shouldComponentUpdate之前修改this.state，修改this.state的时机肯定只能是在调用componentWillUpdate和render函数之间。

也就是说，即使setState号称“同步更新”this.state，实际上还是不能立即更新，因为setState引发的生命周期函数shouldComponentUpdate和componentWillUpdate里，this.state还没有改变。

听起来，解决得并不是很彻底，似乎把事情搞得更复杂了。

### 第二个选择：setState自动触发异步的组件更新过程
这种选择下，setState返回时，this.state已经被改变了，但是并没有立即引发更新过程，React依然将setState产生的结果放在队列里，等到时机合适时走更新过程。

这样肯定不行啊，如果setState把this.state改了，那shouldComponentUpdate和componentWillUpdate咋办？这两个函数一直就假设执行时this.state并没有被改变啊。

这种选择无疑是行不通的。

### 第三个选择：setState根本不触发组件更新过程
前两个选择都不怎样，那就看这第三个选择，setState只修改this.state，并不出发组件更新过程，那我们就需要另外一个函数用来主动触发更新状态，可是……如果真的这样的话，还需要setState干吗？

**如果用这种方法来写code，那么React也就不够React了，算不上Reactive。**


## getInitialState 方法用于定义初始状态
```
ar LikeButton = React.createClass({
  getInitialState: function() {
    return {liked: false};
  },
  handleClick: function(event) {
    this.setState({liked: !this.state.liked});
  },
  render: function() {
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick={this.handleClick}>
        You {text} this. Click to toggle.
      </p>
    );
  }
});

ReactDOM.render(
  <LikeButton />,
  document.getElementById('example')
);
```

上面代码是一个 LikeButton 组件，它的 getInitialState 方法用于定义初始状态，也就是一个对象，这个对象可以通过 this.state 属性读取。当用户点击组件，导致状态变化，this.setState 方法就修改状态值，每次修改以后，自动调用 this.render 方法，再次渲染组件。

由于 this.props 和 this.state 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，this.props 表示那些一旦定义，就不再改变的特性，而 this.state 是会随着用户互动而产生变化的特性。

## 如何定义State
定义一个合适的State，是正确创建组件的第一步。State必须能代表一个组件UI呈现的完整状态集，即组件的任何UI改变，都可以从State的变化中反映出来；同时，State还必须是代表一个组件UI呈现的最小状态集，即State中的所有状态都是用于反映组件UI的变化，没有任何多余的状态，也不需要通过其他状态计算而来的中间状态。

组件中用到的一个变量是不是应该作为组件State，可以通过下面的4条依据进行判断：


1. 这个变量是否是通过Props从父组件中获取？如果是，那么它不是一个状态。
2. 这个变量是否在组件的整个生命周期中都保持不变？如果是，那么它不是一个状态。
3. 这个变量是否可以通过其他状态（State）或者属性(Props)计算得到？如果是，那么它不是一个状态。
4. 这个变量是否在组件的render方法中使用？如果不是，那么它不是一个状态。这种情况下，这个变量更适合定义为组件的一个普通属性，例如组件中用到的定时器，就应该直接定义为this.timer，而不是this.state.timer。


**请务必牢记，并不是组件中用到的所有变量都是组件的状态！当存在多个组件共同依赖一个状态时，一般的做法是状态上移，将这个状态放到这几个组件的公共父组件中。**

## State 与 Props 区别
除了State, 组件的Props也是和组件的UI有关的。他们之间的主要区别是：State是可变的，是组件内部维护的一组用于反映组件UI变化的状态集合；而Props对于使用它的组件来说，是只读的，要想修改Props，只能通过该组件的父组件修改。在组件状态上移的场景中，父组件正是通过子组件的Props, 传递给子组件其所需要的状态。




作者：艾特老干部
链接：https://www.jianshu.com/p/c6257cbef1b1
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
























