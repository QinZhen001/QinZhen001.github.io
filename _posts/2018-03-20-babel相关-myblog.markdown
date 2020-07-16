---
layout:     post
title:      "babel相关"
date:       2017-07-28 22:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Babel
---

> “Yeah It's on. ”


[写一个babel插件](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md#toc-plugin-options)


# 正文


[网页链接](https://segmentfault.com/p/1210000008466178)



babel可以将当前运行平台(浏览器、node服务器)尚不支持的下一代或几代js语法编译为当前支持的js语法版本，比如可以把es6 es7和es8的js代码编译为es5的代码。



* **plugin: babel的插件，在6.x版本之后babel必需要配合插件来进行工作**
* **preset: babel插件集合的预设，包含某一部分的插件plugin**







## 插件

Babel 构建在插件之上，使用现有的或者自己编写的插件可以组成一个转换通道，Babel 的插件分为两种: 语法插件和转换插件。



### 语法插件


这些插件只允许 Babel 解析（parse） 特定类型的语法（不是转换），可以在 AST 转换时使用，以支持解析新语法，例如：

```javascript
import * as babel from "@babel/core";const code = babel.transformFromAstSync(ast, {   
//支持可选链 
plugins: ["@babel/plugin-proposal-optional-chaining"],  
babelrc: false}).code;
```


### 转换插件

转换插件会启用相应的语法插件(因此不需要同时指定这两种插件)，这点很容易理解，如果不启用相应的语法插件，意味着无法解析，连解析都不能解析，又何谈转换呢？





### 插件顺序


[https://www.babeljs.cn/docs/plugins#%E6%8F%92%E4%BB%B6%E9%A1%BA%E5%BA%8F](https://www.babeljs.cn/docs/plugins#%E6%8F%92%E4%BB%B6%E9%A1%BA%E5%BA%8F)


这意味着如果两个转换插件都将处理“程序（Program）”的某个代码片段，则将根据转换插件或 preset 的排列顺序依次执行。


* **插件在 Presets 前运行。**
* **插件顺序从前往后排列。**
* **Preset 顺序是颠倒的（从后往前）。**


例如：

```
{
  "plugins": ["transform-decorators-legacy", "transform-class-properties"]
}
```


先执行 transform-decorators-legacy ，在执行 transform-class-properties。


**重要的时，preset 的顺序是 颠倒的。如下设置：**



```
{
  "presets": ["es2015", "react", "stage-2"]
}
```


将按如下顺序执行：stage-2、react 然后是 es2015。



>这主要的是为了确保向后兼容，因为大多数用户将 "es2015" 排在 "stage-0" 之前。



### 插件的使用


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



## 常用插件



### babel-plugin-import

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













###  [babel-plugin-dynamic-import-node](https://github.com/airbnb/babel-plugin-dynamic-import-node) 



 https://github.com/airbnb/babel-plugin-dynamic-import-node 



 Babel plugin to transpile `import()` to a deferred `require()`,  





### babel-plugin-transform-remove-console

[https://www.npmjs.com/package/babel-plugin-transform-remove-console](https://www.npmjs.com/package/babel-plugin-transform-remove-console)

This plugin removes all `console.*` calls.





## config

> babel.config.js



### 区分环境



```js
module.exports = (api) => {
  // 缓存
  // https://www.babeljs.cn/docs/config-files#apiversion
  api.cache(true)
  return {
    presets: [
    // https://github.com/vuejs/vue-cli/tree/master/packages/@vue/babel-preset-app
      '@vue/cli-plugin-babel/preset'
    ], 
    'env': {
      // 开发环境  
      'development': {
      // babel-plugin-dynamic-import-node plugin only does one thing by converting all import() to require().
      // This plugin can significantly increase the speed of hot updates, when you have a large number of pages.
      // https://panjiachen.github.io/vue-element-admin-site/guide/advanced/lazy-loading.html
        'plugins': ['dynamic-import-node']
      },
      // 正式环境  
      'production': {
        plugins: [
          // 正式环境清楚console.log
          ['transform-remove-console', { exclude: ['error', 'warn'] }]
        ]
      }
    }
  }
}

```







## preset

通过使用或创建一个 preset 即可轻松使用一组插件。





### babel-preset-env


babel-preset-env 是一个新的 preset，可以根据配置的目标运行环境（environment）自动启用需要的 babel 插件。

目前我们写 javascript 代码时，需要使用 N 个 preset，比如：babel-preset-es2015、babel-preset-es2016。es2015 可以把 ES6 代码编译为 ES5，es2016 可以把 ES2016 代码编译为 ES6。babel-preset-latest 可以编译 stage 4 进度的 ECMAScript 代码。

问题是我们几乎每个项目中都使用了非常多的 preset，包括不必要的。例如很多浏览器支持 ES6 的 generator，如果我们使用 babel-preset-es2015 的话，generator 函数就会被编译成 ES5 代码。

babel-preset-env 的工作方式类似 babel-preset-latest，唯一不同的就是 babel-preset-env 会根据配置的 env 只编译那些还不支持的特性。

使用这个插件，你讲再也不需要使用 es20xx presets 了。

配置语法和 Autoprefixer 一样......


```
"babel": {
  "presets": [
    [
      "env",
      {
        "targets": {
          "browsers": ["last 2 versions", "ie >= 7"]
        }
      }
    ]
  ]
},
```



## polyfill

**语法转换只是将高版本的语法转换成低版本的，但是新的内置函数、实例方法无法转换**。这时，就需要使用 polyfill，顾名思义，polyfill的中文意思是垫片，所谓垫片就是垫平不同浏览器或者不同环境下的差异，让新的内置函数、实例方法等在低版本浏览器中也可以使用。



------



很重要的点，再次强调一下



**语法转换只是将高版本的语法转换成低版本的，但是新的内置函数api、实例方法无法转换， 比如 Promise、Generator、Set、Maps、Symbol 等全局对象，一些定义在全局对象上的方法（比如 Object.assign）也不会被转码 **






### babel-polyfill
全局垫片
**为应用而准备(业务中使用)，不是为框架准备的**
会污染全局变量

Babel 默认只转换新的 JavaScript 语法，而不转换新的 API。例如，Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign）都不会转译。如果想使用这些新的对象和方法，必须使用 babel-polyfill，为当前环境提供一个垫片。


### babel-runtime-transform

[https://babeljs.io/docs/en/next/babel-plugin-transform-runtime](https://babeljs.io/docs/en/next/babel-plugin-transform-runtime)


局部垫片
**为开发框架而准备**
不会污染全局变量


### babel-runtime 使用场景
Babel 转译后的代码要实现源代码同样的功能需要借助一些帮助函数，例如，{ [name]: 'JavaScript' } 转译后的代码如下所示：
```
'use strict';
function _defineProperty(obj, key, value) {
  if (key in obj) {
    Object.defineProperty(obj, key, {
      value: value,
      enumerable: true,
      configurable: true,
      writable: true
    });
  } else {
    obj[key] = value;
  }
  return obj;
}
var obj = _defineProperty({}, 'name', 'JavaScript');
```

类似上面的帮助函数 _defineProperty 可能会重复出现在一些模块里，导致编译后的代码体积变大。Babel 为了解决这个问题，提供了单独的包 babel-runtime 供编译模块复用工具函数。

启用插件 babel-plugin-transform-runtime 后，Babel 就会使用 babel-runtime 下的工具函数，转译代码如下：
```
'use strict';
// 之前的 _defineProperty 函数已经作为公共模块 `babel-runtime/helpers/defineProperty` 使用
var _defineProperty2 = require('babel-runtime/helpers/defineProperty');
var _defineProperty3 = _interopRequireDefault(_defineProperty2);
function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
var obj = (0, _defineProperty3.default)({}, 'name', 'JavaScript');
```

思考：babel-runtime 为什么适合 JavaScript 库和工具包的实现？

1. 避免 babel 编译的工具函数在每个模块里重复出现，减小库和工具包的体积；
2. 在没有使用 babel-runtime 之前，库和工具包一般不会直接引入 polyfill。否则像 Promise 这样的全局对象会污染全局命名空间，这就要求库的使用者自己提供 polyfill。这些 polyfill 一般在库和工具的使用说明中会提到，比如很多库都会有要求提供 es5 的 polyfill。在使用 babel-runtime 后，库和工具只要在 package.json 中增加依赖 babel-runtime，交给 babel-runtime 去引入 polyfill 就行了；



## stage

#### stage-0


它包含stage-1, stage-2以及stage-3的所有功能，同时还另外支持如下两个功能插件：

* transform-do-expressions
* transform-function-bind


#### stage-1
stage-1除了包含stage-2和stage-3，还包含了下面4个插件：


* transform-class-constructor-call (Deprecated)
* transform-class-properties
* transform-decorators – disabled pending proposal update
* transform-export-extensions



#### stage-2

stage-2它除了覆盖stage-3的所有功能，还支持如下两个插件：

* syntax-trailing-function-commas
* transform-object-reset-spread


#### stage-3
它支持大名鼎鼎的async和await


总的来说，它包含如下两个插件:

* transform-async-to-generator
* transform-exponentiation-operator








## 补充
因为babel编译es6到es5的过程中，babel-plugin-transform-runtime这个插件会自动polyfill es5不支持的特性，这些polyfill包就是在babel-runtime这个包里，**所以babel-runtime需要安装在dependency而不是devDependency**。

babel-plugin-transform-runtime和babel-runtime
字面意思就能看出来，一个是转化的包（插件），一个是充满polyfill的包。


### Generator

[https://www.jianshu.com/p/92639e681e2a](https://www.jianshu.com/p/92639e681e2a)

Generator 的中文名称是生成器，它是ECMAScript6中提供的新特性。在过去，封装一段运算逻辑的单元是函数。函数只存在“没有被调用”或者“被调用”的情况，不存在一个函数被执行之后还能暂停的情况，而Generator的出现让这种情况成为可能。


通过function*来定义的函数称之为“生成器函数”（generator function），它的特点是可以中断函数的执行，每次执行yield语句之后，函数即暂停执行，直到调用返回的生成器对象的next()函数它才会继续执行。


也就是说Generator 函数是一个状态机，封装了多个内部状态。执行 Generator 函数返回一个遍历器对象（一个指向内部状态的指针对象），调用遍历器对象的next方法，使得指针移向下一个状态。每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。

作者：黎贝卡beka
链接：https://www.jianshu.com/p/92639e681e2a
来源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。





### "modules": false



.babelrc 文件 ["env", {"modules": false}]配置是什么作用的配置？

```javascript
{
  "presets": [
    ["env", {
      "modules": false
    }],
    "stage-2"
  ],
  "plugins": ["transform-runtime"],
  "env": {
    "test": {
      "presets": ["env", "stage-2"],
      "plugins": ["transform-es2015-modules-commonjs", "dynamic-import-node"]
    }
  }
}
```


modules 字段配置 Babel 对 ES6 模块的相应处理。
Babel 默认是将ES6模块语法转化为CommonJS规范写法，配置为modules:false则不转化。


**如果使用了 Webpack 且配置为modules:false，Webpack 会进行 tree shaking，去除一些无用代码。**





## 总结
* 具体项目还是需要使用 babel-polyfill，只使用 babel-runtime 的话，实例方法不能正常工作（例如 "foobar".includes("foo")）；
* JavaScript 库和工具可以使用 babel-runtime，在实际项目中使用这些库和工具，需要该项目本身提供 polyfill；




