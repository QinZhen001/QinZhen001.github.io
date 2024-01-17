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

# 正文

[网页链接](http://es6.ruanyifeng.com/#docs/decorator#%E7%B1%BB%E7%9A%84%E4%BF%AE%E9%A5%B0)

[Exploring EcmaScript Decorators](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841)

[https://www.typescriptlang.org/docs/handbook/decorators.html#decorators](https://www.typescriptlang.org/docs/handbook/decorators.html#decorators)



装饰器对类的行为的改变，是代码**编译**时发生的，而不是在运行时。这意味着，装饰器能在编译阶段运行代码。也就是说，装饰器本质就是编译时执行的函数。

- 使用简单，易于理解
- 在不改变原有代码情况下，扩展类属性和类方法
- 是一个编译时执行的函数





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

```ts
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
```ts
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
```ts
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
```ts
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
```ts
class Person {
  @readonly
  name() { return `${this.first} ${this.last}` }
}
```
上面代码中，修饰器readonly用来修饰“类”的name方法。

修饰器函数readonly一共可以接受三个参数。
```tsx
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
```ts
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
```ts
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
```ts
class MyReactComponent extends React.Component {}

export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);
```
有了装饰器，就可以改写上面的代码。
```ts
@connect(mapStateToProps, mapDispatchToProps)
export default class MyReactComponent extends React.Component {}
```
相对来说，后一种写法看上去更容易理解。

**但是要记得安装配置**
1. npm install babel-plugin-transform-decorators-legacy --save-dev
2. "plugins": ["transform-decorators-legacy"]




### 装饰器不能用于函数
修饰器只能用于类和类的方法，不能用于函数，因为存在函数提升。
```ts
var counter = 0;

var add = function () {
  counter++;
};

@add
function foo() {
}
```
上面的代码，意图是执行后counter等于 1，但是实际上结果是counter等于 0。因为函数提升，使得实际执行的代码是下面这样。
```ts
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
```ts
var readOnly = require("some-decorator");

@readOnly
function foo() {
}
```
面代码也有问题，因为实际执行是下面这样。
```ts
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



### 记录函数执行时间的装饰器

实现：

```tsx
const run = (target, name, descriptor) => {

  const originalMethod = descriptor.value; // 保存原始方法

  descriptor.value = function (...args) {
    let startTime = Date.now();
    // 调用原始方法 注意this指向
    const res = originalMethod.apply(this, args);
    if (res instanceof Promise) {
      res.then(() => {
        let endTime = Date.now();
        console.log('end', endTime - startTime);
      }).catch(() => {
        let endTime = Date.now();
        console.log('end', endTime - startTime);
      })
    } else {
      let endTime = Date.now();
      console.log('end', endTime - startTime);
    }

    return res
  };

  return descriptor
}
```

测试：

```tsx
class Test {

  @run
  async task() {
    await sleep()
    console.log('task')
  }
}

let t = new Test()
t.task()
```





### 装饰器和注解的区别

[https://zhuanlan.zhihu.com/p/22277764](https://zhuanlan.zhihu.com/p/22277764)

- 注解（Annotation）：仅提供附加元数据支持，并不能实现任何操作。需要另外的 Scanner 根据元数据执行相应操作。
- 装饰器（Decorator）：仅提供定义劫持，能够对类及其方法的定义并没有提供任何附加元数据的功能。

AtScript 语言中，@ 语法就是注解，用于添加对应的元数据；而在 TypeScript 语言中，@ 语法是装饰器，用于拦截类型的定义过程。

例如，对于同样的 AtScript 代码：

```ts
@myFun
class myClass {}
```

生成的结果（近似）为：

```ts
class myClass {}
myClass.annotations = [
  new myFun
]
```

*注：很容易看出，由于注解都会通过 new 实例化，因此括号存在与否不会产生影响。*

**（AtScript 的）注解能够完全被（ES next）装饰器在实现上模拟！**

所有 Angular 中使用的 Decorator 都并不是真正作为 Decorator 使用，只是通过 Decorator + Reflect.metadata 的组合来模拟 Annotation 的功能。即附加元数据走的是 Reflect.metadata，该实现和 Decorator 本身并无联系。





# 补充



## 原理

```tsx
function readonly(target, name, descriptor){
    descriptor.writable = false;
    return descriptor;
}

class Cat{
 @readonly
  meow(){
    return 'meow';
  }
}


// transform ...
// the engine first invokes the decorator:
let descriptor = {
  value:specifiedFunction,
  enumerable:false,
  configurable:true,
  writable:true
}

descriptor = readonly(Cat.prototype, 'meow', descriptor) || descriptor;
Object.defineProperty(Cat.prototype, 'meow', descriptor);

```







## vite 中使用装饰器 

[https://zhuanlan.zhihu.com/p/417263788](https://zhuanlan.zhihu.com/p/417263788)

```tsx
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react({
    babel: {
      plugins: [
        ["@babel/plugin-proposal-decorators", { legacy: true }],
        ["@babel/plugin-proposal-class-properties", { loose: true }],
      ],
    }
  })],
})
```



## parameter-decorators

```ts
class BugReport {

  print(@required verbose: boolean) {

  }
}
```

默认情况下不支持 parameter decorators 需要使用 babel 插件 

[https://www.npmjs.com/package/babel-plugin-parameter-decorator](https://www.npmjs.com/package/babel-plugin-parameter-decorator)



## 依赖注入

[https://angular.cn/guide/dependency-injection](https://angular.cn/guide/dependency-injection)

**将创建对象的任务转移给其他 class，并直接使用依赖项的过程，被称为“依赖项注入”。**（DI）

DI 系统中存在两个主要角色：依赖使用者和依赖提供者。

需要 用到 reflect-metadata 库



## async function

[typescript decorator async function](https://juejin.cn/s/typescript%20decorator%20async%20function)

```ts
function asyncDecorator(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  if (isAsyncFunction(originalMethod)) {
    descriptor.value = async function (...args: any[]) {
      console.log('Async operation started...');
      const result = await originalMethod.apply(this, args);
      console.log('Async operation completed.');
      return result;
    }
  } else {
    descriptor.value = function (...args: any[]) {
      // ...
    }
  }
}
```





## 控制反转

IOC（Inversion of Control， 控制反转）就是一个可以自动实例化具体类并且管理各对象之间关系的**容器**，有了这个自动化的容器，我们关注的就不是具体的关系，而是上升到只需关注抽象之间的关系，而且还可以省去手动实例化。
