---
layout:     post
title:      "装饰器(Decorator)"
date:       2018-03-11 19:39:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - ES7
---

> “Yeah It's on. ”


## 正文
[网页链接](http://es6.ruanyifeng.com/#docs/decorator#%E7%B1%BB%E7%9A%84%E4%BF%AE%E9%A5%B0)

### 类的修饰
许多面向对象的语言都有修饰器（Decorator）函数，用来修改类的行为。目前，有一个提案将这项功能，引入了 ECMAScript。
```
@testable
class MyTestableClass {
  // ...
}

function testable(target) {
  target.isTestable = true;
}

MyTestableClass.isTestable // true
```
上面代码中，@testable就是一个修饰器。它修改了MyTestableClass这个类的行为，为它加上了静态属性isTestable。testable函数的参数target是MyTestableClass类本身。

基本上，修饰器的行为就是下面这样。
```
@decorator
class A {}

// 等同于

class A {}
A = decorator(A) || A;
```

也就是说，修饰器是一个对类进行处理的函数。修饰器函数的第一个参数，就是所要修饰的目标类。

```
function testable(target) {
  // ...
}
```

上面代码中，testable函数的参数target，就是会被修饰的类。
如果觉得一个参数不够用，可以在修饰器外面再封装一层函数。

```
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  }
}

@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable // true

@testable(false)
class MyClass {}
MyClass.isTestable // false
```

上面代码中，修饰器testable可以接受参数，这就等于可以修改修饰器的行为。

注意，修饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，修饰器能在编译阶段运行代码。也就是说，修饰器本质就是编译时执行的函数。

前面的例子是为类添加一个静态属性，如果想添加实例属性，可以通过目标类的prototype对象操作。
```
function testable(target) {
  target.prototype.isTestable = true;
}

@testable
class MyTestableClass {}

let obj = new MyTestableClass();
obj.isTestable // true
```
上面代码中，修饰器函数testable是在目标类的prototype对象上添加属性，因此就可以在实例上调用。

下面是另外一个例子。
```
// mixins.js
export function mixins(...list) {
  return function (target) {
    Object.assign(target.prototype, ...list)
  }
}

// main.js
import { mixins } from './mixins'

const Foo = {
  foo() { console.log('foo') }
};

@mixins(Foo)
class MyClass {}

let obj = new MyClass();
obj.foo() // 'foo'
```
上面代码通过修饰器mixins，把Foo对象的方法添加到了MyClass的实例上面。可以用Object.assign()模拟这个功能。
```
const Foo = {
  foo() { console.log('foo') }
};

class MyClass {}

Object.assign(MyClass.prototype, Foo);

let obj = new MyClass();
obj.foo() // 'foo'
```


### 类方法的修饰
修饰器不仅可以修饰类，还可以修饰类的属性。
```
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}
```
上面代码中，修饰器readonly用来修饰“类”的name方法。

修饰器函数readonly一共可以接受三个参数。
```
function readonly(target, name, descriptor){
  // descriptor对象原来的值如下
  // {
  //   value: specifiedFunction,
  //   enumerable: false,
  //   configurable: true,
  //   writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}

readonly(Person.prototype, 'name', descriptor);
// 类似于
Object.defineProperty(Person.prototype, 'name', descriptor);
```
修饰器第一个参数是类的原型对象，上例是Person.prototype，修饰器的本意是要“修饰”类的实例，但是这个时候实例还没生成，所以只能去修饰原型（这不同于类的修饰，那种情况时target参数指的是类本身）；第二个参数是所要修饰的属性名，第三个参数是该属性的描述对象。

另外，上面代码说明，修饰器（readonly）会修改属性的描述对象（descriptor），然后被修改的描述对象再用来定义属性。

下面是另一个例子，修改属性描述对象的enumerable属性，使得该属性不可遍历。
```
class Person {
  @nonenumerable
  get kidCount() { return this.children.length; }
}

function nonenumerable(target, name, descriptor) {
  descriptor.enumerable = false;
  return descriptor;
}
```

如果同一个方法有多个修饰器，会像剥洋葱一样，先从外到内进入，然后由内向外执行。
```
function dec(id){
  console.log('evaluated', id);
  return (target, property, descriptor) => console.log('executed', id);
}

class Example {
    @dec(1)
    @dec(2)
    method(){}
}
// evaluated 1
// evaluated 2
// executed 2
// executed 1
```
上面代码中，外层修饰器@dec(1)先进入，但是内层修饰器@dec(2)先执行。

除了注释，修饰器还能用来类型检查。所以，对于类来说，这项功能相当有用。从长期来看，它将是 JavaScript 代码静态分析的重要工具。



### 应用在react的connect中
际开发中，React 与 Redux 库结合使用时，常常需要写成下面这样。
```
class MyReactComponent extends React.Component {}

export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);
```
有了装饰器，就可以改写上面的代码。
```
@connect(mapStateToProps, mapDispatchToProps)
export default class MyReactComponent extends React.Component {}
```
相对来说，后一种写法看上去更容易理解。

**但是要记得安装配置**
1. npm install babel-plugin-transform-decorators-legacy --save-dev
2. "plugins": ["transform-decorators-legacy"]




### 装饰器不能用于函数
修饰器只能用于类和类的方法，不能用于函数，因为存在函数提升。
```
var counter = 0;

var add = function () {
  counter++;
};

@add
function foo() {
}
```
上面的代码，意图是执行后counter等于 1，但是实际上结果是counter等于 0。因为函数提升，使得实际执行的代码是下面这样。
```
@add
function foo() {
}

var counter;
var add;

counter = 0;

add = function () {
  counter++;
};
```
下面是另一个例子。
```
var readOnly = require("some-decorator");

@readOnly
function foo() {
}
```
面代码也有问题，因为实际执行是下面这样。
```
var readOnly;

@readOnly
function foo() {
}

readOnly = require("some-decorator");
```

### 写一个bound

```tsx
const bound = (proto: any, propertyName: string, descriptor: PropertyDescriptor) => {
  return {
    get() {
      return descriptor.value.bind(this);
    },
  };
};
```

测试：

```tsx
class I18nTranslate {

  @bound
  _transI18n(text: string, options?: any) {
    let content = this._i18nInstance.t(text);
    // ...
    return content;
  }
}
```





