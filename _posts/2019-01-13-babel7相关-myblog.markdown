---
layout:     post
title:      "Babel 原理、配置、插件与转译总结"
description: "系统整理 Babel 的配置方法（preset-env / polyfill / transform-runtime）、常用插件、转译原理以及从零实现 Babel 插件的完整实践。"
date:       2019-01-13 19:44:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 前端工程化
---

> “Yeah It's on. ”

## 前序

[https://juejin.cn/post/6956224866312060942](https://juejin.cn/post/6956224866312060942)

[Babel 7 相关](https://www.jynxio.com/article/11.html)

babel 6 存在的问题

- es 的标准每年都在变，现在的 stage-0 可能很快就 stage-2 了，那 preset 怎么维护，要不要跟着变，用户怎么知道这个 stage-x 都支持什么特性？
- 只能转成 es5，那目标环境支持一些 es6 特性了，那这些转换和 polyfill 岂不是无用功？ 而且还增加了产物的体积。
- polyfill 手动引入，比较麻烦，有没有更好的方式

babel 7 如何解决：

babel 7 废弃了 preset-20xx 和 preset-stage-x 的 preset 包，而换成了 preset-env，preset-env 默认会支持所有 es 标准的特性，如果没进入标准的，不再封装成 preset，需要手动指定 plugin-proposal-xxx。

> 更新说明：Babel 在语法转译、语法提案（proposal）支持、polyfill 按需注入、生态插件丰富度上依然是标杆，短期内不会被完全取代。但在"编译速度"这个维度上，它已被一批 Rust / Go 实现的工具明显挑战和替代：
>
> - **esbuild**（Go）：Vite 开发态依赖预构建、以及很多库的打包都用它，转译速度比 Babel 快一到两个数量级，但不做 polyfill、不支持自定义 AST 插件。
> - **SWC**（Rust）：Next.js 已默认用 SWC 替代 Babel 做编译与压缩（`next/babel` 仅在检测到自定义 `.babelrc` 时回退），Jest 也可用 `@swc/jest` 提速。
> - **Oxc / Rolldown**（Rust）：新一代工具链（含 Vite 未来的 Rolldown），进一步统一转译、打包、lint。
> - **tsc / tsgo**：纯 TS 项目可直接用 TypeScript 编译器输出，配合原生 ESM 已能满足很多场景。
>
> 现在仍强依赖 Babel 的典型场景：需要自定义 AST 插件（如 `babel-plugin-macros`、埋点/国际化插件）、依赖最新提案语法（装饰器等）、或已有一整套成熟的 Babel 配置难以迁移。新项目做技术选型时，如果没有自定义插件需求，优先考虑 SWC / esbuild 以获得更快的构建体验。

## 正文

[https://www.jianshu.com/p/cbd48919a0cc](https://www.jianshu.com/p/cbd48919a0cc)

[一文搞懂 core-js@3、@babel/polyfill、@babel/runtime、@babel/runtime-corejs3 的作用与区别](https://juejin.cn/post/7062621128229355528)

* @babel/cli
* @babel/core
* @babel/preset-env
* @babel/polyfill
* @babel/runtime
* @babel/plugin-transform-runtime
* @babel/plugin-transform-xxx

以上这些就是我们以后常常会使用的babel的各个重要部分了
这里要注意一下这个@这个符号，这个是只有babel7才特有的，babel6都木有

### @babel/cli

@babel/cli是babel提供的内建的命令行工具，主要是提供babel这个命令来对js文件进行编译，这里要注意它与另一个命令行工具@babel/node的区别，首先要知道他们二者都是命令行工具，但是官方文档明确对他们定义了他们各自的使用范围：

**@babel/cli 是一个适合安装在本地项目里，而不是全局安装**

>While you can install Babel CLI globally on your machine, it's much better to install it locally project by project.

**@babel/node 跟node cli类似，不适用在产品中，意味着适合全局安装**

>babel-node is a CLI that works exactly the same as the Node.js CLI
You should not be using babel-node in production

----

```javascript
let fun = () => console.log('hello babel')
```

我们在安装了@babel/cli或者@babel/node之后
使用@babel/cli编译
```text
$ babel test.js
```
使用@babel/node编译
```text
$ babel-node test.js
```
两个的编译结果都是该文件无任何变化

这个问题的发生来自 babel 6 。Babel 6 做了大量模块化的工作，将原来集成一体的各种编译功能分离出去，独立成插件。这意味着，默认情况下，当下版本的 babel 不会编译代码。

这里就扯淡了。。。你不能将箭头函数编译成es5，那搞个毛呀。。。

好吧，既然安装了@babel/core,安装了@babelb/cli这两个还是不行，那就说明它还需要别人配合，这也就是所谓的光有刀（@babel/core，@babelb/cli）不行，还得有料（@babel/plugin-transform-xxx）才行，一堆配合前两者，真正发挥作用的插件（@babel/plugin-transform-xxx）就登场

babel的相关插件

[https://babeljs.io/docs/en/plugins](https://babeljs.io/docs/en/plugins)

如果我的代码中大量使用插件，那我依然避免不了在配置文件中大量填写插件信息的工作，但是伟大的babel为了让程序员们有更多的时间做自己喜爱的事情，而不是浪费生命在一个一个的挑选插件，然后把它们写在.babelrc上，它提供了一个叫做preset的概念，说好听点叫预设，直白点就是插件包的意思，意味着babel会预先替我们做好了一系列的插件包，例如下面这些babel认为程序员会用到的常用的插件包：

* @babel/preset-env
* @babel/preset-flow
* @babel/preset-react
* @babel/preset-typescript

### @babel/env

[@babel/env vs @babel/preset-env](https://stackoverflow.com/questions/56108110/confused-with-babel-preset-configs-between-babel-env-and-babel-preset-env)

> Maybe they are same?

That is correct, the `preset-` piece is optional. Since you are doing

@babel/env 和 @babel/preset-env 是一个东西。

### @babel/preset-env

[ https://jdc.jd.com/archives/212962 ]( https://jdc.jd.com/archives/212962 )

使用`@babel/preset-env`，可以**「按需」**将`core-js`中的特性打包，这样可以显著减少最终打包的体积。

这里的**「按需」**，分为两个粒度：

- 宿主环境的粒度。根据不同宿主环境将该环境下所需的所有特性打包
- 按使用情况的粒度。仅仅将使用了的特性打包

那么我们在安装了@babel/preset-env，并且在.babelrc中配置了@babel/preset-env之后

```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "4"
        }
      }
    ]
  ]
}
```

当然这里的targets参数配置除了可以设置node环境外，还可以设置针对各个浏览器环境的配置，例如

```javascript
{
  "targets": {
    "chrome": "58",
    "ie": "11"
  }
}
```

第二个问题

眼尖的同学可以在代码编译之后的结果中找到，那就是代码中的
findIndex方法和padStart方法，这两个方法作为es6提出的新方法，居然没有被babel编译解析，这样如果我直接使用node命令执行编译后的index.js文件，那么必然是会报错的，因为我版本为4的node环境哪里认识什么findIndex和padStart，那这样就很尴尬了，所以光是使用@babel/preset-env是不够的，我们还需要一个叫@babel/polyfill的包来解决。

>引用别人的一段理解：解释的很好
>babel 编译过程处理第一种情况 - 统一语法的形态，通常是高版本语法编译成低版本的，比如 ES6 语法编译成 ES5 或 ES3。而 babel-polyfill 处理第二种情况 - 让目标浏览器支持所有特性，不管它是全局的，还是原型的，或是其它。这样，通过 babel-polyfill，不同浏览器在特性支持上就站到同一起跑线。

>我对polyfill的理解：polyfill我们又称垫片，见名知意，所谓垫片也就是垫平不同浏览器或者不同环境下的差异，因为有的环境支持这个函数，有的环境不支持这种函数，解决的是有与没有的问题，这个是靠单纯的@babel/preset-env不能解决的，因为@babel/preset-env解决的是将高版本写法转化成低版本写法的问题，因为不同环境下低版本的写法有可能不同而已。

####  useBuiltIns

[https://blog.csdn.net/james324/article/details/116128919](https://blog.csdn.net/james324/article/details/116128919)

 https://www.babeljs.cn/docs/babel-preset-env#modules

**useBuiltIns 就是使用 polyfill （corejs）的方式，是在入口处全部引入（entry），还是每个文件引入用到的（usage），或者不引入（false）。**

entry:

- **用法**: 当你将 `useBuiltIns` 设置为 `entry` 时，Babel 会要求你在应用程序的入口文件中显式地导入 `core-js` 库中的 polyfills，如 `import "core-js/stable";` 和 `import "regenerator-runtime/runtime";`。
- 特点
  - 可以提前加载所有需要的 polyfills。
  - 较为适合于较大或较复杂的应用。
  - 需要手动导入 polyfills。

usage:

- **用法**: 如果将 `useBuiltIns` 设置为 `usage`，Babel 会自动根据代码中使用的特性来包含所需的 polyfills。这意味着只会引入实际用到的部分。
- 特点
  - 更加自动化和简便，无需手动引入 polyfills。
  - 可以减少最终生成代码的大小，因为只包含所需的 polyfills。
  - 适合在代码量较小或不确定具体使用了哪些特性时使用。

当值为 `entry` 时，Babel 会将 `import"@babel/polyfill"` 或者 `require("@babel/polyfill")` 语句根据我们指定的环境配置替换为单个的 polyfill require。

如将

```js
import "@babel/polyfill";
```

替换为

```js
import "core-js/modules/es7.string.pad-start";
import "core-js/modules/es7.string.pad-end";
```

注意：

**polyfill 机制是，对于例如 Array.from 等静态方法，直接在 global.Array 上添加 （添加在全局，造成全局变量污染）**

**这种使用 polyfill的方式不适合第三库的开发**

####  targets

`string | Array | { [string]: string }`，默认为`{}`。

```json
{
  "targets": "> 0.25%, not dead"
}
```

或支持最低环境版本的对象：

```json
{
  "targets": {
    "chrome": "58",
    "ie": "11"
  }
}
```

**如果未指定targets，则旁注`@babel/preset-env`将默认转换所有ECMAScript 2015+代码。**

### @babel/preset-react

[https://babeljs.io/docs/en/babel-preset-react](https://babeljs.io/docs/en/babel-preset-react)

This preset always includes the following plugins:

- [@babel/plugin-syntax-jsx](https://babeljs.io/docs/en/babel-plugin-syntax-jsx)
- [@babel/plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)
- [@babel/plugin-transform-react-display-name](https://babeljs.io/docs/en/babel-plugin-transform-react-display-name)

#### runtime

```text
classic | automatic, defaults to classic
```

`automatic` auto imports the functions that JSX transpiles to. `classic` does not automatic import anything.

### @babel/standalone

[https://babeljs.io/docs/en/babel-standalone](https://babeljs.io/docs/en/babel-standalone)

@babel/standalone provides a standalone build of Babel for use in browsers and other non-Node.js environments.

execute all script tags with type `text/babel` or `text/jsx`:

```html
<div id="output"></div>
<!-- Load Babel -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<!-- Your custom script here -->
<script type="text/babel">
  const getMessage = () => "Hello World";
  document.getElementById("output").innerHTML = getMessage();
</script>
```

### @babel/plugin-transform-runtime

[https://babeljs.io/docs/babel-plugin-transform-runtime#options](https://babeljs.io/docs/babel-plugin-transform-runtime#options)

A plugin that enables the re-use of Babel's injected helper code to save on codesize.

**避免全局污染**：通过使用这个插件，Babel 生成的代码不会将辅助函数添加到全局作用域中，减少了潜在的命名冲突。

### @babel/polyfill

[https://mp.weixin.qq.com/s/hgiSKyFxb6RFD6n2diixvQ](https://mp.weixin.qq.com/s/hgiSKyFxb6RFD6n2diixvQ)

`@babel/polyfill`可以看作是：`core-js`加`regenerator-runtime`。

regenerator-runtime是generator以及async/await的运行时依赖

单独使用`@babel/polyfill`会将`core-js`全量导入，造成项目打包体积过大。

> 从**Babel v7.4.0**[5]开始，`@babel/polyfill`被废弃了，可以直接引用`core-js`与`regenerator-runtime`替代

### **@babel/runtime**

[https://www.babeljs.cn/docs/babel-runtime](https://www.babeljs.cn/docs/babel-runtime)

**需要放到dependencies**

`@babel/runtime`包含了`Babel`所有**「辅助方法」**以及`regenerator-runtime`。

单纯引入`@babel/runtime`还不行，因为`Babel`不知道何时引用`@babel/runtime`中的**「辅助方法」**。

所以，还需要引入`@babel/plugin-transform-runtime`。

这个插件会在编译时将所有使用**「辅助方法」**的地方从**「自己维护一份」**改为从`@babel/runtime`中引入。

#### @babel/runtime-corejs 2/3

* **@babel/runtime-corejs2**

它是 `@babel/runtime` 的升级版，它不仅仅包含 `@babel/runtime` 的所有内容，还包含 2 号主版本的 `core-js` 。

2 号主版本的 `core-js` 只支持全局变量（如 `Promise` ）和静态属性（如 `Array.from` ），不支持实例属性（如 `Array.prototype.includes` ）。

* **@babel/runtime-corejs3**

它是 `@babel/runtime` 的升级版，它不仅仅包含 `@babel/runtime` 的所有内容，还包含 3 号主版本的 `core-js` 。

3 号主版本的 `core-js` 不仅支持全局变量（如 `Promise` ）和静态属性（如 `Array.from` ），还支持实例属性（如 `Array.prototype.includes` ）。

**和corejs的区别**

使用 `core-js@3` 来 polyfill，会全局引入缺少的 API，例如 `require("core-js/modules/es.promise.js")`。有时候开发工具库，希望避免 polyfill 污染全局变量。**这时候可以不使用  `core-js@3` 来 polyfill，转而使用 `@babel/runtime-corejs3`，`@babel/runtime-corejs3` 相当于 `@babel/runtime` + 不污染环境的 `core-js@3`。**

### @babel/register

[https://www.babeljs.cn/docs/babel-register](https://www.babeljs.cn/docs/babel-register)

@babel/register在底层改写了node的require方法，在代码里引入@babel/register模块后，所有通过require引入并且以.es6, .es, .jsx 和 .js为后缀名的模块都会经过babel的转译。

### @babel/node

[https://babeljs.io/docs/en/babel-node](https://babeljs.io/docs/en/babel-node)

babel-node is a CLI that works exactly the same as the Node.js CLI, with the added benefit of compiling with Babel presets and plugins before running it.

### plugin

#### @babel/plugin-transform-runtime

**（适合第三方库的开发）**

[@babel/plugin-transform-runtime 到底是什么](https://zhuanlan.zhihu.com/p/147083132)

@babel/plugin-transform-runtime的作用是提供统一的模块化的helper，那什么是helper，我们举个例子：

我们编译之后的index.js代码里面有不少新增加的函数，如_classCallCheck，_defineProperties，_createClass，这种函数就是helper。

那这种helper跟我们的@babel/runtime有什么关系了，我们接着看，比如像这个`_createClass`就是我们将es6的class关键字转化成传统js时生成的一个函数，那么如果我有很多个js文件中都定义了class类，那么在编译转化时就会产生大量相同的`_createClass`方法，那这些`_createClass`这样的helper方法是不是冗余太多，因为它们基本都是一样的，所以我们能不能采用一个统一的方式提供这种helper，也就是利用es或者node的模块化的方式提供helper，将这些helper做成一个模块来引入到代码中，岂不是可以减少这些helper函数的重复书写。

**@babel/runtime 要装在 dependencies  **

**而 @babel/plugin-transform-runtime 要装在 devDependencies 中**

---

然后就只需要在.babelrc中写上：

```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "4"
        }
      }
    ]
  ],
  "plugins": [
    "@babel/plugin-transform-runtime"
  ]
}
```

这样就会自动地添加

```javascript
var _interopRequireDefault = require("@babel/runtime/helpers/interopRequireDefault");
var _classCallCheck2 = _interopRequireDefault(require("@babel/runtime/helpers/classCallCheck"));
var _createClass2 = _interopRequireDefault(require("@babel/runtime/helpers/createClass"));
```

这一类helper已经是被从@babel/runtime包require进来了，这都是@babel/runtime的功劳，但是事情还没完，我们还有个包@babel/plugin-transform-runtime没提到就用了，这个包的作用其实就是辅助@babel/runtime的，因为有了@babel/plugin-transform-runtime它会帮我自动动态require  @babel/runtime中的内容，如果没有这个@babel/plugin-transform-runtime，那么我们要使用@babel/runtime中的内容，就只有像require('@babel/polyfill')那样人工去手动添加了，所以@babel/plugin-transform-runtime非常方便，由于@babel/plugin-transform-runtime是一个插件，所以它是需要配置到.babelrc中的，这一点要记住。

-----

```javascript
let isHas = [1,2,3].includes(2);

new Promise((resolve, reject) => {
    resolve(100);
});
```

编译出来的代码如下

```javascript
"use strict";
require("core-js/modules/es.array.includes");
require("core-js/modules/es.object.to-string");
require("core-js/modules/es.promise");

var isHas = [1, 2, 3].includes(2);
new Promise(function (resolve, reject) {  
    resolve(100);
});
```

Array.prototype 上新增了 includes 方法，并且新增了全局的 Promise 方法，污染了全局环境，这跟不使用 @babel/plugin-transform-runtime 没有区别嘛。

如果我们希望 @babel/plugin-transform-runtime 不仅仅处理帮助函数，同时也能加载 polyfill 的话，我们需要给 @babel/plugin-transform-runtime 增加配置信息。

首先新增依赖 @babel/runtime-corejs3:

修改配置文件如下(移除了 @babel/preset-env 的 useBuiltIns 的配置，不然不就重复了

```javascript
{    
"presets": [
    "@babel/preset-env"     
],    
"plugins": [   
[        
    "@babel/plugin-transform-runtime",{     
   			 "corejs": 3       
        }     
    ]   
]}
```

然后重新编译，看一下，编译出来的结果

```javascript
"use strict";
var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");
var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));
var _includes = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/instance/includes"));


var _context;var isHas = (0, _includes.default)(_context = [1, 2, 3]).call(_context, 2);new _promise.default(function (resolve, reject) {  resolve(100);});
```

可以看出，没有直接去修改 Array.prototype，或者是新增 Promise 方法，避免了全局污染。如果上面 @babel/plugin-transform-runtime 配置的 core-js 是 "2"，其中不包含实例的 polyfill 需要单独引入。

**如果我们配置的 corejs 是 3 版本，那么不管是实例方法还是全局方法，都不会再污染全局环境。**

**总结：**

babel 在转译的过程中，对 syntax 的处理可能会使用到 helper 函数，对 api 的处理会引入 polyfill。

默认情况下，babel 在每个需要使用 helper 的地方都会定义一个 helper，导致最终的产物里有大量重复的 helper；引入 polyfill 时会直接修改全局变量及其原型，造成原型污染。

@babel/plugin-transform-runtime 的作用是将 helper 和 polyfill 都改为从一个统一的地方引入，并且引入的对象和全局变量是完全隔离的

##### useESModules

[https://www.jiangruitao.com/babel/transform-runtime3/](https://www.jiangruitao.com/babel/transform-runtime3/)

```tsx
   [
      "@babel/plugin-transform-runtime",
      {
        "corejs": {
          "version": 3,
          "proposals": true
        },
        "useESModules": true
      }
    ],
```

该项用来设置是否使用ES6的模块化用法，取值是布尔值。默认是fasle，在用webpack一类的打包工具的时候，我们可以设置为true，以便做静态分析。

##### regenerator

[https://www.babeljs.cn/docs/babel-plugin-transform-runtime#regenerator](https://www.babeljs.cn/docs/babel-plugin-transform-runtime#regenerator)

`boolean`, defaults to `true`.

切换是否将生成器函数转换为使用不会污染全局作用域的再生器运行时。

```tsx
try {
  regeneratorRuntime = runtime;
} catch (accidentalStrictMode) {
  // This module should not be running in strict mode, so the above
  // assignment should always work unless something is misconfigured. Just
  // in case runtime.js accidentally runs in strict mode, in modern engines
  // we can explicitly access globalThis. In older engines we can escape
  // strict mode using a global Function call. This could conceivably fail
  // if a Content Security Policy forbids using Function, but in that case
  // the proper solution is to fix the accidental strict mode problem. If
  // you've misconfigured your bundler to force strict mode and applied a
  // CSP to forbid Function, and you're not willing to fix either of those
  // problems, please detail your unique predicament in a GitHub issue.
  if (typeof globalThis === "object") {
    globalThis.regeneratorRuntime = runtime;
  } else {
    Function("r", "regeneratorRuntime = r")(runtime);
  }
}
```

#### @babel/plugin-proposal-decorators

[https://babeljs.io/docs/en/babel-plugin-proposal-decorators](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)

#### @babel/plugin-proposal-class-properties

[https://babel.docschina.org/docs/en/babel-plugin-proposal-class-properties/](https://babel.docschina.org/docs/en/babel-plugin-proposal-class-properties/)

##### loose

`boolean`，默认为 `false`。

当设置为 `true` 时，类属性将被编译为赋值表达式而不是 `Object.defineProperty`。

#### @babel/plugin-proposal-decorators

[https://babeljs.io/docs/en/babel-plugin-proposal-decorators](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)

##### legacy

`boolean`, defaults to `false`.

Use the legacy (stage 1) decorators syntax and behavior.

### use strict

[https://blog.csdn.net/qq_40259641/article/details/84106252](https://blog.csdn.net/qq_40259641/article/details/84106252)

这是babelES6转译ES5自动加上的,使用严格模式的意思;

严格模式有什么用?

主要有以下几个：(错误检测、规范、效率、安全、面向未来)
* 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;
* 消除代码运行的一些不安全之处，保证代码运行的安全；
* 提高编译器效率，增加运行速度；
* 为未来新版本的Javascript做好铺垫。

如何取消严格模式？

```bash
npm install babel-plugin-transform-remove-strict-mode
```

```javascript
{
  "plugins": ["transform-remove-strict-mode"]
}
```

>babel-plugin-transform-remove-strict-mode这个不是babel7的东西，babel7要使用@babel开头的东西

### loose mode
[https://www.jianshu.com/p/8f47a5364665](https://www.jianshu.com/p/8f47a5364665)

loose mode 我翻译为松散模式，loose mode在babel中通常是不推荐使用的，但是我们需要知道的是使用 loose mode 转换而来的代码更加像ES5的代码（更像是人手写的）

### comments
```javascript
{
  "presets": [                       //预设置的语法
    "es2015",
    "stage-2"
  ],  
  "plugins": ["transform-runtime"],  //插件
  "comments": false,                 //是在生成的文件中，不产生注释
}
```

### @babel/preset-env中配置polyfill

[https://www.babeljs.cn/docs/babel-preset-env#usebuiltins](https://www.babeljs.cn/docs/babel-preset-env#usebuiltins)

useBuiltIns

```text
"usage" | "entry" | false, defaults to false.
```

>This option adds direct references to the core-js module as bare imports. Thus core-js will be resolved relative to the file itself and needs to be accessible. You may need to specify core-js@2 as a top level dependency in your application if there isn't a core-js dependency or there are multiple versions.

This option configures how @babel/preset-env handles polyfills.

-----

**useBuiltIns: 'entry'**

>NOTE: Only use require("@babel/polyfill"); once in your whole app. Multiple imports or requires of @babel/polyfill will throw an error since it can cause global collisions and other issues that are hard to trace. **We recommend creating a single entry file that only contains the require statement.**

This option enables a new plugin that replaces the statement import "@babel/polyfill" or require("@babel/polyfill") with individual requires for @babel/polyfill based on environment.

---------------

**useBuiltIns: 'usage' (experimental)**

Adds specific imports for polyfills when they are used in each file. We take advantage of the fact that a bundler will load the same polyfill only once.

**@babel/preset-env 提供了一个 useBuiltIns 参数，设置值为 usage 时，就只会包含代码需要的 polyfill 。有一点需要注意：配置此参数的值为 usage ，必须要同时设置 corejs**

Babel 会检查所有代码，以便查找在目标环境中缺失的功能，然后仅仅把需要的 polyfill 包含进来。

------------

**useBuiltIns: false**

Don't add polyfills automatically per file, or transform import "@babel/polyfill" to individual polyfills.

### vuecli3中默认useBuiltIns: 'usage'

[https://cli.vuejs.org/zh/guide/browser-compatibility.html#browserslist](https://cli.vuejs.org/zh/guide/browser-compatibility.html#browserslist)

一个默认的 Vue CLI 项目会使用 [@vue/babel-preset-app](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app)，它通过 `@babel/preset-env` 和 `browserslist` 配置来决定项目需要的 polyfill。

默认情况下，它会把 [`useBuiltIns: 'usage'`](https://new.babeljs.io/docs/en/next/babel-preset-env.html#usebuiltins-usage) 传递给 `@babel/preset-env`，这样它会根据源代码中出现的语言特性自动检测需要的 polyfill。这确保了最终包里 polyfill 数量的最小化。然而，这也意味着**如果其中一个依赖需要特殊的 polyfill，默认情况下 Babel 无法将其检测出来。**

如果有依赖需要 polyfill，你有几种选择：

1. **如果该依赖基于一个目标环境不支持的 ES 版本撰写:** 将其添加到 `vue.config.js` 中的 [`transpileDependencies`](https://cli.vuejs.org/zh/config/#transpiledependencies) 选项。这会为该依赖同时开启语法转换和根据使用情况检测 polyfill。
2. **如果该依赖交付了 ES5 代码并显式地列出了需要的 polyfill:** 你可以使用 `@vue/babel-preset-app` 的 [polyfills](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app#polyfills) 选项预包含所需要的 polyfill。**注意 `es.promise` 将被默认包含，因为现在的库依赖 Promise 是非常普遍的。**

```js
// babel.config.js
module.exports = {
  presets: [
    ['@vue/app', {
      polyfills: [
        'es.promise',
        'es.symbol'
      ]
    }]
  ]
}
```

3. **如果该依赖交付 ES5 代码，但使用了 ES6+ 特性且没有显式地列出需要的 polyfill (例如 Vuetify)：\**请使用 `useBuiltIns: 'entry'` 然后在入口文件添加 `import 'core-js/stable'; import 'regenerator-runtime/runtime';`。这会根据 `browserslist` 目标导入\**所有** polyfill，这样你就不用再担心依赖的 polyfill 问题了，但是因为包含了一些没有用到的 polyfill 所以最终的包大小可能会增加。

#### 构建库或是 Web Component 时的 Polyfills

**当使用 Vue CLI 来[构建一个库或是 Web Component](https://cli.vuejs.org/zh/guide/build-targets.html) 时，推荐给 `@vue/babel-preset-app` 传入 `useBuiltIns: false` 选项。这能够确保你的库或是组件不包含不必要的 polyfills。通常来说，打包 polyfills 应当是最终使用你的库的应用的责任。**

### esm 打包

1. 配置`@babel/preset-env`的`modules`选项为`false`，关闭模块转换；
2. 配置`@babel/plugin-transform-runtime`的`useESModules`选项为`true`，使用`ES module`形式引入`helper`函数。

```tsx
module.exports = {
  presets: [
    [
      '@babel/env',
      {
        // Setting this to false will preserve ES modules
        // https://babeljs.io/docs/babel-preset-env#modules
        modules: false, // 关闭模块转换
      },
    ],
    '@babel/typescript',
    '@babel/react',
  ],
  plugins: [
    '@babel/proposal-class-properties',
    [
      '@babel/plugin-transform-runtime',
      {
        useESModules: true, // 使用esm形式的helper
      },
    ],
  ],
};
```

## 大坑

### presets

当你安装了@babel/preset-env 使用时 **presets: ['@babel/env']**

重点注意是这个
**@babel/env**

### @babel/preset-es2015
[https://www.npmjs.com/package/@babel/preset-es2015](https://www.npmjs.com/package/@babel/preset-es2015)

这个东西在babel7中被干掉了，真的坑 ，这里坑了一个小时

This package has been deprecated

>👋 We've deprecated any official yearly presets in 6.x in favor or babel-preset-env. For 7.x it would be @babel/preset-env.

### @babel开头

**babel7之后babel相关的npm包都是以@babel开头的**

比如：我们不能使用plugin-transform-strict-mode，要使用@babel/plugin-transform-strict-mode

[https://www.npmjs.com/package/@babel/plugin-transform-strict-mode](https://www.npmjs.com/package/@babel/plugin-transform-strict-mode)

### @babel/plugin-transform-strict-mode
**默认babel转义后的js文件头会带上'use strict';**

如果想去掉js文件头部的'use strict'

**@babel/plugin-transform-strict-mode取消js文件的严格模式 (默认babel转义后的js文件头会带上'use strict';)**

```javascript
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    "@babel/plugin-transform-strict-mode"
  ]
}
```

### gulp-babel的坑

```javascript
const gulp = require('gulp');
const babel = require('gulp-babel');
 
gulp.task('default', () =>
    gulp.src('src/app.js')
        .pipe(babel({
            presets: ['@babel/env']
        }))
        .pipe(gulp.dest('dist'))
);
```

**这里一定要是@babel/env 不能是其他的东西  而且这里不能使用plugins 很无语**

>很奇葩的东西，我也很绝望啊

经过一段时间的排查应该是版本的问题

gulp-babel don't produce any output file or doesn't work properly

[https://stackoverflow.com/questions/52599370/gulp-babel-dont-produce-any-output-file-or-doesnt-work-properly](https://stackoverflow.com/questions/52599370/gulp-babel-dont-produce-any-output-file-or-doesnt-work-properly)

---------

卧槽，最后解决的办法居然是

**使用babel-preset-env而不是用@babel/preset-env**

  **使用这个  "babel-preset-env": "^1.7.0",**

  **而且不使用babel7相关东西 全部降低到babel6相关**

  >服Orz

----------------------------------------

### babel 版本的问题

[https://www.cnblogs.com/soyxiaobi/p/9554565.html](https://www.cnblogs.com/soyxiaobi/p/9554565.html)

在配置webpack.config.js自动打包的时候,出现Error: Cannot find module '@babel/core'错误

**官方默认babel-loader | babel 对应的版本需要一致: 即babel-loader需要搭配最新版本babel**

### Couldn't find preset "@babel/env"

[https://github.com/babel/babel/issues/9016](https://github.com/babel/babel/issues/9016)

Couldn't find preset "@babel/env" relative to directory

解决：

I change npm package's .babelrc to .babelrc.js,and it work

```javascript
module.exports = {
  presets: [
    require('@babel/preset-env')
  ]
}
```

## 常用插件补充

> 以下整理自早期 Babel 配置笔记。

### babel-plugin-dynamic-import-node

[https://www.npmjs.com/package/babel-plugin-dynamic-import-node](https://www.npmjs.com/package/babel-plugin-dynamic-import-node)

它只做一件事：将所有的 `import()` 转化为 `require()`，这样就可以用同步方式引入异步组件。结合 `BABEL_ENV` 环境变量，让它只作用于开发环境，可以大大提升热更新速度。

```js
{
  "env": {
    "development": {
      "plugins": ["dynamic-import-node"]
    }
  }
}
```

### babel-plugin-import

[https://www.npmjs.com/package/babel-plugin-import](https://www.npmjs.com/package/babel-plugin-import)

**为组件库实现单组件按需加载并且自动引入其样式**：

```js
import { Button } from 'antd';

↓ ↓ ↓ ↓ ↓ ↓

var _button = require('antd/lib/button');
require('antd/lib/button/style');
```

原理：和普遍的 babel 插件一样遍历 AST，然后：

1. **收集依赖**：找到 `importDeclaration`，分析出包和依赖，若包名和 `libraryName` 一致就收集。
2. **判断是否使用**：判断收集到的成员是否在代码中被使用（如 `CallExpression`），有使用就调用 `importMethod` 生成新的 `import` 语句。
3. **生成引入代码**：根据配置项生成代码和样式的 `import` 语句。

三个核心参数：

```js
{
  "libraryName": "antd",     // 包名
  "libraryDirectory": "lib", // 目录，默认 lib
  "style": true,             // 是否引入 style
}
```

### babel-plugin-transform-remove-console

移除所有 `console.*` 调用。

### plugin-transform-typescript

[https://babeljs.io/docs/en/babel-plugin-transform-typescript](https://babeljs.io/docs/en/babel-plugin-transform-typescript)

添加对 TypeScript 类型语法的支持（该插件已包含在 `@babel/preset-typescript` 中），但**不做类型检查**。推荐 `preset-env` 与 `preset-typescript` 配合使用。

### babel-plugin-module-resolver

[https://www.npmjs.com/package/babel-plugin-module-resolver](https://www.npmjs.com/package/babel-plugin-module-resolver)

在编译时为模块添加新的解析器，允许添加“根”目录以及为目录、文件或 npm 模块设置自定义别名。

### loose mode

[https://2ality.com/2015/12/babel6-loose-mode.html](https://2ality.com/2015/12/babel6-loose-mode.html)

许多插件有两种模式：

- **normal 模式**：尽可能遵循 ECMAScript 6 语义（通过 `Object.defineProperty` 定义对象属性）。
- **loose 模式**：产生更简单的 ES5 代码（用 `=` 直接赋值，更像手写的 ES5）。

一般不推荐使用 loose 模式：生成代码可能更快更兼容旧引擎，但从编译 ES6 切换到原生 ES6 时可能遇到问题。

### Generator

通过 `function*` 定义的“生成器函数”，特点是可以中断函数的执行。每次执行 `yield` 语句后函数暂停，直到调用生成器对象的 `next()` 才继续。Generator 函数是一个状态机，封装了多个内部状态；执行 async/await、yield 需要 `regenerator-runtime` 运行时支持。

## 从零实现一个 Babel 插件

[babel-handbook](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md)

### babel 工作原理

**Babel 对代码进行转换：先将 JS 代码解析为 AST 抽象语法树（解析），对树做静态分析（转换），再将语法树生成 JS 代码（生成）。每一层树被称为节点，每个节点都有 `type` 属性描述节点类型。**

```javascript
// 代码
const result = 1 + 1

// 生成的 AST（节选）
{
  "type": "Program",
  "body": [
    {
      "type": "VariableDeclaration",
      "declarations": [
        {
          "type": "VariableDeclarator",
          "id": { "type": "Identifier", "name": "result" },
          "init": {
            "type": "BinaryExpression",
            "left": { "type": "Literal", "value": 1 },
            "operator": "+",
            "right": { "type": "Literal", "value": 1 }
          }
        }
      ],
      "kind": "const"
    }
  ],
  "sourceType": "module"
}
```

可以用 [astexplorer](https://astexplorer.net/) 在线查看 AST。

#### @babel/core

“微内核”架构中的“内核”，主要负责：

- 加载和处理配置（config）
- 加载插件
- 调用 `Parser` 进行语法解析，生成 `AST`
- 调用 `Traverser` 遍历 AST，并使用「访问者模式」应用插件对 AST 进行转换
- 生成代码，包括 SourceMap 转换和源代码生成

#### 遍历与访问者

AST 是树形结构，转换通过访问者对 AST 的遍历实现。**Visitors 访问者是一个对象，不同属性对应不同的 AST 节点类型。注意对每一个节点的遍历会执行两次：进入节点一次，退出节点一次。**

```javascript
const visitors = {
  enter (path) {
    // 进入该节点
  },
  exit (path) {
    // 退出该节点
  }
}
```

#### 路径

每个节点都拥有自身的路径对象（访问者的参数）。`path.node` 代表该节点，`path.parent` 代表父节点，`path.replaceWith` / `path.replaceWithMultiple` 用于替换节点。

**替换一个节点：**

```javascript
BinaryExpression(path) {
  path.replaceWith(
    t.binaryExpression("**", path.node.left, t.numberLiteral(2))
  );
}
```

**用多节点替换单节点：**

```javascript
ReturnStatement(path) {
  path.replaceWithMultiple([
    t.expressionStatement(t.stringLiteral("Is this the real life?")),
    t.expressionStatement(t.stringLiteral("Is this just fantasy?")),
  ]);
}
```

**停止遍历**用 `path.stop()`：

```js
const visitor = {
  FunctionDeclaration(path, state) {
    console.log("1111")
    path.stop()
  },
}
```

### 插件选项

如果想让用户自定义插件行为，可以接受插件特定选项，这些选项会通过 `state` 对象传递给访问者：

```javascript
export default function({ types: t }) {
  return {
    visitor: {
      FunctionDeclaration(path, state) {
        console.log(state.opts);
        // { option1: true, option2: false }
      }
    }
  }
}
```

### 插件的 pre / post 函数

插件可以具有在插件之前或之后运行的函数，用于设置或清理/分析：

```javascript
export default function({ types: t }) {
  return {
    pre(state) {
      this.cache = new Map();
    },
    visitor: {
      StringLiteral(path) {
        this.cache.set(path.node.value, 1);
      }
    },
    post(state) {
      console.log(this.cache);
    }
  };
}
```

### 插件开发常见问题

**获取当前文件名：**

```javascript
Identifier(path, state) {
  console.log(state.file.opts.filename);
}
```

**Maximum call stack size exceeded**：使用 `path.replaceWith` 时若用相同类型节点替换自身，会导致无限递归。例如在 `FunctionDeclaration` 中再 `replaceWith` 一个 `functionDeclaration`，会不断触发该访问者而崩溃。

**函数的 4 种情况：**

```text
FunctionDeclaration          函数声明        function foo(){}
FunctionExpression           函数表达式      const func = function () {}
ObjectMethod                 对象方法        let obj = { fn () {} }
ArrowFunctionExpression      箭头函数        const func = ()=> {}
```
