---
layout:     post
title:      "面试之js和ts"
date:       2019-08-02 11:22:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 面试
---

> “Yeah It's on. ”



[2 年前端 7～9 月面试经历总结](https://mp.weixin.qq.com/s/AR7MfTwYCK6V4YuN6xnSsA)



# ES5相关



## javaScript中有基本数据类型

* undefined
* null
* boolean
* number
* string
* object



## 从Chrome源码看JS Object的实现 （选看）

[https://zhuanlan.zhihu.com/p/26169639](https://zhuanlan.zhihu.com/p/26169639)





## 执行上下文和执行栈


[https://github.com/yygmind/blog/issues/12](https://github.com/yygmind/blog/issues/12)


执行上下文是当前 JavaScript 代码被解析和执行时所在环境的抽象概念。



执行上下文的类型


执行上下文总共有三种类型


* 全局执行上下文：只有一个，浏览器中的全局对象就是 window 对象，this 指向这个全局对象。
* 函数执行上下文：存在无数个，只有在函数被调用的时候才会被创建，每次调用函数都会创建一个新的执行上下文。
* Eval 函数执行上下文： 指的是运行在 eval 函数中的代码，很少用而且不建议使用。



----

### 执行栈


执行栈，也叫调用栈，具有 LIFO（后进先出）结构，用于存储在代码执行期间创建的所有执行上下文。



首次运行JS代码时，会创建一个**全局执行上下文**并Push到当前的执行栈中。每当发生函数调用，引擎都会为该函数创建一个**新的函数执行上下文**并Push到当前执行栈的栈顶。



根据执行栈LIFO规则，当栈顶函数运行完成后，其对应的函数执行上下文将会从执行栈中Pop出，上下文控制权将移到当前执行栈的下一个执行上下文。

--------



执行上下文的创建
执行上下文分两个阶段创建：1）创建阶段； 2）执行阶段



创建阶段


1. 确定 this 的值，也被称为 This Binding。
2. LexicalEnvironment（词法环境） 组件被创建。
3. VariableEnvironment（变量环境） 组件被创建。


直接看伪代码可能更加直观

```javascript
ExecutionContext = {  
  ThisBinding = <this value>,     // 确定this 
  LexicalEnvironment = { ... },   // 词法环境
  VariableEnvironment = { ... },  // 变量环境
}
```


This Binding



### VO和AO

https://blog.csdn.net/Ancecis/article/details/104382441

**在函数上下文中，用活动对象(activation object, AO)来表示变量对象。**


活动对象和变量对象的区别在于

1. 变量对象（VO）是规范上或者是JS引擎上实现的，并不能在JS环境中直接访问。 （全局上下文的变量对象VO）
2. 当进入到一个执行上下文后，这个变量对象才会被激活，所以叫函数活动对象（AO），这时候活动对象上的各种属性才能被访问。



**AO可以理解为VO的一个实例，也就是VO是一个构造函数，然后VO(Context) === AO**，所以VO提供的是一个函数中所有变量数据的模板。



VO和AO其实是一个东西，只是处于不同的执行上下文生命周期。AO存在于执行上下文位于执行上下文堆栈顶部(就是上边说的’当控制进入函数代码的执行上下文时’)的时期。再粗暴点，就是**函数调用时，VO被激活成了AO**。



![](https://s1.ax1x.com/2020/11/04/Bc4V4x.png)

> - 初始化全局vo和全局ao，由于全局对象只有一个，所以vo和ao一致。



举个例子：

```js
var data = []
for(var i = 0; i < 3; i++) {
    data[i] = (function(i) {
      return function() {
        console.log(i)
      }
  }(i))
}
data[0]()
data[1]()
data[2]()
```

`data[0]()` 的作用域链:

```
data[0]Context = {
  Scope: [AO, 匿名函数Context.AO, globalContext.VO]
}
```

作用域向上查找都是查找上面作用域的AO

**作用域链搜索到了最后总是会到globalContext.VO（全局环境的变量对象）**



----



为什么`scoped`指向的是AO而不是VO呢？

VO只是一个模板，由AO实例化。

```js
function A() {
  let count = 0;
  return function() {
    console.log(count++);
  }
}
```

**上面个的例子如果把A函数执行两遍，里面的count不会共享，也就是AO对象不同，所以AO就是VO的实例。**





## 了解v8引擎一段js代码如何执行的

在执行一段代码时，JS 引擎会首先创建一个执行栈



然后JS引擎会创建一个全局执行上下文，并push到执行栈中, 这个过程JS引擎会为这段代码中所有变量分配内存并赋一个初始值（undefined），在创建完成后，JS引擎会进入执行阶段，这个过程JS引擎会逐行的执行代码，即为之前分配好内存的变量逐个赋值(真实值)。



如果这段代码中存在function的声明和调用，那么JS引擎会创建一个函数执行上下文，并push到执行栈中，其创建和执行过程跟全局执行上下文一样。但有特殊情况，即当函数中存在对其它函数的调用时，JS引擎会在父函数执行的过程中，将子函数的全局执行上下文push到执行栈，这也是为什么子函数能够访问到父函数内所声明的变量。



还有一种特殊情况是，在子函数执行的过程中，父函数已经return了，这种情况下，JS引擎会将父函数的上下文从执行栈中移除，与此同时，JS引擎会为还在执行的子函数上下文创建一个闭包，这个闭包里保存了父函数内声明的变量及其赋值，子函数仍然能够在其上下文中访问并使用这边变量/常量。当子函数执行完毕，JS引擎才会将子函数的上下文及闭包一并从执行栈中移除。





## requestAnimationFrame 和 requestIdleCallback

[https://www.jianshu.com/p/2771cb695c81](https://www.jianshu.com/p/2771cb695c81)

* requestAnimationFrame 每一帧必定会执行不同  （一般用于执行动画）
* requestIdleCallback 是捡浏览器空闲来执行任务。



**`requestAnimationFrame` 的时间间隔都会紧跟屏幕刷新一次所需要的时间**；例如某一设备的刷新率是 75 Hz，那这时的时间间隔就是 13.3 ms（1 秒 / 75 次）。需要注意的是这个方法虽然能够**保证回调函数在每一帧内只渲染一次**，但是**如果这一帧有太多任务执行，还是会造成卡顿的；因此它只能保证重新渲染的时间间隔最短是屏幕的刷新时间。**



**`setTimeout` 或 `setInterval` 是使用定时器来触发回调函数的，而定时器并无法保证能够准确无误的执行，有许多因素会影响它的运行时机，比如说：当有同步代码执行时，会先等同步代码执行完毕，异步队列中没有其他任务，才会轮到自己执行**。



举个例子：

```js
		const frame = ()=>{
		    count++
		    count % 10 == 0 && stars.blink()
		    moon.draw()
		    stars.draw()

		    meteors.forEach((meteor, index, arr)=> {
		        //如果流星离开视野之内，销毁流星实例，回收内存
		        if (meteor.flow()) {
		            meteor.draw()
		        } else {
		            arr.splice(index, 1)
		        }
		    })
		    requestAnimationFrame(frame)
		}
		frame() //调用函数
```







## 关于地址引用的一道面试题

```javascript
  var a = {n: 1};
  var b = a;
  a.x = a = {n: 2};
  console.log(a.x);//undefined
  console.log(b);  // {n: 1, x: {n:2}}
  
  console.log(b.x === a)  // true
```

可以说
a.x = a = {n:2}
在这一步的时候
a.x = undefine
同时 b 也有了个 x ,因为 a 和 b 指向同一份内存
b.x = undefine
接着在等待 x 赋值的时候先执行
a = {n:2}
可恶的是原来的 a 吧指针指向了另一份内存空间 {n:2} ，注意的是 新的内存空间
{n:2} 并没有 x 这个变量，这个是关键点
所以新的 a.x 当然又是 undefined
但前面不是说了 b.x 的状态是 {n:1, x: undefined}
b.x 是有x的，所以 b.x = a 即 b.x = {n:2}

所以
a.x = undefined
b = {n:1, x: {n:2} }





-----



```javascript
function test(person) {
  person.age = 26
  person = {
    name: 'hzj',
    age: 18
  }
  return person
}
const p1 = {
  name: 'fyq',
  age: 19
}
const p2 = test(p1)
console.log(p1) // -> ?
console.log(p2) // -> ?


```



结果:

```javascript
p1：{name: “fyq”, age: 26}
p2：{name: “hzj”, age: 18}
```



> 原因: 在函数传参的时候传递的是对象在堆中的内存地址值，test函数中的实参person是p1对象的内存地址，通过调用person.age = 26确实改变了p1的值，但随后person变成了另一块内存空间的地址，并且在最后将这另外一份内存空间的地址返回，赋给了p2。



## 闭包

MDN 对闭包的定义为：

> 闭包是指那些能够访问自由变量的函数。

什么是自由变量：

> 自由变量是指在函数中使用，但既不是函数参数，也不是函数局部变量的变量。

所以

> 闭包 = 函数 + 函数能能够访问的自由变量

> 在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

----



闭包的简单定义是：函数 A 返回了一个函数 B，并且函数 B 中使用了函数 A 的变量，函数 B 就被称为闭包。

闭包是指有权访问另一个函数作用域的变量的函数。--《javascript高级程序设计》 

**一个知识点就是闭包中的变量并不保存中栈内存中，而是保存在堆内存中，这也就解释了函数之后之后为什么闭包还能引用到函数内的变量。**


```javascript
function A() {
  let a = 1
  function B() {
      console.log(a)
  }
  return B
}
```

函数 A 弹出调用栈后，函数 A 中的变量这时候是存储在堆上的，所以函数B依旧能引用到函数A中的变量。现在的 JS 引擎可以通过逃逸分析辨别出哪些变量需要存储在堆上，哪些需要存储在栈上。





**闭包产生的本质就是，当前环境中存在指向父级作用域的引用**





---

为什么在执行上下文已经弹出执行栈，子函数还可以访问到父函数的变量？



```js
var scope = 'global scope'
function checkScope() {
  var scope = 'local scope'
  return function() {
    return scope
  }
}

var foo = checkScope()
foo()
```



首先分析这段代码执行上下文栈和执行上下文的变化情况：

1. 进入全局代码，创建全局执行上下文，全局上下文压入执行上下文栈
2. 全局执行上下文初始化
3. 执行 checkScope 函数，创建函数执行上下文，函数执行上下文被压入执行上下文栈
4. checkScope 执行上下文初始化，创建变量对象、作用域链和 this 等
5. checkScope 执行完毕，上下文从执行上下文栈中弹出
6. 执行回调函数，创建函数执行上下文，压入执行上下文栈
7. 初始化函数执行上下文，创建变量对象、作用域链和 this
8. 函数执行完毕，上下文从执行上下文栈中弹出



**为什么在执行上下文已经弹出执行栈，子函数还可以访问到父函数的变量？**

这是因为在创建子函数的时候，子函数的执行上下文维护了一个作用域链：

```
Context = {
  Scope: [AO, checkScopeContext.AO, globalContext.VO]
}
```







### 闭包的用处



1. 返回一个函数。刚刚已经举例。
2. 作为函数参数传递
3. 在定时器、事件监听、Ajax请求、跨窗口通信、Web Workers或者任何异步中，只要使用了回调函数，实际上就是在使用闭包。



```javascript
var a = 1;
function foo(){
  var a = 2;
  function baz(){
    console.log(a);
  }
  bar(baz);
}
function bar(fn){
  // 这就是闭包
  fn();
}
// 输出2，而不是1
foo();

```





### 闭包的缺点

由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露





### 一个闭包的问题

[https://github.com/yygmind/blog/issues/18](https://github.com/yygmind/blog/issues/18)

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}

var foo = checkscope(); // foo指向函数f
console.log(foo());  //local scope		// 调用函数f()
```


简要的执行过程如下：


* 进入全局代码，创建全局执行上下文，全局执行上下文压入执行上下文栈
* 全局执行上下文初始化
* 执行 checkscope 函数，创建 checkscope 函数执行上下文，checkscope 执行上下文被压入执行上下文栈
* checkscope 执行上下文初始化，创建变量对象、作用域链、this等
* checkscope 函数执行完毕，checkscope 执行上下文从执行上下文栈中弹出
* 执行 f 函数，创建 f 函数执行上下文，f 执行上下文被压入执行上下文栈
* f 执行上下文初始化，创建变量对象、作用域链、this等
* f 函数执行完毕，f 函数上下文从执行上下文栈中弹出



那么问题来了， 函数f 执行的时候，checkscope 函数上下文已经被销毁了，那函数f是如何获取到scope变量的呢？



**函数f 执行上下文维护了一个作用域链，会指向指向checkscope作用域，作用域链是一个数组，结构如下。**


```javascript
fContext = {
    Scope: [AO, checkscopeContext.AO, globalContext.VO],
}
```

所以指向关系是当前作用域 --> checkscope作用域--> 全局作用域



即使 checkscopeContext 被销毁了，但是 **JavaScript 依然会让 checkscopeContext.AO（活动对象） 活在内存中**，f 函数依然可以通过 f 函数的作用域链找到它，**这就是闭包实现的关键**。



### 一个常见的闭包面试题

```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();
data[1]();
data[2]();
```

如果知道闭包的，答案就很明显了，都是3


循环结束后，全局执行上下文的VO是



```javascript
globalContext = {
    VO: {
        data: [...],
        i: 3
    }
}
```


执行 data[0] 函数的时候，data[0] 函数的作用域链为：



```javascript
data[0]Context = {
    Scope: [AO, globalContext.VO]
}
```


由于其自身没有i变量，就会向上查找，所有从全局上下文查找到i为3，data[1] 和 data[2] 是一样的。

-----

解决办法




改成闭包，方法就是data[i]返回一个函数，并访问变量i



```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function (i) {
      return function(){
          console.log(i);
      }
  })(i);
}

data[0]();	// 0
data[1]();	// 1
data[2]();	// 2
```


循环结束后的全局执行上下文没有变化。

执行 data[0] 函数的时候，data[0] 函数的作用域链发生了改变：



```javascript
data[0]Context = {
    Scope: [AO, 匿名函数Context.AO, globalContext.VO]
}
```


匿名函数执行上下文的AO为：

```javascript
匿名函数Context = {
    AO: {
        arguments: {
            0: 0,
            length: 1
        },
        i: 0
    }
}

```






### 变量提升和函数提升

```
<script>
    console.log(fun)

    console.log(person)
</script>

<script>
    console.log(person)

    console.log(fun)

    var person = "Eric";

    console.log(person)

    function fun() {
        console.log(person)
        var person = "Tom";
        console.log(person)
    }

    fun()

    console.log(person)
</script>
```


上面代码的执行结果是什么？



------



第一个 script ：
```
<script>
    console.log(fun)
    console.log(person)
</script>
```
当前全局作用域中并没有定义 fun 和 person，那么执行 console.log(fun) 会导致报错提示 fun 没有定义，并且会阻断代码继续执行，也就不会执行 console.log(person) 。

第二个 script：
```
<script>
    console.log(person)     // (1)
    console.log(fun)           // (2)

    var person = "Eric";
    console.log(person)     // (3)

    function fun() {
        console.log(person)  // (4)
        var person = "Tom";
        console.log(person)  // (5)
    }

    fun()
    console.log(person)      // (6)
</script>
```


虽然变量 person 和函数 fun 是在下方定义的，但是会发生变量提升和函数提升，因此：
(1) 处打印 undefined
(2) 处打印 function fun() { ... }

随后变量 person 被赋值为”Eric“
(3) 处打印 Eric

由于在函数 fun 中，重新定义了一个 person 变量
(4) 处打印 undefined
(5) 处打印 Tom

全局作用域中存在一个变量 person
(6) 处打印 Eric




### 关于作用域的一道面试题

```javascript
var scope="global";
function scopeTest(){
    console.log(scope);
    var scope="local"  
}
scopeTest(); //undefined
```


上面的代码输出是undefined，这是因为局部变量scope变量提升了，等效于下面



```javascript
var scope="global";
function scopeTest(){
    var scope;
    console.log(scope);
    scope="local"  
}
scopeTest(); //undefined
```


**注意，如果在局部作用域中忘记var，那么变量就被声明为全局变量。**



---



```js
var scope = 'global scope'
function checkScope() {
  var scope = 'local scope'
  function f() {
    return scope
  }
  return f()
}

checkScope() // 'local scope'

var scope = 'global scope'
function checkScope() {
  var scope = 'local scope'
  function f() {
    return scope
  }
  return f
}

checkScope()() // 'local scope'
```



两段代码都会打印 `local scope` ，这是因为 JS 采用词法作用域，函数作用域基于函数创建的位置。

> JavaScript 函数的执行用到了作用域链，**这个作用域链是在函数定义的时候创建的**。嵌套的函数 f() 定义在这个作用域链里，其中的变量 scope 一定是局部变量，不管何时何地执行函数 f()，这种绑定在执行 f() 时依然有效。



 



## 作用域

作用域负责收集和维护由所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。—— 摘录自《你不知道的JavaScript》(上卷)



作用域有两种工作模型：词法作用域和动态作用域，**JS采用的是词法作用域工作模型**，词法作用域意味着作用域是由书写代码时变量和函数声明的位置决定的。(with 和 eval 能够修改词法作用域，但是不推荐使用，对此不做特别说明)



### 作用域链


每个函数都有自己的执行上下文环境，当代码在这个环境中执行时，会创建变量对象的作用域链，作用域链是一个对象列表或对象链，它保证了变量对象的有序访问。


作用域链的开始是当前代码执行环境的变量对象，常被称之为“活跃对象”（AO），变量的查找会从第一个链的对象开始，如果对象中包含变量属性，那么就停止查找，如果没有就会继续向上级作用域链查找，直到找到全局对象中





### 作用域链和原型继承查找时的区别

* 如果去查找一个普通对象的属性，但是在当前对象和其原型中都找不到时，会返回undefined；
* 但查找的属性在作用域链中不存在的话就会抛出ReferenceError。



## 原型链



###  原型链中的原型对象赋值无效

```js
  function Ofo() { }

  function Bick() {
    this.name = 'mybick'
  }

  var myBick = new Ofo()
  Ofo.prototype = new Bick()

  var youbick = new Bick()


  console.log(myBick.name)  // undefined
  console.log(youbick.name) // mybick
```



我们在看几个例子：

```js
  function Person() { }

  Person.prototype.name = "John";

  var man = new Person();
  // 对象字面量 改变了prototype的地址
  Person.prototype = {
    name: "Mike"
  };
  // 所以这里无法修改name
  Person.prototype.name = "Tom";

  console.log(man.name); // John
```



```js
  function Person() { }

  Person.prototype.name = "John";
  var man = new Person();
  // 这里的Person.prototype 和上面的Person.prototype 指向同一个地址
  // 并没有修改prototype
  // 所以修改name有效	
  Person.prototype.name = "Tom";

  console.log(man.name); // Tom
```



```js
  function Person() { }
  Person.prototype.name = "John";
  var man = new Person();
  // man 已经生成 man对应的Person.prototype已经固化	
  Person.prototype = {
    name: "Tom"
  };
  console.log(man.name); // Tom
```







---

我们在看几个例子：

```js
  function Test() { }
  Test.prototype = { x: 1 };
  var a = new Test();
  Test.prototype = { y: 2 };
  var b = new Test();
  console.log(a.x, a.y); // 1 undefined
  console.log(b.x, b.y); // undefined 2
```



```js
  function Test() { }
  // 在a生成前 改变Test.prototype才有效果
  var a = new Test(); 
  // a已经生成了 a 对应的Test.prototype已经固化
  // 下面的代码只对b有效
  Test.prototype = { x: 1 }; 
  var b = new Test();
  Test.prototype = { y: 2 }; // b已经生成了 b 对应的Test.prototype已经固化
  console.log(a.x, a.y);  // undefined undefined
  console.log(b.x, b.y);  // 1 undefined
```





### 原型链的终点指向什么

所有原型链的终点都是Object函数的prototype属性

**因为在JavaScript中的对象都默认由Object()构造。Objec.prototype指向的原型对象同样拥有原型，不过它的原型是null，而null则没有原型。**



### 原型链的一些问题

* 实例的 __proto__ 属性（原型）等于其构造函数的 prototype 属性。
* Object.proto === Function.prototype
* Function.prototype.proto === Object.prototype
* Object.prototype.proto === null





### 关于prototype的输出问题

```js
  var foo = {},
    F = function () { };
  Object.prototype.a = 'value a';
  Function.prototype.b = 'value b';


  console.log(foo.a) // value a
  console.log(foo.b) // undefined 
  console.log(F.a) // value a
  console.log(F.b) // value b
```

这里考察了一个知识点：

```js
  F.__proto__ === Function.prototype
  Function.prototype.__proto__ === Object.prototype
```












## this全面解析



 **在 ES5 中 ,** **this 永远指向 最后调用它的那个对象**  



### this 的四种绑定规则

在 JavaScript 中，this 指向的绑定规则有以下四种：

- 默认绑定（非严格模式情况下，this 指向 `window`, 严格模式下，this指向 `undefined`。）
- 隐式绑定（如果函数调用时，前面存在调用它的对象，那么this就会隐式绑定到这个对象上）
- 显式绑定（函数通过 `call()`、`apply()`、`bind()`调用，this 指向被绑定的对象。）
- new 绑定（函数被 `new` 调用，`this` 指向由 `new` 新构造出来的这个对象。）




### 隐式丢失


被隐式绑定的函数特定情况下会丢失绑定对象，应用默认绑定，把this绑定到全局对象或者undefined上。

```javascript
// 虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身。
// bar()是一个不带任何修饰的函数调用，应用默认绑定。
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; // 函数别名

var a = "oops, global"; // a是全局对象的属性

// 全局环境的执行上下文
bar(); // "oops, global"
```


参数传递就是一种隐式赋值，传入函数时也会被隐式赋值。**回调函数丢失this绑定是非常常见的。**

---

例子：



```javascript
function foo() {
    console.log( this.a );
}

function doFoo(fn) {
    // fn其实引用的是foo
    
    fn(); // <-- 调用位置！
    (fn的执行上下文环境其实是全局环境 也就是window)
}

var obj = {
    a: 2,
    foo: foo
};

var a = "oops, global"; // a是全局对象的属性

doFoo( obj.foo ); // "oops, global"
```

----


**回调函数丢失this绑定会出现在setTimeout中**

**这里要非常注意,在平时写代码的时候得小心**


```javascript
// JS环境中内置的setTimeout()函数实现和下面的伪代码类似：
function setTimeout(fn, delay) {
    // 等待delay毫秒
    fn(); // <-- 调用位置！
}
```







### 关于this的面试题

```javascript
  function foo() { // 运行
    console.log(this.a);   // 2
  }

  var a = 2;

  (function () { // 严格模式下调用函数则不影响默认绑定
    "use strict";

    foo(); // 2
  })();
```

严格模式下，不能将全局对象用于默认绑定，this会绑定到undefined。只有函数运行在非严格模式下，默认绑定才能绑定到全局对象。在严格模式下调用函数则不影响默认绑定。


```javascript
  function foo() { // 运行在严格模式下，this会绑定到undefined
    "use strict";
   
    // TypeError: Cannot read property 'a' of undefined
    console.log(this.a); 
  }

  var a = 2;

  foo();
```





----



值的输出



```js
var num = 1; // 这里在自执行函数里被改为4

let obj = {
  num: 2,
  add: function () {
    this.num = 3;
    (function () {
      console.log(this) // 这里自执行函数的this指向window
      console.log(this.num); // 1
      this.num = 4; // 改变 window.num
    })();
    console.log(this.num); // 3
  },
  sub: function () {
    console.log(this.num);
  },
};

obj.add(); // 输出 1 3  
console.log(obj.num); // 3
console.log(num); // 4

const sub = obj.sub;
sub(); // 4  sub在window环境下执行

```



----



```js
var num = 10;

const obj = { num: 20 };

obj.fn = (function (num) {
  // 传进来的num是20
  this.num = num * 3;  // 60
  num++; // 21
  console.log(this) // 这里的this指向window
  

  return function (n) {
    this.num += n; // 65
    num++; // 22
    console.log(num); 
    console.log(this) // 这里的this要看具体执行 可能是window 可能是obj
  };

})(obj.num);


var fn = obj.fn;
fn(5); // 22

obj.fn(10); // 23

console.log(num, obj.num);  //  65  30
// 第一个num 相当于window.num 在fn(5)中 变成了65
// obj.num 在obj.fn(10)的执行中 20 + 10 变成了30

```









### call

模拟一个call的实现


只要实现下面3步就可以模拟实现了


1. 将函数设置为对象的属性：foo.fn = bar
2. 执行函数：foo.fn()
3. 删除函数：delete foo.fn


```javascript
// 第一版
Function.prototype.call2 = function(context) {
    // 首先要获取调用call的函数，用this可以获取
    context.fn = this; 		// foo.fn = bar
    context.fn();			// foo.fn()
    delete context.fn;		// delete foo.fn
}




// 测试一下
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call2(foo); // 1
```

---





**模拟实现第二步**




第一版有一个问题，那就是函数 bar 不能接收参数，所以我们可以从 arguments中获取参数，取出第二个到最后一个参数放到数组中，为什么要抛弃第一个参数呢，因为第一个参数是 this。





```javascript
  Function.prototype.call2 = function (context, ...args) {
    context.fn = this
    context.fn(...args)
    delete  context.fn
  }


  // 测试一下
  var foo = {
    value: 1
  };

  function bar(aaa) {
    console.log(aaa, this.value);
  }

  bar.call2(foo, "123"); // 123 1
```



----



**模拟实现第三步**



还有一些细节的东西需要注意


1. this 参数可以传 null 或者 undefined，此时 this 指向 window
2. this 参数可以传基本类型数据，原生的 call 会自动用 Object() 转换
3. 函数是可以有返回值的

```javascript
  Function.prototype.call2 = function (context, ...args) {
    // 实现细节 1 和 2
    context = context ? Object(context) : window
    context.fn = this
    let result = context.fn(...args)
    delete  context.fn
    // 实现细节 3
    return result
  }


  // 测试一下
  var foo = {
    value: 1
  };

  function bar(aaa) {
    console.log(aaa, this.value);
    return aaa + this.value
  }

  let res = bar.call2(foo, 123); // 123 1
  console.log(res) // 124
```



### apply

实现一个apply

```javascript
  Function.prototype.apply = function (context, arr) {
    context = context ? Object(context) : window
    context.fn = this
    let result
    if (!arr) {
      result = context.fn()
    } else {
      result = context.fn(...arr)
    }
    delete context.fn
    return result
  }
```


### bind

[https://muyiy.cn/blog/3/3.4.html#bind](https://muyiy.cn/blog/3/3.4.html#bind)



bind 有如下特性：

1. 可以指定this
2. 返回一个函数
3. 可以传入参数
4. 柯里化



-----

**模拟实现第一步**



对于第 1 点，使用 call / apply 指定 this 。

对于第 2 点，使用 return 返回一个函数。

结合前面 2 点，可以写出第一版，代码如下：


```javascript
// 第一版
Function.prototype.bind2 = function(context) {
    var self = this; // this 指向调用者
    return function () { // 实现第 2点
        return self.apply(context); // 实现第 1 点
    }
}
```

-----



**模拟实现第二步**


对于第 3 点，使用 arguments 获取参数数组并作为 self.apply() 的第二个参数。

对于第 4 点，获取返回函数的参数，然后同第3点的参数合并成一个参数数组，并作为 self.apply() 的第二个参数。


```javascript
  Function.prototype.bind2 = function (context) {
    let self = this
    // 实现第3点，因为第1个参数是指定的this,所以只截取第1个之后的参数
    let args = Array.prototype.slice.call(arguments, 1)
    return function () {
      // 实现第4点，这时的arguments是指bind返回的函数传入的参数
      let bindArgs = Array.prototype.slice.call(arguments);
      return self.apply(context, args.concat(bindArgs))
    }
  }
```

---

**模拟实现第三步**



到现在已经完成大部分了，但是还有一个难点，bind 有以下一个特性



>一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器，提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。


来个例子说明下：


```javascript
var value = 2;
var foo = {
    value: 1
};
function bar(name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = 'kevin';

var bindFoo = bar.bind(foo, 'Jack');
var obj = new bindFoo(20);
// undefined
// Jack
// 20

obj.habit;
// shopping

obj.friend;
// kevin
```



```javascript
// 第三版
Function.prototype.bind2 = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        
        // 注释1
        return self.apply(
            this instanceof fBound ? this : context, 
            args.concat(bindArgs)
        );
    }
    // 注释2
    fBound.prototype = this.prototype;
    return fBound;
}
```


* 注释1：
  * 当作为构造函数时，this 指向实例，此时 this instanceof fBound 结果为 true，可以让实例获得来自绑定函数的值，即上例中实例会具有 habit 属性。
  * 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
* 注释2： 
  * 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值，即上例中 obj 可以获取到 bar 原型上的 friend。

----

**模拟实现第四步**



上面实现中 fBound.prototype = this.prototype有一个缺点，直接修改 fBound.prototype 的时候，也会直接修改 this.prototype。


来个代码测试下：

```javascript
// 测试用例
var value = 2;
var foo = {
    value: 1
};
function bar(name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = 'kevin';

var bindFoo = bar.bind2(foo, 'Jack'); // bind2
var obj = new bindFoo(20); // 返回正确
// undefined
// Jack
// 20

obj.habit; // 返回正确
// shopping

obj.friend; // 返回正确
// kevin

obj.__proto__.friend = "Kitty"; // 修改原型

bar.prototype.friend; // 返回错误，这里被修改了
// Kitty
```


这边可以直接使用ES5的 Object.create()方法生成一个新对象

```
fBound.prototype = Object.create(this.prototype);
```





```javascript
// 第四版
Function.prototype.bind2 = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        
        // 注释1
        return self.apply(
            this instanceof fBound ? this : context, 
            args.concat(bindArgs)
        );
    }
    // 注释2
    fBound.prototype = Object.create(this.prototype);
    return fBound;
}
```

----



**模拟实现第五步**


到这里其实已经差不多了，但有一个问题是调用 bind 的不是函数，这时候需要抛出异常。

```javascript
if (typeof this !== "function") {
  throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
}
```

### 自执行函数的思考题

当我们调用对象中的一个属性是自执行函数时

```javascript
  var obj = {
    // 2、say 是立即执行函数
    say: function () {
      function _say() {
        console.log(this);
      }
      console.log("123");
      return _say.bind(obj);
    }(),
  };

  obj.say;   // "123"
```



---




升级题目


```javascript
// 1、赋值语句是右执行的,此时会先执行右侧的对象
var obj = {
    // 2、say 是立即执行函数
    say: function() {
        function _say() {
            // 5、输出 window
            console.log(this);
        }
        // 3、编译阶段 obj 赋值为 undefined
        console.log(obj);
        // 4、obj是 undefined，bind 本身是 call实现，
        // ：call 接收 undefined 会绑定到 window。
        return _say.bind(obj);
    }(),
};
obj.say();  // undefined window
```



### 自执行函数的this指向window

[https://segmentfault.com/q/1010000004648772](https://segmentfault.com/q/1010000004648772)

从上面的题目的题目我们可以知道，obj.say执行，里面的this不是指向obj，而是指向window。

这是为什么？



当你要确定“函数中的this是什么”的时候，永远不要到函数**定义的地方**去找答案！而是要到**函数被调用**的地方找答案！



obj.say中的自执行函数所在环境是window，所以里面的this也是指向window。





### call模拟实现的一个问题

```javascript
Function.prototype.call = function (context) {
    context = context ? Object(context) : window; 
    context.fn = this;

    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    var result = eval('context.fn(' + args +')');

    delete context.fn;
    return result;
}

```

当然是有问题的，其实这里假设 context 对象本身没有 fn 属性，这样肯定不行，我们必须保证 fn属性的唯一性。

**ES6下模拟实现**

ES6有一个新的基本类型Symbol，表示独一无二的值。


```javascript
Function.prototype.call = function (context) {
  context = context ? Object(context) : window; 
  var fn = Symbol(); // added
  context[fn] = this; // changed

  let args = [...arguments].slice(1);
  let result = context[fn](...args); // changed

  delete context[fn]; // changed
  return result;
}
```



## 函数实现



### 实现curry函数

**不断递归获取传入参数，直到取到的参数个数等于fn的参数个数为止，最终将获取到的所有参数传给fn并返回执行结果。**


```javascript
  function curry(fn) {
    const c = (...args) =>
      (args.length === fn.length) ?
        fn(...args) :
        (..._args) => c(...args, ..._args)

    return c
  }
```

测试


```javascript
  function add(x, y) {
    return x + y
  }

  let after = curry(add)
  let res = after(1)(2)

  console.log(res) //3
```



该方法几乎为最简洁、代码行数最少的实现方法了。



### 实现一个无限累加的函数

[https://www.cnblogs.com/oxspirt/p/5436629.html](https://www.cnblogs.com/oxspirt/p/5436629.html)

示例如下：


```javascript
add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10 

// 以此类推
```

----


具体实现

```javascript
function add(a){
 function s(b){
    a =   a+b;
    return s;
 }
 s.toString = function(){return a;}
 return s;
}
console.log(add(1)(2)(3)(4));
```


1. 使用闭包， 同时要对JavaScript 的作用域链（原型链）有深入的理解；
2. 重写函数的 toSting()方法；





### 实现一个instanceof

instanceof 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 prototype。



实现 instanceof：

1. 首先获取类型的原型
2. 然后获得对象的原型
3. 然后一直循环判断对象的原型是否等于类型的原型，直到对象原型为 null，因为原型链最终为 null







```js
function myInstanceof(letf,right){
  prototype = right.prototype 
  letf = letf.__proto__
  while(true){
    if(!letf){
      return false
    }
    if(letf === prototype){
      return true 
    }
    left = left.__proto__
  }
}
```



### 写一个自定义定义log函数

```javascript
 function log() {
    console.log.apply(console, arguments);
  }
```

进阶：向这个log函数添加前缀

```javascript
  function log() {
    const args = Array.prototype.slice.call(arguments);
    args.unshift('前缀');
    console.log.apply(console, args);
  }
```

>**Array.prototype.slice.call可以将伪数组转换成真正的数组，这是个重要的技巧。**



### 实现一个LazyMan



实现一个LazyMan，可以按照以下方式调用：

`LazyMan('Hank')`，输出：

> Hi, This is Hank!

`LazyMan('Hank').sleep(5).eat('dinner')`，输出：

> Hi, This is Hank!
> // 等待5秒
> Weak up after 5
> Eat dinner ~

`LazyMan('Hank').eat('dinner').eat('supper')`，输出

> Hi, this is Hank!
> Eat dinner ~
> Eat supper ~

`LazyMan('Hank').sleepFirst(5).eat('supper')`，输出

> // 等待5秒
> Wake up after 5
> Hi, this is Hank!
> Eat supper





解决：



```javascript
        class LLazyMan {
            constructor(name) {
                this.name = name
                this.runTimer = null
                this.queueList = []
                this.sayHi()
            }
            sayHi() {
                this.queueList.push(() => {
                    console.log(`Hi, This is ${this.name}!`)
                })
                return this.run()
            }

            sleep(timer) {
                this.queueList.push(() => {
                    return new Promise((resolve, reject) => {
                        console.log("延时 ", timer)
                        setTimeout(() => {
                            resolve()
                        }, timer * 1000)
                    })
                })
                return this.run()
            }

            sleepFirst(timer) {
                this.queueList.unshift(() => {
                    return new Promise((resolve, reject) => {
                        console.log("延时 ", timer)
                        setTimeout(() => {
                            resolve()
                        }, timer * 1000)
                    })
                })
                return this.run()
            }

            run() {
                if (this.runTimer) {
                    clearTimeout(this.runTimer)
                }
                this.runTimer = setTimeout(async () => {
                    console.log(this.queueList)
                    for (let fn of this.queueList) {
                        await fn()
                    }
                    this.queueList = []
                    this.runTimer = null
                }, 0)
                return this
            }

            eat(name) {
                this.queueList.push(() => {
                    console.log(`Eat ${name} ~`)
                })
                return this.run()
            }
        }

        let LazyMan = name => (new LLazyMan(name))

        LazyMan('Hank').eat("aaa").sleepFirst(5)



```








## new

>new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。 ——（来自于MDN）





使用new来调用函数，或者说发生构造函数调用时，会自动执行下面的操作



1. 创建（或者说构造）一个新对象。
2. 这个新对象会被执行[[Prototype]]连接。
3. 这个新对象会绑定到函数调用的this。
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。



>在 JavaScript 中，new 操作符并不像其他面向对象的语言一样，而是一种模拟出来的机制。在 JavaScript 中，所有的函数都可以被 new 调用，这时候这个函数一般会被称为 “构造函数”，实际上并不存在所谓“构造函数”，更确切的理解应该是对于函数的 “构造器调用模式”。
>
>
>
>以上关于 new 绑定来源于你不知道的js上卷 2.2绑定规则。

### 手写一个new

**模拟实现第一步**


```javascript
function create() {
	// 创建一个空的对象
    var obj = new Object(),
	// 获得构造函数，arguments中去除第一个参数
    Con = [].shift.call(arguments);
	// 链接到原型，obj 可以访问到构造函数原型中的属性
    obj.__proto__ = Con.prototype;
	// 绑定 this 实现继承，obj 可以访问到构造函数中的属性
    var ret = Con.apply(obj, arguments);
	// 优先返回构造函数返回的对象
	return ret instanceof Object ? ret : obj;
};

```


使用这个手写的new


```javascript
// 测试用例
function Car(color) {
    this.color = color;
}
Car.prototype.start = function() {
    console.log(this.color + " car start");
}

var car = create(Car, "black");
car.color;
// black

car.start();
// black car start
```


非常要注意的一点

**var ret = Con.apply(obj, arguments);**


构造函数执行时要绑定obj的上下文环境,在构造函数中经常会用到this.xxx =xxxx 

这里的this是指上面的obj



---

**模拟实现第二步**



构造函数返回值有如下三种情况：



1. 返回一个对象
2. 没有 return，即返回 undefined
3. 返回undefined 以外的基本类型





情况3：返回undefined 以外的基本类型

```javascript
function Car(color, name) {
    this.color = color;
    return "new car";
}

var car = new Car("black", "BMW");
car.color;
// black

car.name;
// undefined
```


实例 car 中只能访问到**构造函数中的属性**，和情况1完全相反，结果相当于没有返回值。

**所以需要判断下返回的值是不是一个对象，如果是对象则返回这个对象，不然返回新创建的 obj对象。**








```javascript
// 第二版
function create() {
	// 1、获得构造函数，同时删除 arguments 中第一个参数
    Con = [].shift.call(arguments);
	// 2、创建一个空的对象并链接到原型，obj 可以访问构造函数原型中的属性
    var obj = Object.create(Con.prototype);
	// 3、绑定 this 实现继承，obj 可以访问到构造函数中的属性
    var ret = Con.apply(obj, arguments);
	// 4、优先返回构造函数返回的对象
	return ret instanceof Object ? ret : obj;
};
```



### new的优先级

```javascript
function Foo() {
    return this;
}
Foo.getName = function () {
    console.log('1');
};
Foo.prototype.getName = function () {
    console.log('2');
};

new Foo.getName();   // -> 1
new Foo().getName(); // -> 2
```


new Foo() 的优先级大于 new Foo ，所以对于上述代码来说可以这样划分执行顺序


```javascript
new (Foo.getName());
(new Foo()).getName();
```

对于第一个函数来说，先执行了 Foo.getName() ，所以结果为 1；对于后者来说，先执行 new Foo() 产生了一个实例，然后通过原型链找到了 Foo 上的 getName 函数，所以结果为 2。



### 构造函数中的this指向



 JS里没有类.
构造函数是个**函数**,this指向的是个**对象**,this蒙上眼睛指也指不到构造函数去. 



 **构造函数的this指向创建的实例对象无疑** 





 原本的构造函数是window对象的方法，如果不用new操作符而直接调用，那么构造函数的执行对象就 是window，即this指向了window。现在用new操作符后，this就指向了新生成的对象。理解这一步至关重要。 


















## 拷贝相关



### 浅拷贝



#### 浅拷贝的使用场景

* Object.assign()
* 展开语法 Spread
* Array.prototype.slice()


#### 什么是深拷贝

深拷贝会拷贝所有的属性，并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。拷贝前后两个对象互不影响。



深拷贝使用场景：

* JSON.parse(JSON.stringify(object))

但是该方法有以下几个问题。

1. 会忽略 undefined
2. 会忽略 symbol
3. 不能序列化函数
4. 不能解决循环引用的对象
5. 不能正确处理new Date()
6. 不能处理正则



#### 实现一个深拷贝

一个简单的深拷贝

```javascript
function cloneDeep1(source) {
    var target = {};
    for(var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (typeof source[key] === 'object') {
                target[key] = cloneDeep1(source[key]); // 注意这里
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}
```




* 对于对象的判断逻辑不严谨，因为 typeof null === 'object'
* 没有考虑数组的兼容

-----

改动过后的 isObject 判断逻辑如下

```javascript
function isObject(obj) {
	return typeof obj === 'object' && obj != null;
}
```

---



#### 深拷贝如何解决循环引用

需要解决拷贝对象内的循环引用问题（使用数组记录拷贝过的对象，记录一个拷贝对象的数据结构，{source:原拷贝对象，target:拷贝后的对象}）



```js
//存放已拷贝的对象用于循环引用检测
let objArr = [];
 
function deepCopy(obj) {
  //判断循环引用检测
  for (let ele of objArr) {
    if (obj === ele.source) {
      // 在递归中已经出现过  
      return ele.target;
    }
  }
  //拷贝容器
  let newObj = {};
 
  //将拷贝的对象放入数组中用于循环引用检测
  objArr.push({
    source: obj, //被拷贝对象上的原引用对象，用于循环检测比对
    target: newObj
  })
 
  // 使用Reflect可以检测到Symbol类型的属性
  Reflect.ownKeys(obj).forEach(key => {
    if (obj.hasOwnProperty(key)) {
      if (typeof obj[key] === 'object') {
        // 使用Array.from对拷贝的数组进行处理
        newObj[key] = Object.prototype.toString.call(obj[key]) === '[object Array]' ? Array.from(deepCopy(obj[key], key)) : deepCopy(obj[key], key);
      } else {
        // 属性值为原始类型的值
        newObj[key] = obj[key];
      }
    }
  })
  return newObj;
}
```

使用递归的方式可能会造成爆栈，解决办法就是采用迭代的方式

```js
function cloneForce(x) {
  //拷贝对象记录
  const uniqueList = []; 
 
  let root = {};
 
  // 循环数组
  const loopList = [{
    parent: root,
    key: undefined,
    data: x,
  }];
 
  while (loopList.length) {
    //深拷贝，元素出栈
    const node = loopList.pop();
    const parent = node.parent;
    const key = node.key;
    const data = node.data;
 
    let res = parent;
    if (typeof key !== 'undefined') {
      res = parent[key] = {};
    }
 
    // 判断数据是否存在
    let uniqueData = find(uniqueList, data);、
    //数据存在
    if (uniqueData) {
      parent[key] = uniqueData.target;
      break; // 中断本次循环
    }
 
    //数据不存在，将其放入数组
    uniqueList.push({
      source: data,
      target: res,
    });
 
    for (let k in data) {
      if (data.hasOwnProperty(k)) {
        if (typeof data[k] === 'object') {
          // 下一次循环
          loopList.push({
            parent: res,
            key: k,
            data: data[k],
          });
        } else {
          res[k] = data[k];
        }
      }
    }
  }
 
  return root;
}
 
function find(arr, item) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i].source === item) {
      return arr[i];
    }
  }
 
  return null;
}
```

### Object.assign 

 主要是将所有**可枚举属性**的值从一个或多个源对象复制到目标对象，同时返回目标对象。



模拟实现：

```javascript
if (typeof Object.assign2 != 'function') {
  // Attention 1
  Object.defineProperty(Object, "assign2", {
    value: function (target) {
      'use strict';
      if (target == null) { // Attention 2
        throw new TypeError('Cannot convert undefined or null to object');
      }

      // Attention 3
      var to = Object(target);
        
      for (var index = 1; index < arguments.length; index++) {
        var nextSource = arguments[index];

        if (nextSource != null) {  // Attention 2
          // Attention 4
          for (var nextKey in nextSource) {
            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
              to[nextKey] = nextSource[nextKey];
            }
          }
        }
      }
      return to;
    },
    writable: true,
    configurable: true
  });
}
```

**Object.assign 方法肯定不会拷贝原型链上的属性，所以模拟实现时需要用 hasOwnProperty(..) 判断处理下**






#### Object.assign 是不可枚举的

我们可以使用 2 种方法查看 Object.assign 是否可枚举，使用 Object.getOwnPropertyDescriptor 或者 Object.propertyIsEnumerable 都可以，其中propertyIsEnumerable(..) 会检查给定的属性名是否直接存在于对象中（而不是在原型链上）并且满足 enumerable: true。

具体用法如下：

```javascript
// 方法1
Object.getOwnPropertyDescriptor(Object, "assign");
// {
// 	value: ƒ, 
//  writable: true, 	// 可写
//  enumerable: false,  // 不可枚举，注意这里是 false
//  configurable: true	// 可配置
// }

// 方法2
Object.propertyIsEnumerable("assign");
// false
```

上面代码说明 Object.assign 是不可枚举的。



**所以要实现 Object.assign 必须使用 Object.defineProperty**，并设置 writable: true, enumerable: false, configurable: true，当然默认情况下不设置就是 false。



#### Object.assign使用时原始类型被包装为对象

```javascript
var v1 = "abc";
var v2 = true;
var v3 = 10;
var v4 = Symbol("foo");

var obj = Object.assign({}, v1, null, v2, undefined, v3, v4); 
// 原始类型会被包装，null 和 undefined 会被忽略。
// 注意，只有字符串的包装对象才可能有自身可枚举属性。
console.log(obj); 
// { "0": "a", "1": "b", "2": "c" }
```

上面代码中的源对象 v2、v3、v4 实际上被忽略了，**原因在于他们自身没有可枚举属性**。



证明：


```javascript
var v1 = "abc";
var v2 = true;
var v3 = 10;
var v4 = Symbol("foo");
var v5 = null;

// Object.keys(..) 返回一个数组，包含所有可枚举属性
// 只会查找对象直接包含的属性，不查找[[Prototype]]链
Object.keys( v1 ); // [ '0', '1', '2' ]
Object.keys( v2 ); // []
Object.keys( v3 ); // []
Object.keys( v4 ); // []
Object.keys( v5 ); 
// TypeError: Cannot convert undefined or null to object

// Object.getOwnPropertyNames(..) 返回一个数组，包含所有属性，无论它们是否可枚举
// 只会查找对象直接包含的属性，不查找[[Prototype]]链
Object.getOwnPropertyNames( v1 ); // [ '0', '1', '2', 'length' ]
Object.getOwnPropertyNames( v2 ); // []
Object.getOwnPropertyNames( v3 ); // []
Object.getOwnPropertyNames( v4 ); // []
Object.getOwnPropertyNames( v5 ); 
// TypeError: Cannot convert undefined or null to object
```

----



但是下面的代码是可以执行的。

```javascript
var a = "abc";
var b = {
    v1: "def",
    v2: true,
    v3: 10,
    v4: Symbol("foo"),
    v5: null,
    v6: undefined
}

var obj = Object.assign(a, b); 
console.log(obj);
// { 
//   [String: 'abc']
//   v1: 'def',
//   v2: true,
//   v3: 10,
//   v4: Symbol(foo),
//   v5: null,
//   v6: undefined 
// }
```


原因很简单，因为此时 undefined、true 等不是作为对象，而是作为对象 b 的属性值，对象 b 是可枚举的。






### Object 上的属性是不可枚举的


```javascript
for(var i in Object) {
    console.log(Object[i]);
}

// 无输出
```



```javascript
  Object.aaa = "aaa"

  for (var i in Object) {
    console.log(Object[i]);
  }
  
  // "aaa"
```


**上面代码说明原生 Object 上的属性不可枚举。**


### hasOwnProperty会失败


通过 Object.create(null) 来创建，这种情况下，使用 myObject.hasOwnProperty(..) 就会失败。




**因为没有连接到 Object.prototype**




```javascript
var myObject = Object.create( null );
myObject.b = 2;

("b" in myObject); 
// true

myObject.hasOwnProperty( "b" );
// TypeError: myObject.hasOwnProperty is not a function
```


解决方法也很简单 

使用call


```javascript
var myObject = Object.create( null );
myObject.b = 2;

Object.prototype.hasOwnProperty.call(myObject, "b");
// true
```

---

```javascript
// 模拟实现Object.assign时

// 使用 for..in 遍历对象 nextSource 获取属性值
// 此处会同时检查其原型链上的属性
for (var nextKey in nextSource) {
    // 使用 hasOwnProperty 判断对象 nextSource 中是否存在属性 nextKey
    // 过滤其原型链上的属性
    if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
        // 赋值给对象 to,并在遍历结束后返回对象 to
        to[nextKey] = nextSource[nextKey];
    }
}
```







## 原型 Prototype



### constructor 值只读吗



这个得分情况，对于引用类型来说 constructor 属性值是可以修改的，但是对于基本类型来说是只读的。


引用类型情况其值可修改这个很好理解，比如原型链继承方案中，就需要对 constructor重新赋值进行修正。



```javascript
function Foo() {
    this.value = 42;
}
Foo.prototype = {
    method: function() {}
};

function Bar() {}

// 设置 Bar 的 prototype 属性为 Foo 的实例对象
Bar.prototype = new Foo();
Bar.prototype.foo = 'Hello World';

Bar.prototype.constructor === Object;
// true

// 修正 Bar.prototype.constructor 为 Bar 本身
Bar.prototype.constructor = Bar;

var test = new Bar() // 创建 Bar 的一个新实例
console.log(test);
```



-----


**constructor对于基本类型来说是只读的**

```javascript
function Type() { };
var	types = [1, "muyiy", true, Symbol(123)];

for(var i = 0; i < types.length; i++) {
	types[i].constructor = Type;
	types[i] = [ types[i].constructor, types[i] instanceof Type, types[i].toString() ];
};

console.log( types.join("\n") );
// function Number() { [native code] }, false, 1
// function String() { [native code] }, false, muyiy
// function Boolean() { [native code] }, false, true
// function Symbol() { [native code] }, false, Symbol(123)
```


为什么呢？因为创建他们的是只读的原生构造函数（native constructors），这个例子也说明了依赖一个对象的 constructor 属性并不安全。


### 不要使用Bar.prototype = Foo

```javascript
function Foo() {
  	return 'foo';
}
Foo.prototype.method = function() {
  	return 'method';
}
function Bar() {
  	return 'bar';
}
Bar.prototype = Foo; // Bar.prototype 指向到函数
let bar = new Bar();
console.dir(bar);

bar.method(); // Uncaught TypeError: bar.method is not a function
```


### Function.__proto__ === Function.prototype


先看Function.prototype的定义

>The Function prototype object is itself a Function object (its [[Class]] is "Function").

>The value of the [[Prototype]] internal property of the Function prototype object is the standard built-in Object prototype object.

>The Function prototype object does not have a valueOf property of its own; however, it inherits the valueOf property from the Object prototype Object.



Function.prototype 对象是一个函数（对象），其 [[Prototype]] 内部属性值指向内建对象 Object.prototype。Function.prototype 对象自身没有 valueOf 属性，其从 Object.prototype 对象继承了valueOf 属性。


```javascript
Function.prototype
// ƒ () { [native code] }

Function.prototype.prototype
// undefined
```


为什么没有呢，我的理解是 Function.prototype 是引擎创建出来的函数，引擎认为不需要给这个函数对象添加 prototype 属性，不然 Function.prototype.prototype… 将无休无止并且没有存在的意义。

----




在看Object



Object 作为构造函数时，其 [[Prototype]] 内部属性值指向 Function.prototype，即


>The value of the [[Prototype]] internal property of the Object constructor is the standard built-in Function prototype object.



```javascript
Object.__proto__ === Function.prototype
// true
```



-----



再看Function




>The Function constructor is itself a Function object and its [[Class]] is "Function". The value of the [[Prototype]] internal property of the Function constructor is the standard built-in Function prototype object.


Function 构造函数是一个函数对象，其 [[Class]] 属性是 Function。Function 的 [[Prototype]] 属性指向了 Function.prototype，即

```javascript
Function.__proto__ === Function.prototype
// true
```

-----



鸡生蛋蛋生鸡问题

```javascript
Object instanceof Function 		// true
Function instanceof Object 		// true

Object instanceof Object 			// true
Function instanceof Function 	// true
```



Object 构造函数继承了 Function.prototype，同时 Function 构造函数继承了Object.prototype。这里就产生了 鸡和蛋 的问题。为什么会出现这种问题，因为 Function.prototype 和 Function.__proto__ 都指向 Function.prototype。


```javascript
// Object instanceof Function 	即
Object.__proto__ === Function.prototype 					// true

// Function instanceof Object 	即
Function.__proto__.__proto__ === Object.prototype	// true

// Object instanceof Object 		即 			
Object.__proto__.__proto__ === Object.prototype 	// true

// Function instanceof Function 即	
Function.__proto__ === Function.prototype					// true
```


解释 1、YES：按照 JavaScript 中“实例”的定义，a 是 b 的实例即 a instanceof b 为 true，默认判断条件就是 b.prototype 在 a 的原型链上。而 Function instanceof Function 为 true，本质上即 Object.getPrototypeOf(Function) === Function.prototype，正符合此定义。

解释 2、NO：Function 是 built-in 的对象，也就是并不存在“Function对象由Function构造函数创建”这样显然会造成鸡生蛋蛋生鸡的问题。实际上，当你直接写一个函数时（如 function f() {} 或 x => x），也不存在调用 Function 构造器，只有在显式调用 Function 构造器时（如 new Function('x', 'return x') ）才有。

我个人偏向于第二种解释，即先有 Function.prototype 然后有的 function Function() ，所以就不存在鸡生蛋蛋生鸡问题了，把 Function.__proto__ 指向 Function.prototype 是为了保证原型链的完整，让 Function 可以获取定义在 Object.prototype 上的方法。





## 事件循环相关

https://zhuanlan.zhihu.com/p/33058983



[深入探究 eventloop 与浏览器渲染的时序问题](https://www.404forest.com/2017/07/18/how-javascript-actually-works-eventloop-and-uirendering/#4-requestAnimationFrame-callback-%E7%9A%84%E6%89%A7%E8%A1%8C%E6%97%B6%E6%9C%BA)

**并不是每轮 eventloop 都会执行 UI Render**







### Event loop介绍

1. Javascript的事件分为同步任务和异步任务.
2. 遇到同步任务就放在执行栈中执行.
3. 遇到异步任务会在合适的时间放到任务队列之中，等到执行栈执行完毕之后再去执行任务队列之中的事件.



JS中存在一个叫做执行栈的东西。JS的所有同步代码都在这里执行，当执行一个函数调用时，会创建一个新的执行环境并压到栈中开始执行函数中的代码，当函数中的代码执行完毕后将执行环境从栈中弹出，当栈空了，也就代表执行完毕。





> ```
> setTimeout(_=>{}, 1000)
> ```
>
> 代码中setTimeout会在一秒后将回调函数添加到任务队列中



事件循环的顺序，决定了JavaScript代码的执行顺序。它从script(整体代码)开始第一次循环。之后全局上下文进入函数调用栈。直到调用栈清空(只剩全局)，当执行栈空了，会检查微任务队列中是否有任务，将微任务队列中的任务依次拿出来执行一遍。当微任务队列空了，从宏任务队列中拿出来一个任务去执行，执行完毕后检查微任务队列，微任务队列空了之后再从宏任务队列中拿出来一个任务执行。这样持续的交替执行任务叫做**事件循环**。



注意点:

**当script执行完毕之后，就开始执行所有的可执行的微任务**

**当script执行完毕之后，就开始执行所有的可执行的微任务**

**当script执行完毕之后，就开始执行所有的可执行的微任务**











### 任务队列(Task Queue)

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。主线程的读取过程基本上是自动的，只要执行栈一清空，"任务队列"上第一位的事件就自动进入主线程。但是，如果存在"定时器"，主线程首先要检查一下执行时间，某些事件只有到了规定的时间，才能返回主线程。



setTimeout/Promise等我们称之为任务源。而进入任务队列的是他们指定的具体执行任务。

 ```js
// setTimeout中的回调函数才是进入任务队列的任务
setTimeout(function() {
    console.log('xxxx');
})
// 非常多的同学对于setTimeout的理解存在偏差。所以大概说一下误解：
// setTimeout作为一个任务分发器，这个函数会立即执行，而它所要分发的任务，也就是它的第一个参数，才是延迟执行
 ```









## Load 和 DOMContentLoaded 区别

* Load 事件触发代表页面中的 DOM，CSS，JS，图片已经全部加载完毕。
* DOMContentLoaded 事件触发代表初始的 HTML 被完全加载和解析，不需要等待 CSS，JS，图片加载。



[https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)



> 注意：**`DOMContentLoaded`** 事件必须等待其所属script之前的样式表加载解析完成才会触发。





```js
document.addEventListener('DOMContentLoaded',function(){
    console.log('DOMContentLoaded!!!!!!!');
});
```







## null和undefined的差异

* null转为数字类型值为0,而undefined转为数字类型为 NaN(Not a Number)
* undefined是代表调用一个值而该值却没有赋值,这时候默认则为undefined
* null是一个很特殊的对象,最为常见的一个用法就是作为参数传入
* 设置为null的变量或者对象会被内存收集器回收




>相同点:在 if判断语句中,值都默认为 false





## 类数组

[https://github.com/i-want-offer/FE-Essay/blob/master/JS/JavaScript%20%E6%B7%B1%E5%85%A5%E7%B3%BB%E5%88%97/%E7%B1%BB%E6%95%B0%E7%BB%84.md](https://github.com/i-want-offer/FE-Essay/blob/master/JS/JavaScript%20%E6%B7%B1%E5%85%A5%E7%B3%BB%E5%88%97/%E7%B1%BB%E6%95%B0%E7%BB%84.md)

所谓类数组对象：拥有一个 length 属性和若干索引属性的对象





区别在于类数组无法调用数组的方法：

```js
var array = ['name', 'age', 'sex']
var arrayLike = {
  0: 'name',
  1: 'age',
  2: 'sex',
  length: 3
}

arrayLike.push('4') // arrayLike.push is not a function
```





## sort实现

[https://github.com/v8/v8/blob/ad82a40509c5b5b4680d4299c8f08d6c6d31af3c/src/js/array.js#L710](https://github.com/v8/v8/blob/ad82a40509c5b5b4680d4299c8f08d6c6d31af3c/src/js/array.js#L710)

```
function InnerArraySort(array, length, comparefn) {
  // In-place QuickSort algorithm.
  // For short (length <= 10) arrays, insertion sort is used for efficiency.
  
     if (to - from <= 10) {
        InsertionSort(a, from, to);
        return;
      }
  
  	....
  }
```

翻译一下 当length <= 10是插入排序，当length >22是快速排序



## service worker

[https://juejin.im/post/6844903781306482695](https://juejin.im/post/6844903781306482695)

pwa渐进式web应用



sw便是在web worker的基础上增加了离线缓存的能力



sw是由事件驱动的,具有生命周期



可以拦截处理页面的所有网络请求(fetch)，可以访问cache和indexDB，支持推送，并且可以让开发者自己控制管理缓存的内容以及版本，为离线弱网环境下的 web 的运行提供了可能，让 web 在体验上更加贴近 native。





## 函数声明和函数表达式 (函数提升)

[https://blog.csdn.net/sky1679/article/details/88897205](https://blog.csdn.net/sky1679/article/details/88897205)


* function max(){}表示函数声明，可以放在代码的任何位置，也可以在任何地方成功调用；
* var max  = function(){};表示函数表达式，即将一个匿名函数赋值给一个变量，实现通过变量来调用这个匿名函数，但它需要在声明过后才能进行调用，如果调用在声明之前就会报如上红色字体的错误。而这在函数声明中不会出现这样的错误。

---



```js
a();
function a() {
	console.log(1);
}
```

**函数a是函数声明**，执行的是**函数提升**，实际效果是

```js
function a() {
	console.log(1);
}
a();
```





```js
b();
var b = function() {}
```

**而函数b是函数表达式**，执行的是**变量提升**，实际效果是

```js
var b;
b();
b = function() {}
```

**由于b已经声明了，所以不会报错ReferenceError，而是TypeError。**



----

**函数会首先被提升，其次才是变量**。

```js
foo();
var foo;
function foo() {
    console.log(1);
}    
var foo = function() {
    console.log(2);
};

// 输出 1
```

var foo尽管出现在function foo() {}的声明之前，但是函数声明会被提升到普通变量之前。重复的var声明被忽略。

----

**但是出现在后面函数声明却可以覆盖前面的函数声明**

```js
foo();  //2
function foo() {
    console.log(1);
}
function foo() {
    console.log(2);
}    
```











# ES6相关





## 箭头函数和普通函数的区别



* **箭头函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。**

* 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。

* 不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

* 不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数。



**注意： `this`对象的指向是可变的，但是在箭头函数中，它是固定的。**







举一个例子：



```javascript
var id = 21;

function foo() {
    // setTimeout 是在window全局环境下执行的
    setTimeout(function(){
        console.log('id:', this.id);
      	// 输出 21
    }, 100);
}


foo.call({ id: 42 });
```

  

 

```javascript
var id = 21;

function foo() {
    setTimeout(()=>{
        console.log('id:', this.id);
        // 输出 42 
    }, 100);
}


foo.call({ id: 42 });
```





**箭头函数可以让`setTimeout`里面的`this`，绑定定义时所在的作用域，而不是指向运行时所在的作用域。**





-----



科普：

```
setTimeout(fn,delay) { fn(); }

fn的调用者是window对象，所以this指向了window
```



---



所以，箭头函数转成 ES5 的代码如下



```javascript
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}

```





上面代码中，转换后的 ES5 版本清楚地说明了，箭头函数里面根本没有自己的`this`，而是引用外层的`this`。







## commonjs 与 esm 的区别

* commonjs输出拷贝
* esm输出引用

- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

 **esm 的 import read-only 特性**

read-only 的特性很好理解，import 的属性是只读的，不能赋值，类似于 const 的特性，这里就不举例解释了。

**esm 存在 export/import 提升**

esm 对于 import/export 存在提升的特性，具体表现是规范规定 import/export 必须位于模块顶级，不能位于作用域内；其次对于模块内的 import/export 会提升到模块顶部，**这是在编译阶段完成的**。



esm 的 import/export 提升在正常情况下，使用起来跟 commonjs 没有区别，因为一般情况下，我们在引入模块的时候，都会在模块的同步代码执行完才获取到输出值。所以即使存在提升，也无法感知。















## commonjs vs commonjs2

https://blog.csdn.net/qq8427003/article/details/64921642



* commonjs 规范只定义了exports
* commonjs2 规范存在 exports 和 module.exports

module.exports 是nodejs对commonjs的具体实现。exports 只是它的一个别名。





## Promise相关



### async await 和 promise 的关系

async await 是 promise 和 generator 函数组合的一个语法糖





### 一句话概述下什么是 promise

The Promise object is used for asynchronous computations. A Promise represents a single asynchronous operation that hasn't completed yet, but is expected in the future.


>译文：Promise对象用于异步操作，它表示一个尚未完成且预计在未来完成的异步操作。



### 实现一个Promise

[https://github.com/LaamGinghong/Promise-A-Plus/blob/master/src/promise.js](https://github.com/LaamGinghong/Promise-A-Plus/blob/master/src/promise.js)



### 实现 Promise.all 方法


在实现 Promise.all 方法之前，我们首先要知道 Promise.all 的功能和特点，因为在清楚了 Promise.all 功能和特点的情况下，我们才能进一步去写实现。


Promise.all(iterable) 返回一个新的 Promise 实例。此实例在 iterable 参数内所有的 promise 都 fulfilled 或者参数中不包含 promise 时，状态变成 fulfilled；如果参数中 promise 有一个失败rejected，此实例回调失败，失败原因的是第一个失败 promise 的返回结果。

```javascript
let p = Promise.all([p1, p2, p3]);
```


p的状态由 p1,p2,p3决定，分成以下；两种情况：


1. 只有p1、p2、p3的状态都变成 fulfilled，p的状态才会变成 fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
2. 只要p1、p2、p3之中有一个被 rejected，p的状态就变成 rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。




**Promise.all 的返回值是一个 promise 实例**


* 如果传入的参数为空的可迭代对象，Promise.all 会 同步 返回一个已完成状态的 promise
* 如果传入的参数中不包含任何 promise,Promise.all 会 异步 返回一个已完成状态的 promise
* 其它情况下，Promise.all 返回一个 处理中（pending） 状态的 promise.



```javascript
Promise.myAll = function(...args){
     const result = [];
     let isFail = false;
     let errInfo;
     let j = args.length;
     for (let i = 0; i < args.length; i++){
         // 如果有错误就停止循环 
         if(isFail) {
            return Promise.reject(errInfo);
         };
         // 挨个运行promise
         args[i].then(res => {
            // 处理结果
            result.push(res); 
            j--;
            if(j === 0){
                return Promise.resolve(result);
            }
         }).catch(err => {
            // 设置停止循环的标志，存储错误信息
            isFail = true;
            errInfo = err;
         })
     }
}
// 以上都是未经运行未经测试的伪代码，可能有问题，大致思路就这样~

```





### Promise.all出现错误不停止

Promise.all中任何一个Promise出现错误的时候都会执行reject，导致其他正常返回的数据也无法使用，如何解决？





* 把单个promise中的reject操作换成resolve(new Error("xxx"))
* 在单个promise函数执行时catch错误
* 引入Promise.allSettled
* 第三方库promise-transaction







```js
// 在单个promise函数执行时catch错误

  function fetch1() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(111)
      }, 1000)
    })
  }


  function fetch2() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("fetch2")
      }, 2000)
    })
  }


  function fetch3() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("fetch3")
      }, 2000)
    })
  }

  let arr = [fetch1().catch(e => {
    console.log("我在这里捕获了错误", e)
    debugger
  }),
  fetch2(),
  fetch3()
  ]
  Promise.all(arr).then(res => {
    console.log(res)
    for (let item of res) {
      console.log(item)
    }
  }).catch(e => {
    console.log("那么这里就无法捕获错误")
    debugger
    console.log("e", e)
  })

```











## const本质

**const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动**

对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。


但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。





## symbol实现原理

```js
  var mySymbol = function () { }
  var Symbol1 = Object.create(mySymbol.prototype)
  var Symbol2 = Object.create(mySymbol.prototype)

  console.log(Symbol1 == Symbol2) // false 
```

![](https://s3.ax1x.com/2020/12/24/rcqbi8.jpg)



```js
第二种方法实现：

(function () {
    var root = this
    var SymbolPolyfill = function Symbol(description) {

      if (this instanceof SymbolPloyfill) {
        throw new TypeError('Symbol is not a constructor');
      }

      var descString = description === undefined ? undefined : String(description)
      var symbol = Object.create(null)
      Object.defineProperties(symbol, {
        '__Description__': {
          value: descString,
          writable: false,
          enumerable: false,
          configurable: false
        }

      })

      return symbol
    }

    root.SymbolPolyfill = SymbolPolyfill;
  })()
```







补充知识：

 **Symbol 函数前不能使用 new 命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。**

**instanceof 的结果为 false**

```js
var s = Symbol('foo');
console.log(s instanceof Symbol); // false
```

**Symbol 函数的参数只是表示对当前 Symbol 值的描述，相同参数的 Symbol 函数的返回值是不相等的。**

```js
// 没有参数的情况
var s1 = Symbol();
var s2 = Symbol();

console.log(s1 === s2); // false

// 有参数的情况
var s1 = Symbol('foo');
var s2 = Symbol('foo');

console.log(s1 === s2); // false
```









# ts相关

> 对于大小厂而言，他们对 ts 的掌握程度要求是不一样的，对于小厂而言，他们希望来面试的人能够熟练使用 ts 进行业务开发，所以 ts 的概念的熟练程度对他们来说最重要。而对于大厂而言，他们可能更加希望来面试的人能利用 ts 开发一些 ts 的周边工具，所以比较少会直接问 ts 的一些概念，一般都会让你手写一个 ts 的工具函数。



### 对ts的理解

ts是js的超集。ts一方面是对js加上了很多条条框框的限制，另一方面是拓展了js的一些能力，就像es6提供了那么多神奇的语法糖一样。只要按照一定的规则去书写js，就能享受到ts带来的好处。



- 清晰的函数参数/接口属性，增加了代码可读性和可维护性
- 静态检查
- 生成API文档
- 配合现代编辑器，各种提示



### ts的泛型

泛型决定了一个类型在不同的场景下能够在每个场景下从始至终的保持类型一致



对于泛型，我是这样理解的，编写一个方法，让方法可以传入任意的参数，但是参数与参数，参数与结果之间存在一定的约束，以保证传入某个类型的参数就能得到确定类型的返回值，或者保证了我们传入值的正确性



```typescript
function find<T>(items: T[], callback: (item: T, index: number) => boolean): T | undefined {
  for (let i = 0, length = items.length; i < length; i++) {
    if (callback(items[i], i)) {
      return items[i]
    }
  }
}

const items = [{ a: 1 }, { a: 2 }, { a: 4 }, null ]
const result = find(items, (item, index) => item.a === 2)
```



```
首先<T>是给函数声明了一个类型变量T，后面要求items是一个T类型的数组，然后后面的callback函数的参数item是一个T类型的变量，index为数字，然后callback返回boolean类型结果，整个find函数返回T类型结果或者undefined
```

**如上，我们就能准确定义函数的每一个参数了，参数与参数，参数与返回结果之间就形成了约束关系**





### **type 和 interface 的区别**



**type 只是一个类型别名，并不会产生类型**。



> - An interface can be named in an extends or implements clause, but a type alias for an object type literal cannot.
> - An interface can have multiple merged declarations, but a type alias for an object type literal cannot.



**type 关键字的产生的东西官方有一个名字 type aliases ，就是类型别名，重点是它是别名不是真正的类型**



**interface是接口，type是类型，本身就是两个概念。只是碰巧表现上比较相似。**
**希望定义一个变量类型，就用type，如果希望是能够继承并约束的，就用interface。**



相同点：

*  都可以描述一个对象或者函数 
*  都允许拓展（extends） 
   *  interface extends interface (接口继承接口)  
   *  type extends type (类型继承类型) 
   *  interface extends type (接口继承类型)  
   *  type extends interface (类型继承接口) 





interface

```tsx
interface Point {
  x: number;
  y: number;
}

interface SetPoint {
  (x: number, y: number): void;
}

```



type 

```tsx
type Point = {
  x: number;
  y: number;
};

type SetPoint = (x: number, y: number) => void;

```



**与接口类型不一样，类型别名可以用于一些其他类型，比如原始类型、联合类型和元组：**

```tsx
// primitive
type Name = string;

// object！
type PartialPointX = { x: number; };
type PartialPointY = { y: number; };

// union
type PartialPoint = PartialPointX | PartialPointY;

// tuple
type Data = [number, string];

```





作者：七月流萤
链接：https://juejin.im/post/6844903749501059085
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### **ts 中如何实现一个函数的重载**

[https://www.zhihu.com/question/63751258](https://www.zhihu.com/question/63751258)



首先我们要区分override（重写）和overload（重载）的区别：



override（重写）：

1. 方法名、参数、返回值不同。

2. 子类方法不能缩小父类方法的访问权限。

3. 子类方法不能抛出比父类方法更多的异常（但子类方法可以不抛异常）

4. 存在于父类和子类之间。

5. 方法被定义为final时不能被重写。

overload（重载）：

1. 参数类型、个数、顺序至少一种不相同。

2. 不能重载只有返回值不同的方法名。

3. 存在于父类和子类、同类中。



举个例子：

```ts
function pickCark(x: any): any {
  let pickCard = null
  if (typeof x == 'object') {
    pickCard = Math.floor(Math.random() * x.length);
  } else if (typeof x == 'number') {
    pickCard = Math.floor(x / 13);
  }
  return pickCard
}
```



# JSON 相关





## 一道JSON.stringify输出题

```js
const obj = {
  a: 3,
  b: 4,
  c: null,
  d: undefined,
  get e () {}
}

console.log(JSON.stringify(obj))
```

输出：

```js
{"a":3,"b":4,"c":null}
```

对其中的 `undefined`，`function` 将在 `JSON.stringify` 时会忽略掉





# 其它



## 一道关于异步任务队列的面试题


实现下面这道题中的machine函数

```javascript
function machine() {
    
}


machine('ygy').execute() 
// start ygy
machine('ygy').do('eat').execute(); 
// start ygy
// ygy eat
machine('ygy').wait(5).do('eat').execute();
// start ygy
// wait 5s（这里等待了5s）
// ygy eat
machine('ygy').waitFirst(5).do('eat').execute();
// wait 5s
// start ygy
// ygy eat
```


分析：链式调用，返回this，wait异步任务，需要维护一个任务队列，waitFirst可以插入到任务队列头部，execute依次执行所有任务



```javascript
    class Action {
        constructor(name) {
            this.queue = []
            this.name = name
            this.queue.push(new QueueItem(0, () => console.log(`start ${name}`)))
        }

        do(action) {
            this.queue.push(new QueueItem(0, () => console.log(`${this.name} ${action}`)))
            return this
        }

        wait(time) {
            this.queue.push(new QueueItem(time, () => console.log(`wait ${time}s`)))
            return this
        }

        waitFirst(time) {
            this.queue.unshift(new QueueItem(time, () => console.log(`wait ${time}s`)))
            return this
        }


        async execute() {
            while (this.queue.length > 0) {
                const curItem = this.queue.shift()
                if (!curItem.defer) {
                    curItem.callback()
                    continue
                }
                await this.defer(curItem.defer, curItem.callback)
            }
        }

        defer(time, callback) {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    callback()
                    resolve(true)
                }, time * 1000)
            })
        }
    }


    class QueueItem {
        constructor(defer, callback) {
            this.defer = defer
            this.callback = callback
        }
    }


    function machine(name) {
        return new Action(name)
    }

```

作者：尹光耀
链接：https://juejin.im/post/5c8f30606fb9a070ef60996d
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



## 一道异步输出的题目

```js
  setTimeout(function () {
    console.log(1)
  }, 0);

  new Promise(function executor(resolve) {
    console.log(2);
    for (var i = 0; i < 10000; i++) {
      i == 9999 && resolve();
    }
    console.log(3);
  }).then(function () {
    console.log(4);
  });

  console.log(5);
  
  // 2 3 5 4 1 
```





## 一道关于轮询的面试题



```javascript
  function poll(checkStatus,callback){
        // 轮询调用checkStatus，当checkStatus为true时，执行callback
        // 如果checkStatus为false，则等待上次时间的1.5倍；第一次等待为1s
}

如： 等待1s，调用checkStatus返回false
等待1.5s 调用checkStatus返回false
等待2.25s 调用checkStatus返回true，调用callback
```







解决：



```javascript
 function timeOut(timer) {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    console.log(timer, " 延时执行")
                    resolve(true)
                }, timer)
            })
        }



        async function poll(checkStatus, callback) {
            let timer = 1000
            let flag = true
            while (flag) {
                await timeOut(timer)
                if (checkStatus()) {
                    callback()
                    flag = false
                } else {
                    timer = timer * 1.5
                }
            }
        }

        function checkStatus() {
            return Math.random() > 0.7
        }

        function callback() {
            console.log("callbackcallback")
        }



        poll(checkStatus, callback)
```















## 找到数组中的重复元素



给定两个排好序的数组A,B,大小分别为n,m。给出一个高效算法查找A中的哪些元素存在B数组中

A = [1,2,2,3,4,6,7,7,7,18,22]

B = [2,3,4,6,8,18,21,42]

答案 [2, 2, 3, 4, 6, 18]





解决：双指针





```javascript

let a = [1, 2, 2, 3, 4, 6, 7, 7, 7, 18, 22]
let b = [2, 3, 4, 6, 8, 18, 21, 42]

let aIndex = 0
let bIndex = 0
let curA = a[0]
let curB = b[0]
let res = []
while (aIndex < a.length) {
    if (curA < curB) {
        aIndex++
        curA = a[aIndex]
    } else if (curA === curB) {
        res.push(curA)
        aIndex++
        curA = a[aIndex]
    } else {
        bIndex++
        curB = b[bIndex]
    }
}

console.log(res)
```



## 请实现一个有难度的串行prommise 


要求：不管resolve还是reject都不可以终止串行的执行，要从头执行到尾。



```javascript
const p1 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('111');
    }, 1000)
  });
}
const p2 = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('222');
      }
      , 2000)
  });
}
const p3 = () => {
  return new Promise((resolve, reject) => {
    reject("333");
  });
}
const p4 = () => {
  return new Promise((resolve, reject) => {
    resolve("444");
  });
}
const p5 = () => {
  return new Promise((resolve, reject) => {
    resolve("555");
  });
}


async function f(...args) {
  const arr = args.slice()
  if (arr.length) {
    try {
      const fn = arr.shift()
      const res = await fn()
      console.log("res", res)
    } catch (e) {
      console.log("err", e)
      // ....
    } finally {
      f(...arr)
    }
  }
}


f(p1, p2, p3, p4, p5)

```




## ES6 与 ES5 继承的区别



* ES5的继承是通过prototype或构造函数机制来实现。ES5的继承实质上是**先创建子类的实例对象**，然后再将父类的方法添加到this上(Parent.apply(this))。
* ES6的继承机制实质上是**先创建父类的实例对象**this(所以必须先调用父类的super()方法)，然后再用子类的构造函数修改this。

具体的：ES6通过class关键字定义类，里面有构造方法，类之间通过extends关键字实现继承。子类必须在constructor方法中调用super方法，否则新建实例报错。因为子类没有自己的this对象，而是继承了父类的this对象，然后对其进行加工。如果不调用super方法，子类得不到this对象。

> super关键字指代父类的实例，即父类的this对象。在子类构造函数中，调用super后，才可使用this关键字，否则报错。





##  虚拟dom的优势

1. 优化了dom操作，提升渲染性能；

2. 可以做跨平台开发，如 react native(这个才是重点) 








## ECMAScript 和 JavaScript的关系

JavaScript是在浏览器执行的语言， 是因为它有BOM和DOM，前者提供了一些诸如window.open的函数， 后者提供了一些如document.querySelector的函数。 如果想让它在其他非浏览器平台运行的话， 肯定是不能有这两个的， 而JavaScript - BOM - DOM = ECMAScript。 比如Node平台， 内部的语言就是ECMAScript。 当然因为习惯， 即使我们在用Node的时候， 很多时候也是直接叫JavaScript而不是ES。


## typeof 运算符

* typeof undefined 结果 “undefined”
* typeof ‘abc’ 结果 “string”
* typeof 123 结果 “number”
* typeof true 结果 “boolean”
* typeof {} 结果 “object”
* typeof [] 结果 “object”
* **typeof null 结果 “object”**
* typeof console.log 结果 “function”

## == 运算符

100 == ‘100’ //true 
0 == ‘’ //true 
null == undefined //true

```
if(obj.a == null){ // 这里相当于obj.a === null || obj.a === undefined 的简写形式 // 这是jquery源码中的推荐写法 }
```

## 变量提升（Hoisting）

```
function test() {
   console.log(a);
   console.log(foo());
   
   var a = 1;
   function foo() {
      return 2;
   }
}
 
test();
```

这段代码的执行结果是undefined 和 2。

**变量和函数都被提升(hoisted) 到了函数体的顶部**
上面的代码等同于下面的代码：

```
function test() {
   var a;
   function foo() {
      return 2;
   }
 
   console.log(a);
   console.log(foo());
   
   a = 1;
}
 
test();
```





## 判断一个变量会被当作true还是false

```
var a = 100 
console.log(!!a)
```

## 如何准确判断一个变量是数组类型

```
var arr = [];
console.log(arr instanceof Array); //true
console.log(typeof arr);//object,typeof是无法判断是否是数组的
```

## 语法糖

* var a = {} 其实是 var a = new Object() 的语法糖
* var a = [] 其实是 var a = new Array() 的语法糖
* function Foo(){…} 其实是 var Foo = new Function(…)

>所有的引用类型（数组、对象、函数），都具有对象特性，即可自由扩展属性（除了“null”以外）




## DOM节点的Attribute和property有何区别

* property 只是一个JS对象的属性的修改
* Attribute 是对html标签属性的修改


## location

```
console.log(location.href); //http://localhost:63342/untitled2/test.html?_ijt=qgpv4jmr3aio5urdkukkjtts47
console.log(location.protocol); //http:
console.log(location.pathname); ///test/abc.html(本地位置)
console.log(location.search); //?_ijt=qgpv4jmr3aio5urdkukkjtts47
console.log(location.hash);
```

**hash 属性是一个可读可写的字符串，该字符串是 URL 的锚部分（从 # 号开始的部分）。**





## 继承









### ES5的继承和ES6的继承有什么区别？

ES5的继承时通过prototype或构造函数机制来实现。ES5的继承实质上是先创建子类的实例对象，然后再将父类的方法添加到this上（Parent.apply(this)）。


ES6的继承机制完全不同，实质上是先创建父类的实例对象this（所以必须先调用父类的super()方法），然后再用子类的构造函数修改this。

具体的：ES6通过class关键字定义类，里面有构造方法，类之间通过extends关键字实现继承。子类必须在constructor方法中调用super方法，否则新建实例报错。因为子类没有自己的this对象，而是继承了父类的this对象，然后对其进行加工。如果不调用super方法，子类得不到this对象。

ps：super关键字指代父类的实例，即父类的this对象。在子类构造函数中，调用super后，才可使用this关键字，否则报错。




### instanceof和constructor

用到instanceof和constructor来检测某个对象是不是另一个对象的实例

```
var M = function(name){
    this.name = name
}
var o3 = new M("o3")
```

o3.\_\_proto__ == M.prototype
M.prototype.\_\_proto__ === Object.prototype

**所以o3 instanceof Object     //true**

nstanceof 可以在继承关系中用来判断一个实例是否属于它的父类型,**在多层继承关系中，instanceof 运算符同样适用**。

这样并不好，因为这样无法判断出是否是继承关系中的直接父类

所以我们可以使用 constructor

**o3.\_\_proto__.constructor === M  //true
o3.\_\_proto__.constructor === Object //false**



### **typeof 和 instanceof**

* typeof 是一个一元运算，放在一个运算数之前，运算数可以是任意类型。
* typeof 不能区分array和object
* **instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。**








### 借助构造函数实现继承

```
function Parent1(){
    this.name = 'parent1'
}

function Child1(){
    Parent1.call(this) //在此处改变上下文执行Parent1 this是Child1实例对象
    this.type = 'child1'
}

console.log(Child1())
```

**缺点：Parent1原型链上的东西并没有被Child1继承**


### 借助原型链实现继承

```
function Parent2(){
    this.name = 'parent2'
    this.play = [1,2,3]
}
function Child2(){
    this.type = 'child2'
}
Child2.prototype = new Parent2()
```


```
var s1 = new Child2()
var s2 = new Child2()
s1.play.push(4)
//s1.play  [4,1,2,3]
//s2.play  [4,1,3,4]
```

**缺点：子类共用原型链中的原型对象**

### 借助组合方式实现继承

```
function Parent3(){
    this.name = 'parent3'
    this.play = [1,2,3]
}
function Child3(){
    Parent3.call(this)
    this.type = 'child3'
}
Child3.prototype = new Parent3()
```

**缺点：父类的构造函数执行了两次**


组合继承优化1：

```
function Parent4(){
    this.name = 'parent4'
    this.play = [1,2,3]
}
function Child4(){
    Parent4.call(this)
    this.type = 'child4'
}
Child4.prototype = Parent4.prototype
```

```
var s5 = new Child4()
var s6 = new Child4()
console.log(s5 instanceof Child4,s5 instanceof Parent4)
//true true
```

**缺点：无法区分对象是由子类直接实例化还是父类直接实例化**


组合继承优化2：

```
function Parent5(){
    this.name = 'parent5'
    this.play = [1,2,3]
}
function Child5(){
    Parent4.call(this)
    this.type = 'child5'
}
Child5.prototype = Object.create(Parent5.prototype)
Child5.prototype.constructor = Child5
```

**牛逼啊！！！**







## 异步串行题



[https://juejin.im/post/6860646761392930830?utm_source=gold_browser_extension](https://juejin.im/post/6860646761392930830?utm_source=gold_browser_extension)



先看题目：

```js
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

const subFlow = createFlow([() => delay(1000).then(() => log("c"))]);

createFlow([
  () => log("a"),
  () => log("b"),
  subFlow,
  [() => delay(1000).then(() => log("d")), () => log("e")],
]).run(() => {
  console.log("done");
});

// 需要按照 a,b,延迟1秒,c,延迟1秒,d,e, done 的顺序打印

```

答案：



```js

function createFlow(effects = []) {
  // 拍平数组
  let sources = effects.slice().flat();

  function run(cb) {
    while (sources.length) {
      const task = sources.shift();
      //  next 把 cb 放到下一个flow的run中执行
      const next = () => createFlow(sources).run(cb);
      if (typeof task == "function") {
        const res = task();
        if ( res?.then) {
          // task 函数返回一个promise
          res.then(next);
          return;
        }
      } else if (task.isFlow) {
        task.run(next);
        return;
      }
    }
   cb?.()
    
  }

  return {
    run,
    isFlow: true,
  };
}
```



await解决方案：

```js
function createFlow(effects = []) {
  // 拍平数组
  let sources = effects.slice().flat();

  async function run(cb) {
    while (sources.length) {
      const task = sources.shift();
      typeof task == "function" 
      ? await task() // 是函数
      : await task.run() // 是createFlow
    }
   cb?.()
  }
  return {
    run,
  };
}

```







## var声明的全局变量会挂载到window

```js
let num = 2;
console.log(window.num) // 2
```



```js
let num1 = 2;
let num2 = 2;
console.log(window.num1) //undefined
console.log(window.num2) //undefined
```



## 各种值的相加

[https://juejin.im/post/6866920515420815374?utm_source=gold_browser_extension#heading-10](https://juejin.im/post/6866920515420815374?utm_source=gold_browser_extension#heading-10)



```js
console.log([] + [])  // ""
console.log({} + []) // [object Object]
console.log([] == ![]) // true
console.log(true + false) // 1
```



**包装类型在运算的时候，会先调用`valueOf`方法，如果`valueOf`返回的还是包装类型，那么再调用`toString`方法**



```js
// 还是 数组
const val = [].valueOf()
// 数组 toString 默认会将数组各项使用逗号 "," 隔开, 比如 [1,2,3].toSting 变成了"1,2,3",空数组 toString 就是空字符串
const val1 = val.toString() // val1 是空字符串


console.log([] + [])  // ""
// 面的代码相当于
console.log("" + "")

```



-----



```js
console.log({} + [])

// 对象 {}隐氏转换成了[object Object],然后与""相加
```



-------



```js
console.log([] == ![])
```

对于`===`, 会严格比较两者的值，但是对于`==`就不一样了

1. 比如 `null == undefined`
2. 如果非`number`与`number`比较，会将其转换为`number`
3. 如果比较的双方中由一方是`boolean`,那么会先将`boolean`转换为`number`

```js
// 这个输出 false
console.log(![])
// 套用上面第三条 将 false 转换为 数值
// 这个输出 0
console.log(Number(false))
// 包装类型与 基本类型 == 先将包装类型通过 valueOf toString 转换为基本类型 
// 输出 ""
console.log([].toString())
// 套用第2条， 将空字符串转换为数值、
// 输出 0
console.log(Number(""))
// 所以
console.log(0 == 0)

```



-----



```js
console.log(true + false)
```

两个基本类型相加，如果其中一方是字符，则将其他的转换为字符相加，否则将类型转换为`Number`,然后相加, `Number(true)` 是`1`, `Number(false)`是`0`, 所以结果是 `1`

















## for..in,for..of 和 Object.keys的区别

for..in

* 遍历对象及其原型链上可枚举的属性
* 如果用于遍历数组，除了遍历其元素外，还会遍历开发者对数组对象自定义的可枚举属性及其原型链上的可枚举属性
* 遍历对象返回的属性名和遍历数组返回的索引都是string类型

> 不推荐在数组中使用for in遍历

```js
  Array.prototype.getLength = function () {
    return this.length
  }

  const arr = ['a', 'b', 'c']
  arr.name = 'yd'
  Object.defineProperty(arr, 'age', {
    enumerable: true,
    value: 5,
    writable: true,
    configurable: true
  })

  for(let item in arr){
    console.log(item) // 0 1 2 name age getLength
  }

  const len =  arr.getLength()
  console.log('len',len) // 3
```

----

for..of 

* 支持遍历数组，类数组对象(DOM NodeList)，字符串，Map对象，Set对象
* 不支持遍历普通对象
* 遍历后输出的结果为数组元素的值
* 不会遍历到**对象属性及其原型属性**

```js
  Array.prototype.getLength = function(){
    return this.length
  }

  const arr = ['a','b','c']
  arr.name = 'yd'
  Object.defineProperty(arr,'age',{
    enumerable:true,
    value:17,
    writable:true,
    configurable:true
  })

  for(let i of arr){
    console.log(i) // a b c 
  }
```





----

Object.keys

* 返回对象自身可枚举属性组成的数组
* 不会遍历对象原型链上的属性以及Symbol属性



```js
  function Company() {
    this.name = 'yd'
  }

  Company.prototype.getName = function () {
    return this.name
  }
  const yd = new Company()
  Object.defineProperty(yd, 'age', {
    enumerable: true,
    value: 5,
    writable: true,
    configurable: true
  })
  Object.defineProperty(yd, 'address', {
    enumerable: false,
    calue: '北京',
    writable: true,
    configurable: true
  })

  console.log(Object.keys(yd)) // ["name", "age"]
```





## [javascript函数自执行里的this为什么指向window](https://segmentfault.com/q/1010000004648772)

[javascript函数自执行里的this为什么指向window](https://segmentfault.com/q/1010000004648772)



当你要确定“函数中的this是什么”的时候，永远不要到函数**定义的地方**去找答案！而是要到**函数被调用**的地方找答案！



具体说：函数里面的`this`的含义，是由它被调用的方式决定的。



换句话说，当你看到下面的代码时：

```
a.f();
```

你就可以确定f里面的`this`就是指`a`，而不管`f`是个什么玩意。当你看到下面的代码时：

```
f();
```

你就可以确定(不考虑`bind`，以及严格模式时情况下)，`f`里面的`this`就是指全局对象`window`。





## requestAnimationFrame在EventLoop的执行位置

[https://github.com/ginobilee/blog/issues/6](https://github.com/ginobilee/blog/issues/6)

[深入探究 eventloop 与浏览器渲染的时序问题](https://www.404forest.com/2017/07/18/how-javascript-actually-works-eventloop-and-uirendering/#4-requestAnimationFrame-callback-%E7%9A%84%E6%89%A7%E8%A1%8C%E6%97%B6%E6%9C%BA)

> requestAnimationFrame 会在UI渲染之前

1. 当开始执行它的回调时，在此刻之前注册的所有该类回调，会一次性执行完(一个loop内，这点很关键)
2. 每个该类任务执行完，也会执行微任务(其实不能称为特性，毕竟所有脚本任务都是这样)
3. 如果以自身递归调用的方式(raf回调内递归调用raf，使用该api的正确姿势)，它的触发时机总是与浏览器的渲染频率保持一致。



**特别指出，在一个loop中，可能并不会执行这一步，只要浏览器任务不需要执行渲染，就会跳过。**



结论:

我们看到宏任务与raf任务有明显的差异:

1. 执行时机不同
2. raf任务队列被执行时，会将其此刻队列中所有任务都执行完

所以raf任务不属于宏任务。而由于微任务的特殊性(单独的任务队列)，它显然更不是微任务。

所以它既不是宏任务，又不是微任务。那么它是什么？

**任务。**



![img](https://pic1.zhimg.com/80/v2-4afcb7df8ddbb488046360ea4920ef4c_720w.jpg)

在一些文章中将 requestAnimationFrame 划分为 task，理由是假如你在 requestAnimationFrame 的 callback 中注册了 microtask 任务，你会发现该 microtask 任务会在 requestAnimationFrame 的 callback 结束后立刻执行。

```js
setTimeout(() => {
  console.log('A')
}, 0)
requestAnimationFrame(() => {
  console.log('B')
  Promise.resolve().then(() => {
    console.log('C')
  })
})
```

```
B
C
A
// 有可能出现 A B C 的情况，本节讨论的重点在于 B C 一定紧挨着输出
```



![eventloop-2](https://www.404forest.com/imgs/blog/eventloop-2.jpg)



可以看到 requestAnimationFrame 中注册的 microtask 并没有在下一轮 eventloop 的 task 之后执行，而是直接在本轮 eventloop 中紧跟着 requestAnimationFrame 执行了。



深入 requestAnimationFrame 的执行过程也能发现：[在执行 animation frame callbacks 时，会唤起 callback（invoke the callback）](https://html.spec.whatwg.org/multipage/imagebitmap-and-animations.html#animation-frames)，[在唤起 callback 的最后一步，会 clean up after running a callback](https://heycam.github.io/webidl/#invoke-a-callback-function)，此时若满足 javascript 执行栈为空的条件，则执行 microtask。



总结

1. 每个 eventloop 由三个阶段构成：执行一个 task，执行 microtask 队列，可选的 ui render 阶段，requestAnimationFrame callback 在 render 阶段执行。我们平时写的逻辑代码会被分类为不同的 task 和 microtask。
2. microtask 中注册的 microtask 事件会直接加入到当前 microtask 队列。
3. microtask 执行时机『尽可能早』，只要 javascript 执行栈为空，就会执行。一轮 eventloop 中，可能执行多次 microtask。
4. requestAnimationFrame callback 的执行时机与浏览器的 render 策略有关，是黑箱的。





## async和await 的es5实现

[https://www.zhihu.com/question/39571954](https://www.zhihu.com/question/39571954)





## 一道Object.defineProperty 输出题目

> 来源： 微信小程序 前端面试星球 Day215

```js
  const person = {
    name: "yd"
  }
  Object.defineProperty(person, 'age', {
    value: 21
  })
  person.age = 18

  console.log(person) // {name: "yd", age: 21}
  console.log(Object.keys(person))  // ['name']
```

使用Object.defineProperty方法添加的属性默认不可枚举 （not enumerable）。用defineProperty方法添加的属性默认不可变。可以通过writable, configurable 和 enumerable 属性改变这一行为。

```js
var obj = {};
obj.name = "tn";

//相当于
Object.defineProperty(obj, "name", {
  value: "tn",
  writable: true,
  configurable: true,
  enumerable: true,
});
```

```js
Object.defineProperty(obj, "name", { value: "tn" });

//相当于
Object.defineProperty(obj, "name", {
  value: "tn",
  writable: false,
  configurable: false,
  enumerable: false,
});
```



## 表单可以跨域吗

[https://github.com/frontend9/fe9-interview/issues/1](https://github.com/frontend9/fe9-interview/issues/1)

> 表单提交是可以进行跨域的，不受浏览器的同源策略限制，估计是历史遗留原因，也有可能是表单提交的结果js是拿不到的，所以不限制问题也不大。但是存在一个问题，就是csrf攻击，具体不展开了，因为可以自动带上cookie造成攻击成功，而cookie的新属性SameSite就能用来限制这种情况

因为原页面用 form 提交到另一个域名之后，原页面的脚本无法获取新页面中的内容。

所以浏览器认为这是安全的。。



**浏览器为什么禁止ajax跨域，又为什么允许form表单来跨域?**

限制ajax跨域的原因在于ajax网络请求是可以携带cookie的（通过设置withCredentials为true），比如用户打开了浏览器，登录了weibo.com，然后又打开了百度首页，这时候百度首页内的js，向weibo.com用withCredentials为true的ajax方式提交一个post请求，是会携带浏览器和weibo.com之间的cookie的，所以浏览器就默认禁止了ajax跨域，服务端必须设置CORS才可以。



**form表单提交会不会携带cookie?**

会的。

假设在a.com下,表单提交到baidu.com, 请求会带上baidu.com的cookie。 但不会带上a.com的cookie

```html
<form action="baidu.com"></form>
```

我认为的原因是, **同源策略主要是限制js行为,form表单提交的结果js是无法拿到,所以没有去限制.**

csrf攻击就能利用form表单能带cookie的特点. 而cookie的新属性SameSite就能用来限制这种情况





## js中如何实现函数重载

[https://segmentfault.com/a/1190000016193719](https://segmentfault.com/a/1190000016193719)

利用arguments和switch实现重载

......



利用arguments和闭包实现重载

```js
function addMethod (object, name, fn) {
  // 把前一次添加的方法存在一个临时变量old中
  var old = object[name];

  // 重写object[name]方法
  object[name] = function () {
    if (fn.length === arguments.length) {
      // 如果调用object[name]方法时，如果实参和形参个数一致，则直接调用
      return fn.apply(this, arguments);
    } else if (typeof old === 'function') {
      // 如果实参形参不一致，判断old是否是函数，如果是，就调用old
      return old.apply(this, arguments);
    }
  };
}

addMethod(people, 'find', function() {
  return this.values;
});

addMethod(people, 'find', function(firstName) {
  return this.values.filter((value) => {
    return value.indexOf(firstName) !== -1 ? true : false;
  });
});

addMethod(people, 'find', function(firstName, lastName) {
  return this.values.filter((value) => {
    var fullName = `${firstName} ${lastName}`;
    return value.indexOf(fullName) !== -1 ? true : false;
  });
});

console.log(people.find());                     // ["Dean Edwards", "Sam Stephenson", "Alex Russell", "Dean Tom"]
console.log(people.find('Dean'));               // ["Dean Edwards", "Dean Tom"]
console.log(people.find('Dean', 'Edwards'));    // ["Dean Edwards"]
```





JavaScript可以实现函数重载，主要有两种思想：

1. 利用arguments类数组来判断接收参数的个数
2. 利用闭包保存以前注册进来的同名函数