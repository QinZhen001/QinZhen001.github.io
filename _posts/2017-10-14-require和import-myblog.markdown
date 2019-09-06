---
layout:     post
title:      "require和import"
date:       2017-10-14 22:40:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”

## 前文

ES6标准发布后，module成为标准，标准的使用是以export指令导出接口，以import引入模块，但是在我们一贯的node模块中，我们采用的是CommonJS规范，使用require引入模块，使用module.exports导出接口。

## 正文
 
[网页链接](http://blog.csdn.net/qq_28702545/article/details/54892562)

### require时代的模块

node编程中最重要的思想之一就是模块，而正是这个思想，让JavaScript的大规模工程成为可能。模块化编程在js界流行，也是基于此，随后在浏览器端，requirejs和seajs之类的工具包也出现了，可以说在对应规范下，require统治了ES6之前的所有模块化编程，即使现在，在ES6 module被完全实现之前，还是这样。

node的module遵循CommonJS规范，requirejs遵循AMD，seajs遵循CMD，虽各有不同，但总之还是希望保持较为统一的代码风格。

```
// a.js

// -------- node -----------
module.exports = {
  a : function() {},
  b : 'xxx'
};

// ----------- AMD or CMD ----------------
define(function(require, exports, module){
  module.exports = {
    a : function() {},
    b : 'xxx'
  };
});
```
可以看出，为了保持风格的高度统一，除了在浏览器端的模块中要使用一个define函数来提供模块的闭包以外，其他代码可以完全一致。
```
// b.js

// ------------ node ---------
var m = require('./a');
m.a();

// ------------ AMD or CMD -------------
define(function(require, exports, module){
   var m = require('./a');
   m.a();
});
```
在使用上，也非常相似。虽然AMD or CMD提供了更加丰富的风格，但是我们本文主要是讨论node环境下，所以不做扩展。


### ES6中的module

ES6发布的module并没有直接采用CommonJS，甚至连require都没有采用，也就是说require仍然只是node的一个私有的全局方法，module.exports也只是node私有的一个全局变量属性，跟标准半毛钱关系都没有。

### export导出模块接口

```
// a.js
export default function() {}
export function a () {}

var b = 'xxx';
export {b}; // 这是ES6的写法，实际上就是{b:b}
setTimeout(() => b = 'ooo', 1000);
export var c = 100;
```
在要导出的接口前面，加入export指令。

在export之后，b还可以被修改，这和CommonJS有着巨大不同

```
// 错误演示
export 1; // 绝对不可以
var a = 100;
export a;
```
export在导出接口的时候，必须与模块内部的变量具有一一对应的关系。直接导出1没有任何意义，也不可能在import的时候有一个变量与之对应。 export a 虽然看上去成立，但是 a 的值是一个数字，根本无法完成解构，因此必须写成 export {a} 的形式。即使a被赋值为一个function，也是不允许的。而且，大部分风格都建议，模块中最好在末尾用一个export导出所有的接口，例如：

```
export {fun as default,a,b,c};
```

### import导入模块

import的语法跟require不同，而且import必须放在文件的最开始，且前面不允许有其他逻辑代码，这和其他所有编程语言风格一致。

import的使用和export一样，也挺复杂，可以在这里大致了解。举几个例子：
```
import $ from 'jquery';
import * as _ from '_';
import {a,b,c} from './a';
import {default as alias, a as a_a, b, c} from './a';
```

import后面跟上花括号的形式是最基本的用法，花括号里面的变量与export后面的变量一一对应。这里，你必须了解**对象的解构赋值**的知识

#### as关键字

编程的同学对as都容易理解，简单的说就是取一个别名。export中可以用，import中其实可以用：
```
// a.js
var a = function() {};
export {a as fun};

// b.js
import {fun as a} from './a';
a();
```

上面这段代码，export的时候，对外提供的接口是fun，它是a.js内部a这个函数的别名，但是在模块外面，认不到a，只能认到fun。

import中的as就很简单，就是你在使用模块里面的方法的时候，给这个方法取一个别名，好在当前的文件里面使用。之所以是这样，是因为有的时候不同的两个模块可能通过相同的接口，比如有一个c.js也通过了fun这个接口：
```
// c.js
export function fun() {};
```
如果在b.js中同时使用a和c这两个模块，就必须想办法解决接口重名的问题，as就解决了。

#### default关键字

在export的时候，可能会用到default，说白了，它其实是别名的语法糖：

```
// d.js
export default function() {}

// 等效于：
function a() {};
export {a as default};
```

在import的时候，可以这样用：

```
import a from './d';

// 等效于，或者说就是下面这种写法的简写，是同一个意思
import {default as a} from './d';
```

<strong>这个语法糖的好处就是import的时候，可以省去花括号{}。简单的说，如果import的时候，你发现某个变量没有花括号括起来（没有*号），那么你在脑海中应该把它还原成有花括号的as语法。</strong>

下面这种写法你也应该理解了吧：
```
import $,{each,map} from 'jquery';
```
> import后面第一个 是 {defalut as} 的替代写法。

#### *符号

*就是代表所有，只用在import中，我们看下两个例子：

```
import * as _ from '_';
```
可以用 _.each 调用某个接口。

```
export * from '_';

// 等效于：
import * as all from '_';
export all;
```
*符号尽可能少用，它实际上是使用所有export的接口，但是很有可能你的当前模块并不会用到所有接口，可能仅仅是一个，所以最好的建议是使用花括号，用一个加一个。


### 该用require还是import？
require的使用非常简单，它相当于module.exports的传送门，module.exports后面的内容是什么，require的结果就是什么，对象、数字、字符串、函数……再把require的结果赋值给某个变量，相当于把require和module.exports进行平行空间的位置重叠。

而且require理论上可以运用在代码的任何地方，甚至不需要赋值给某个变量之后再使用，比如：
```
require('./a')(); // a模块是一个函数，立即执行a模块函数
var data = require('./a').data; // a模块导出的是一个对象
var a = require('./a')[0]; // a模块导出的是一个数组
```

你在使用时，完全可以忽略模块化这个概念来使用require，仅仅把它当做一个node内置的全局函数，它的参数甚至可以是表达式：

```
require(process.cwd() + '/a');
```


----------


[http://es6.ruanyifeng.com/#docs/module](http://es6.ruanyifeng.com/#docs/module)

mport命令会被 JavaScript 引擎静态分析，先于模块内的其他语句执行

```javascript
// 报错
if (x === 2) {
  import MyModual from './myModual';
}
```


上面代码中，引擎处理import语句是在编译时，这时不会去分析或执行if语句，所以import语句放在if代码块之中毫无意义，因此会报句法错误，而不是执行时错误。也就是说，import和export命令只能在模块的顶层，不能在代码块之中（比如，在if代码块之中，或在函数之中）。



但是import则不同，它是**编译时的，require是运行时的**它不会将整个模块运行后赋值给某个变量，而是只选择import的接口进行编译，这样在性能上比require好很多。

从理解上，require是赋值过程，import是解构过程，当然，require也可以将结果解构赋值给一组变量，但是import在遇到default时，和require则完全不同： var =require(′jquery′);和import from ‘jquery’ 是完全不同的两种概念。

上面完全没有回答“改用require还是import？”这个问题，因为这个问题就目前而言，根本没法回答，因为目前所有的引擎都还没有实现import，我们在node中使用babel支持ES6，也仅仅是将ES6转码为ES5再执行，import语法会被转码为require。这也是为什么在模块导出时使用module.exports，在引入模块时使用import仍然起效，因为本质上，import会被转码为require去执行。

但是，我们要知道这样一个道理，ES7很快也会发布，js引擎们会尽快实现ES6标准的规定，如果一个引擎连标准都实现不了，就会被淘汰， ES6是迟早的事 。如果你现在仍然在代码中部署require，那么等到ES6被引擎支持时，你必须升级你的代码，而如果现在开始部署import，那么未来可能只需要做很少的改动。

