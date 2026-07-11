---
layout:     post
title:      "Babel 配置、插件与转译原理总结"
description: "系统整理「Babel 配置、插件与转译原理总结」的配置方法、工作机制、工程实践和常见问题。"
date:       2018-03-20 22:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Babel
---

> “Yeah It's on. ”

[写一个babel插件](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md#toc-plugin-options)

## 正文

[网页链接](https://segmentfault.com/p/1210000008466178)

[https://juejin.im/post/6844903602822053895#heading-3](https://juejin.im/post/6844903602822053895#heading-3)

babel可以将当前运行平台(浏览器、node服务器)尚不支持的下一代或几代js语法编译为当前支持的js语法版本，比如可以把es6 es7和es8的js代码编译为es5的代码。

* **plugin: babel的插件，在6.x版本之后babel必需要配合插件来进行工作**
* **preset: babel插件集合的预设，包含某一部分的插件plugin**

### 插件

Babel 构建在插件之上，使用现有的或者自己编写的插件可以组成一个转换通道，Babel 的插件分为两种: 语法插件和转换插件。

#### 语法插件

这些插件只允许 Babel 解析（parse） 特定类型的语法（不是转换），可以在 AST 转换时使用，以支持解析新语法，例如：

```javascript
import * as babel from "@babel/core";
const code = babel.transformFromAstSync(ast, {   
  //支持可选链 
  plugins: ["@babel/plugin-proposal-optional-chaining"],  
  babelrc: false
}).code;
```

#### 转换插件

转换插件会启用相应的语法插件(因此不需要同时指定这两种插件)，这点很容易理解，如果不启用相应的语法插件，意味着无法解析，连解析都不能解析，又何谈转换呢？

#### 插件顺序

[https://www.babeljs.cn/docs/plugins#%E6%8F%92%E4%BB%B6%E9%A1%BA%E5%BA%8F](https://www.babeljs.cn/docs/plugins#%E6%8F%92%E4%BB%B6%E9%A1%BA%E5%BA%8F)

这意味着如果两个转换插件都将处理“程序（Program）”的某个代码片段，则将根据转换插件或 preset 的排列顺序依次执行。

* **插件在 Presets 前运行。**
* **插件顺序从前往后排列。**
* **Preset 顺序是颠倒的（从后往前）。**

例如：

```json
{
  "plugins": ["transform-decorators-legacy", "transform-class-properties"]
}
```

先执行 transform-decorators-legacy ，在执行 transform-class-properties。

**重要的时，preset 的顺序是 颠倒的。如下设置：**

```json
{
  "presets": ["es2015", "react", "stage-2"]
}
```

将按如下顺序执行：stage-2、react 然后是 es2015。

>这主要的是为了确保向后兼容，因为大多数用户将 "es2015" 排在 "stage-0" 之前。

#### 插件的使用

如果插件发布在 npm 上，可以直接填写插件的名称， Babel 会自动检查它是否已经被安装在 node_modules 目录下，在项目目录下新建 .babelrc 文件

```javascript
//.babelrc
{
    "plugins": ["@babel/plugin-transform-arrow-functions"]
}
```

也可以指定插件的相对/绝对路径

```javascript
{
    "plugins": ["./node_modules/@babel/plugin-transform-arrow-functions"]
}
```

### 常用插件

#### babel-plugin-dynamic-import-node

[https://www.npmjs.com/package/babel-plugin-dynamic-import-node](https://www.npmjs.com/package/babel-plugin-dynamic-import-node)

[https://github.com/airbnb/babel-plugin-dynamic-import-node](https://github.com/airbnb/babel-plugin-dynamic-import-node)

它只做一件事就是：将所有的`import()`转化为`require()`，这样就可以用这个插件将所有异步组件都用同步的方式引入了，并结合 [BABEL_ENV](https://babeljs.io/docs/usage/babelrc/#env-option) 这个`bebel`环境变量，让它只作用于开发环境下。

将开发环境中所有`import()`转化为`require()`，这种方案解决了之前重复打包的问题，同时对代码的侵入性也很小，你平时写路由的时候只需要按照官方[文档](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)路由懒加载的方式就可以了，其它的都交给`babel`来处理，当你不想用这个方案的时候，也只需要将它从`babel` 的 `plugins`中移除就可以了。

**具体代码：**

首先在`package.json`中增加`BABEL_ENV`

```js
"dev": "BABEL_ENV=development webpack-dev-server XXXX"
```

接着在`.babelrc`只能加入`babel-plugin-dynamic-import-node`这个`plugins`，并让它只有在`development`模式中才生效。

```js
{
  "env": {
    "development": {
      "plugins": ["dynamic-import-node"]
    }
  }
}
```

之后就大功告成了，路由只要像平时一样写就可以了。[文档](https://panjiachen.github.io/vue-element-admin-site/zh/guide/advanced/lazy-loading.html#新方案)

```js
{ path: '/login', component: () => import('@/views/login/index')}
```

这样能大大提升你热更新的速度。基本两百加页面也能在`2000ms`的热跟新完成，基本做到无感刷新。当然你的项目本身就不大页面也不多，完全没必要搞这些。**当你的页面变化跟不是你写代码速度的时候再考虑也不迟。**

#### babel-plugin-import

[https://www.npmjs.com/package/babel-plugin-import](https://www.npmjs.com/package/babel-plugin-import)

[https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD](https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)

[ https://juejin.im/post/5eefff756fb9a0589b027d97?utm_source=gold_browser_extension ]( https://juejin.im/post/5eefff756fb9a0589b027d97?utm_source=gold_browser_extension )

为什么会出现这个插件？

```js
import { Affix, Avatar, Button, Rate } from 'antd';

import 'antd/lib/affix/style';
import 'antd/lib/avatar/style';
import 'antd/lib/button/style';
import 'antd/lib/rate/style';
```

 会不会觉得这样的代码不够优雅？

 简单来说，`babel-plugin-import` 就是解决了上面的问题，**为组件库实现单组件按需加载并且自动引入其样式**

```js
import { Button } from 'antd';   


↓ ↓ ↓ ↓ ↓ ↓


var _button = require('antd/lib/button');
require('antd/lib/button/style');
```

总结

我们来总结一下，`babel-plugin-import` 和普遍的 `babel` 插件一样，会遍历代码的 `ast`，然后在 `ast` 上做了一些事情：

1. **收集依赖**：找到 `importDeclaration`，分析出包 `a` 和依赖 `b,c,d....`，假如 `a` 和 `libraryName` 一致，就将 `b,c,d...` 在内部收集起来

2. **判断是否使用**：在多种情况下（比如文中提到的 `CallExpression`）判断 收集到的 `b,c,d...` 是否在代码中被使用，如果有使用的，就调用 `importMethod` 生成新的 `impport` 语句

3. **生成引入代码**：根据配置项生成代码和样式的 `import` 语句

-----

这个插件怎么用?

简单来说就需要关心三个参数即可：

```js
{
  "libraryName": "antd",     // 包名
  "libraryDirectory": "lib", // 目录，默认 lib
  "style": true,             // 是否引入 style
}
```

#### babel-plugin-transform-remove-console

[https://www.npmjs.com/package/babel-plugin-transform-remove-console](https://www.npmjs.com/package/babel-plugin-transform-remove-console)

This plugin removes all `console.*` calls.

#### plugin-transform-typescript

[https://babeljs.io/docs/en/babel-plugin-transform-typescript](https://babeljs.io/docs/en/babel-plugin-transform-typescript)

> **NOTE**: This plugin is included in `@babel/preset-typescript`

This plugin adds support for the types syntax used by the [TypeScript programming language](https://www.typescriptlang.org/). However, this plugin does not add the ability to type-check the JavaScript passed to it. For that, you will need to install and set up TypeScript.

Note that although the TypeScript compiler `tsc` actively supports certain JavaScript proposals such as optional chaining (`?.`), nullish coalescing (`??`) and class properties (`this.#x`), this preset does not include these features because they are not the types syntax available in TypeScript only. We recommend using `preset-env` with `preset-typescript` if you want to transpile these features.

**In**

```typescript
const x: number = 0;
```

**Out**

```typescript
const x = 0;
```

#### babel-plugin-macros

[https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros](https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros)

#### babel-plugin-module-resolver

[https://www.npmjs.com/package/babel-plugin-module-resolver](https://www.npmjs.com/package/babel-plugin-module-resolver)

用于在使用 Babel 编译代码时为模块添加新的解析器。该插件允许您添加包含模块的新“根”目录。它还允许您为目录、特定文件甚至其他 npm 模块设置自定义别名。

### preset

通过使用或创建一个 preset 即可轻松使用一组插件。

### polyfill

**语法转换只是将高版本的语法转换成低版本的，但是新的内置函数、实例方法无法转换**。这时，就需要使用 polyfill，顾名思义，polyfill的中文意思是垫片，所谓垫片就是垫平不同浏览器或者不同环境下的差异，让新的内置函数、实例方法等在低版本浏览器中也可以使用。

很重要的点，再次强调一下

**语法转换只是将高版本的语法转换成低版本的，但是新的内置函数api、实例方法无法转换， 比如 Promise、Generator、Set、Maps、Symbol 等全局对象，一些定义在全局对象上的方法（比如 Object.assign）也不会被转码 **

#### babel-polyfill
全局垫片
**为应用而准备(业务中使用)，不是为框架准备的**
会污染全局变量

Babel 默认只转换新的 JavaScript 语法，而不转换新的 API。例如，Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign）都不会转译。如果想使用这些新的对象和方法，必须使用 babel-polyfill，为当前环境提供一个垫片。

#### babel-runtime-transform

[https://babeljs.io/docs/en/next/babel-plugin-transform-runtime](https://babeljs.io/docs/en/next/babel-plugin-transform-runtime)

局部垫片
**为开发框架而准备**
不会污染全局变量

#### babel-runtime 使用场景

参考 babel7 blog 中的 **@babel/runtime**

思考：babel-runtime 为什么适合 JavaScript 库和工具包的实现？

1. 避免 babel 编译的工具函数在每个模块里重复出现，减小库和工具包的体积；
2. 在没有使用 babel-runtime 之前，库和工具包一般不会直接引入 polyfill。否则像 Promise 这样的全局对象会污染全局命名空间，这就要求库的使用者自己提供 polyfill。这些 polyfill 一般在库和工具的使用说明中会提到，比如很多库都会有要求提供 es5 的 polyfill。在使用 babel-runtime 后，库和工具只要在 package.json 中增加依赖 babel-runtime，交给 babel-runtime 去引入 polyfill 就行了；

### core-js

[https://github.com/zloirock/core-js](https://github.com/zloirock/core-js)

[https://juejin.cn/post/6844904055005773831](https://juejin.cn/post/6844904055005773831)

利用 core-js，开发者可以在旧版本的 JavaScript 环境中使用最新的 ECMAScript 标准的特性，而无需考虑浏览器的兼容性问题。

一些 core-js 的常见用途包括：

1. 支持 ECMAScript 中新增的方法和功能，如箭头函数、类、模块、Promise、Set、Map 等。
2. 提供一致的 API，使得开发者可以在不同 JavaScript 环境中编写具有高度一致性的代码。
3. 简化代码的编写和维护，避免手动编写兼容性代码。
4. 帮助开发者逐步迁移到最新的 JavaScript 版本，降低代码迁移的难度。

总之，core-js 的主要用途是提供对 ECMAScript 标准特性的兼容性支持，使开发者可以在各种 JavaScript 环境中编写一致且具有最新特性的代码。

它[和babel紧密集成](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#Babel)：这能够优化`core-js`的导入

它是最普遍、[最流行](https://www.npmtrends.com/core-js-vs-es5-shim-vs-es6-shim-vs-airbnb-js-shims-vs-polyfill-library-vs-polyfill-service-vs-js-polyfills)给 JavaScript 标准库打补丁的方式，但是有很大一部分开发者并不知道他们间接的使用了`core-js`🙂

`babel` 和 `core-js` 是紧密集成的：`babel` 提供了优化 `core-js` 优化导入的可能性。`core-js@3` 开发中很重要的一部分是改进 `core-js` 相关的 `babel` 功能

---

`core-js` is integrated with `babel` and is the base for polyfilling-related `babel` features:

[`@babel/polyfill`](https://babeljs.io/docs/usage/polyfill) [**IS** just the import of stable `core-js` features and `regenerator-runtime`](https://github.com/babel/babel/blob/c8bb4500326700e7dc68ce8c4b90b6482c48d82f/packages/babel-polyfill/src/index.js) for generators and async functions, so if you load `@babel/polyfill` - you load the global version of `core-js` without ES proposals.

As a full equal of `@babel/polyfill`, you can use this:

```js
import 'core-js/stable';
import 'regenerator-runtime/runtime';
```

#### core-js@3

[https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md)

- `core-js` only has breaking changes in major releases, even if it is needed to reflect a change in a proposal.
- `core-js@2` entered feature freeze 1.5 years ago; all new features were added only to the `core-js@3` branch.

### regenerator-runtime

[https://www.npmjs.com/package/regenerator-runtime](https://www.npmjs.com/package/regenerator-runtime)

Standalone runtime for [Regenerator](https://github.com/facebook/regenerator)-compiled generator and `async` functions.

##### regenerator-runtime 和 core-js

regenerator-runtime 是一个由 Facebook 团队开发的专门用于支持 ECMAScript 6+ async/await、yield、generator 函数的运行时库，它是在 core-js 库的基础上构建的。

而 core-js 则是一个非常全面的 JavaScript 标准库，用于模拟 ECMAScript 最新标准中提到的新特性，比如 Promise、Map、Set、Symbol、Array.from 等，以及各种 polyfill，可以实现在旧浏览器上使用最新的语言特性。**在实现 async/await、yield、generator 函数时，需要使用 regenerator-transform 插件，它会将源代码中的 yield、async/await 等代码转换成通过 regenerator-runtime 运行的代码。**

因此，可以说 regenerator-runtime 是 core-js 库的一部分，是其在 async/await、yield、generator 函数方面的补充。

### 补充
因为babel编译es6到es5的过程中，babel-plugin-transform-runtime这个插件会自动polyfill es5不支持的特性，这些polyfill包就是在babel-runtime这个包里，**所以babel-runtime需要安装在dependency而不是devDependency**。

babel-plugin-transform-runtime和babel-runtime
字面意思就能看出来，一个是转化的包（插件），一个是充满polyfill的包。

#### 环境区分

> The env key will be taken from process.env.BABEL_ENV, when this is not available then it uses process.env.NODE_ENV if even that is not available then it defaults to "development".

env会取`process.env.BABEL_ENV`的值，如果这个变量没有设置，会取`process.env.NODE_ENV`的值，如果也没有提供，默认值是`"development"`。

#### babel-polyfill和babel-runtime

babel-polyfill解决了Babel不转换新API的问题，但是直接在代码中插入帮助函数，**会导致污染了全局环境，不建议在第三方库中使用**，并且不同的代码文件中包含重复的代码，导致编译后的代码体积变得很大

babel-runtime

```javascript
var _defineProperty2 = __webpack_require__("./node_modules/babel-runtime/helpers/defineProperty.js");

var _defineProperty3 = _interopRequireDefault(_defineProperty2);

var _assign = __webpack_require__("./node_modules/babel-runtime/core-js/object/assign.js");

var _assign2 = _interopRequireDefault(_assign);

function _interopRequireDefault(obj) { 
    return obj && obj.__esModule ? obj : { default: obj }; 
}

var key = 'babel';
var obj = (0, _defineProperty3.default)(
            {}, key, (0, _assign2.default)({}, { key: 'polyfill' })
          );

```

_defineProperty帮助函数是通过babel-runtime下的模块引用的， 同时Object.assign也变成了模块引用, 这样可以避免自行引入polyfill时导致的污染全局命名空间的问题。

#### loose-mode

[https://2ality.com/2015/12/babel6-loose-mode.html](https://2ality.com/2015/12/babel6-loose-mode.html)

Many plugins in Babel have two modes:

- A normal mode follows the semantics of ECMAScript 6 as closely as possible. (正常模式尽可能地遵循ECMAScript 6的语义。)
- A loose mode produces simpler ES5 code. (松散模式产生更简单的ES5代码。)

Normally, it is recommended to not use loose mode. The pros and cons are:

- Pros: The generated code is potentially faster and more compatible with older engines. It also tends to be cleaner, more “ES5-style”.  (优点:生成的代码可能更快，更兼容旧引擎。它也倾向于更干净，更es5风格。)
- Con: You risk getting problems later on, when you switch from transpiled ES6 to native ES6. That is rarely a risk worth taking. (缺点:当你从编译的ES6切换到原生ES6时，你可能会遇到问题。这几乎是不值得冒的风险。)

举个例子：

normal 模式 =>  通过Object.defineProperty定义对象属性

loose 模式 => 用 = 直接赋值 这种风格更像你用 ES5 手动编写代码

#### Generator

[https://www.jianshu.com/p/92639e681e2a](https://www.jianshu.com/p/92639e681e2a)

Generator 的中文名称是生成器，它是ECMAScript6中提供的新特性。在过去，封装一段运算逻辑的单元是函数。函数只存在“没有被调用”或者“被调用”的情况，不存在一个函数被执行之后还能暂停的情况，而Generator的出现让这种情况成为可能。

通过function*来定义的函数称之为“生成器函数”（generator function），它的特点是可以中断函数的执行，每次执行yield语句之后，函数即暂停执行，直到调用返回的生成器对象的next()函数它才会继续执行。

也就是说Generator 函数是一个状态机，封装了多个内部状态。执行 Generator 函数返回一个遍历器对象（一个指向内部状态的指针对象），调用遍历器对象的next方法，使得指针移向下一个状态。每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。

作者：黎贝卡beka
链接：https://www.jianshu.com/p/92639e681e2a
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

#### 项目开发配置

[https://juejin.cn/post/6984020141746946084](https://juejin.cn/post/6984020141746946084)

`useBuiltIns`使用`usage`，尽量使用社区广泛使用的优质库以优化打包体积，不使用暂未进入规范的特性。`plugin-transform-runtime`只使用其移除内联复用的辅助函数的特性，减小打包体积。

```tsx
{
  "presets": [
    [
      "@babel/preset-env",
      {
        // targets 官方推荐使用 .browserslistrc 配置
        "useBuiltIns": "usage",
        "corejs": {
          "version": 3,
          "proposals": false
        }
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": false // 默认值（所以可不写），即使如此依然需要 yarn add @babel/runtime
      }
    ]
  ]
}
```

[corejs 相关](https://github.com/zloirock/core-js#installation)

**tip： core-js 作为 dependencies**

#### 类库开发

[https://blog.meathill.com/js/babel-preset-env-and-babel-plugin-transform-runtime.html](https://blog.meathill.com/js/babel-preset-env-and-babel-plugin-transform-runtime.html)

库类项目也推荐使用 `@babel/plugin-transform-runtime`，因为库项目通常会面临另一个问题。如果我们直接导入 core-js 作 polyfill 的话，像 `Promise`，`Set`，`Map` 这样的全局对象就会被覆盖。对于一般的应用而言，问题不大；但如果是库，你无法预期其它开发者会在什么情况下使用你的库，很可能他的目标平台都支持这些新语法元素，不希望转译污染。

```tsx
{
  presets: [["@babel/preset-env"]],
  plugins: [
    [
      "@babel/plugin-transform-runtime",
      { corejs: { version: 3, proposals: true }, useESModules: true },
    ],
  ],
};
```

### 总结
* 具体项目还是需要使用 babel-polyfill，只使用 babel-runtime 的话，实例方法不能正常工作（例如 "foobar".includes("foo")）；
* JavaScript 库和工具可以使用 babel-runtime，在实际项目中使用这些库和工具，需要该项目本身提供 polyfill；

#### babel 发展规律

babel就是为了 **把目标环境中不支持的语法和 api 进行转换或 polyfill，尽量的准确、配置尽量的简单、插件更容易书写能做到更多事情**。

所以针对这个目标，babel 一路发展而来， 设计出了 preset（babel 6）、preset-env (babel 7)、polyfill provider（babel 8），plugin-transform-runtime （babel 6）等。
