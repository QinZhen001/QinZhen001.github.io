---
layout:     post
title:      "this到底指向哪里"
date:       2018-04-11 14:34:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”


## 正文
[网页链接](https://juejin.im/post/5acb1d326fb9a028d2083ce2?utm_source=gold_browser_extension)

### this到底是谁
this既不是自身也不是当前函数的作用域。我们可以通过代码来测试。

判断是不是自身
```
function fn(){
    console.log(this.name)
}
fn.name = 'xxx'
fn()  //undefined
```
判断是不是作用域
```
function foo() { 
    var a = 2;
    this.bar(); 
}
function bar() { 
    console.log( this.a );
}
foo(); // ReferenceError: a is not defined
```




**那么this到底是谁呢？其实不一定,this是运行时绑定的，所以取决于函数的执行上下文。**



>当一个函数被调用时，会创建一个活动记录(执行上下文)。这个记录会包含函数在哪里被调用(调用栈)、函数的调用方法、传入的参数等信息，this也是这里的一个属性。


### 如何判断this指向
确定this指向就是确定函数的执行上下文，也就是“谁调用的它”，有以下几种判断方式：


#### 独立函数调用
```
function foo(){
    console.log(this.a)
}
var a = 2
foo()  // 2
```

**这种直接调用的方式this指向全局对象，如果是在浏览器就指向window**

#### 对象上下文(隐式绑定)
```
function foo() { 
    console.log( this.a );
}
var obj = { 
    a: 2,
    foo: foo
};
obj.foo(); // 2
```

foo虽然被定义在全局作用域，但是调用的时候是通过obj上下文引用的，可以理解为在**foo调用的那一刻它被obj对象拥有**。所以this指向obj。



这里有两个问题：

**链式调用** 
链式调用的情况下只有最后一层才会影响调用位置，例如：
`obj1.obj2.obj3.fn() //这里的fn中的this指向obj3`



**引式丢失**
```
function foo() { 
    console.log( this.a );
}
var obj = { 
    a: 2,
    foo: foo 
};
var bar = obj.foo; // 函数别名!
var a = "xxxxx"
bar(); // xxxxx
```

这里的bar其实是引用了obj.foo的地址，这个地址指向的是一个函数，也就是说bar的调用其实符合“独立函数调用”规则。所以它的this不是obj。


>回调函数其实就是隐式丢失

  稍微改一下上面的代码：
```
function foo() { 
    console.log( this.a );
}
var obj = { 
    a: 2,
    foo: foo 
};
var a = "xxxxx"
setTimeout( obj.foo ,100); // xxxxx
```
我们看到，回调函数虽然是通过obj引用的，但是this也不是obj了。其实内置的setTimeout()函数实现和下面的伪代码类似：
```
function setTimeout(fn, delay){
    //等待delay毫秒
    fn()
}
```
其实这段代码隐藏这一个操作就是fn=obj.foo，这和上面例子中的bar=obj.foo异曲同工。



**显式绑定**
显式绑定的说法是和隐式绑定相对的，指的是通过call、apply、bind显式地更改this指向。

这三个方法第一个参数是this要指向的对象。

注意，如果你给第一个参数传递一个值(字符串、布尔、数字)类型的话，这个值会被转换成对象形式(调用new String(..)、new Boolean(..)、new Number(..))。

这三个方法中的bind方法比较特殊，它可以延迟方法的执行，这可以让我们写出更加灵活的代码。它的原理也很容易模拟：
```
function foo(something) { 
    console.log( this.a, something ); 
    return this.a + something;
}
function bind(fn, obj) {
    return function() {
        return fn.apply( obj, arguments );
    }; 
}
var obj = { 
    a:2
};
var bar = bind( foo, obj );
var b = bar( 3 ); // 2 3 
console.log( b ); // 5
```

>注意：如果第一个参数传入null或者undefined，这个值会被忽略，相当于符合独立函数调用规则



**new绑定**
Js中new与传统的面向类的语言机制不同，Js中的“构造函数”其实和普通函数没有任何区别。


其实当我们使用new来调用函数的时候，发生了下列事情：


* 创建一个全新的对象
* 这个新对象会被执行“原型”链接
* 这个新对象会被绑定到调用的this
* 如果函数没有对象类型的返回值，这个对象会被返回



其中，第三步绑定了this,所以“构造函数”和原型中的this永远指向new出来的实例。



### 优先级
以上四条判断规则的权重是递增的。判断的顺序为：


* 函数是new出来的，this指向实例
* 函数通过call、apply、bind绑定过，this指向绑定的第一个参数
* 函数在某个上下文对象中调用（隐式绑定），this指向上下文对象
* 以上都不是，this指向全局对象



### 严格模式下的差异
以上所说的都是在非严格模式下成立，严格模式下的this指向是有差异的。

* 独立函数调用： this 指向undefined
* 对象上的方法： this 永远指向该对象
* 其他没有区别

### 箭头函数中的this
箭头函数不是通过function关键字定义的，也不使用上面的this规则，而是“继承”外层作用域中的this指向。



### getter与setter中的this
es6中的getter或setter函数都会把this绑定到设置或获取属性的对象上。
```
function sum() {
  return this.a + this.b + this.c;
}
var o = {
  a: 1,
  b: 2,
  c: 3,
  get average() {
    return (this.a + this.b + this.c) / 3;
  }
};
Object.defineProperty(o, 'sum', { get: sum, enumerable: true, configurable: true} );
console.log(o.average, o.sum); // logs 2, 6
```





作者：寒东设计师
链接：https://juejin.im/post/5acb1d326fb9a028d2083ce2
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。







### bind(this)



```js
var myObj = {
    specialFunction: function () {

    },
    anotherSpecialFunction: function () {

    }
    getAsyncData: function (cb) {
        cb();
    },
    render: function () {
        var that = this;
        this.getAsyncData(function () {
            that.specialFunction();
            that.anotherSpecialFunction();
        });
    }
};

myObj.render();
```

If we had left our function calls as `this.specialFunction()`, then we would have received the following error:

```javascript
Uncaught TypeError: Object [object global] has no method 'specialFunction'
```

We need to keep the context of the `myObj` object referenced for when the callback function is called. Calling `that.specialFunction()` enables us to maintain that context and correctly execute our function. However, this could be neatened somewhat by using `Function.prototype.bind()`.



Let’s rewrite our example:

```js
render: function () {
    this.getAsyncData(function () {
        this.specialFunction();
        this.anotherSpecialFunction();
    }.bind(this));
}
```

