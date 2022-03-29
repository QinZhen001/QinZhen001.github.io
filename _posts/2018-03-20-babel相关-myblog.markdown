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

[https://juejin.im/post/6844903602822053895#heading-3](https://juejin.im/post/6844903602822053895#heading-3)



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



### dynamic-import-node

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



注意：

**preset-env会转换语法，也就是我们看到的箭头函数、const一类。**
**如果进一步需要转换内置对象、实例方法，那就得用polyfill, 这就需要你做一点配置了**









### useBuiltIns

[https://www.babeljs.cn/docs/babel-preset-env#usebuiltins](https://www.babeljs.cn/docs/babel-preset-env#usebuiltins)

preset-env 中的 useBuiltIns 配置

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry"
      }
    ]
  ]
}
```

`"usage"` | `"entry"` | `false`, defaults to `false`.

This option configures how `@babel/preset-env` handles polyfills.

When either the `usage` or `entry` options are used, `@babel/preset-env` will add direct references to `core-js` modules as bare imports (or requires). This means `core-js` will be resolved relative to the file itself and needs to be accessible.

Since `@babel/polyfill` was deprecated in 7.4.0, we recommend directly adding `core-js` and setting the version via the [`corejs`](https://www.babeljs.cn/docs/babel-preset-env#corejs) option.





#### useBuiltIns: 'entry'

This option enables a new plugin that replaces the `import "core-js/stable";` and `import "regenerator-runtime/runtime"` statements (or `require("core-js")` and `require("regenerator-runtime/runtime")`) with individual requires to different `core-js` entry points based on environment.

提供一个新插件代替core-js/stable 和 regenerator-runtime/runtime



根据配置的浏览器兼容，引入浏览器不兼容的 `polyfill`。需要在入口文件手动添加 `import '@babel/polyfill'`，会自动根据 `browserslist` 替换成浏览器不兼容的所有 `polyfill`。



<details open="" style="box-sizing: border-box; border: 0px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-variant-numeric: inherit; font-variant-east-asian: inherit; font-weight: 400; font-stretch: inherit; line-height: inherit; font-family: -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;, &quot;Segoe UI Symbol&quot;; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline; color: rgb(36, 41, 46); letter-spacing: normal; orphans: 2; text-align: left; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"><div id="details-content"><table style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px 0px 16px; padding: 0px; vertical-align: baseline; border-collapse: collapse; border-spacing: 0px; display: block; overflow: auto; width: 697px;"><thead style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline;"><tr style="box-sizing: border-box; border-width: 1px 0px 0px; border-top-style: solid; border-right-style: initial; border-bottom-style: initial; border-left-style: initial; border-top-color: rgb(223, 226, 229); border-right-color: initial; border-bottom-color: initial; border-left-color: initial; border-image: initial; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline; background-color: transparent;"><th style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: 600; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; background-color: inherit; color: inherit;">Version</th><th style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: 600; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; background-color: inherit; color: inherit;">Changes</th></tr></thead><tbody style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline;"><tr style="box-sizing: border-box; border-width: 1px 0px 0px; border-top-style: solid; border-right-style: initial; border-bottom-style: initial; border-left-style: initial; border-top-color: rgb(223, 226, 229); border-right-color: initial; border-bottom-color: initial; border-left-color: initial; border-image: initial; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline; background-color: transparent;"><td style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; color: inherit;"><code style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: SFMono-Regular, Menlo, Monaco, Consolas, &quot;Liberation Mono&quot;, &quot;Courier New&quot;, monospace; font-size: 13.6px; margin: 0px; padding: 3.2px 6.4px; vertical-align: baseline; background-color: rgba(27, 31, 35, 0.05); border-radius: 3px; color: rgb(57, 57, 57); word-break: normal;">v7.4.0</code></td><td style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; color: inherit;">It replaces<span>&nbsp;</span><code style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: SFMono-Regular, Menlo, Monaco, Consolas, &quot;Liberation Mono&quot;, &quot;Courier New&quot;, monospace; font-size: 13.6px; margin: 0px; padding: 3.2px 6.4px; vertical-align: baseline; background-color: rgba(27, 31, 35, 0.05); border-radius: 3px; color: rgb(57, 57, 57); word-break: normal;">"core-js/stable"</code><span>&nbsp;</span>and<span>&nbsp;</span><code style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: SFMono-Regular, Menlo, Monaco, Consolas, &quot;Liberation Mono&quot;, &quot;Courier New&quot;, monospace; font-size: 13.6px; margin: 0px; padding: 3.2px 6.4px; vertical-align: baseline; background-color: rgba(27, 31, 35, 0.05); border-radius: 3px; color: rgb(57, 57, 57); word-break: normal;">"regenerator-runtime/runtime"</code><span>&nbsp;</span>entry imports</td></tr><tr style="box-sizing: border-box; border-width: 1px 0px 0px; border-top-style: solid; border-right-style: initial; border-bottom-style: initial; border-left-style: initial; border-top-color: rgb(223, 226, 229); border-right-color: initial; border-bottom-color: initial; border-left-color: initial; border-image: initial; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 0px; vertical-align: baseline; background-color: rgb(246, 248, 250);"><td style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; color: inherit;"><code style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: SFMono-Regular, Menlo, Monaco, Consolas, &quot;Liberation Mono&quot;, &quot;Courier New&quot;, monospace; font-size: 13.6px; margin: 0px; padding: 3.2px 6.4px; vertical-align: baseline; background-color: rgba(27, 31, 35, 0.05); border-radius: 3px; color: rgb(57, 57, 57); word-break: normal;">v7.0.0</code></td><td style="box-sizing: border-box; border: 1px solid rgb(223, 226, 229); font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px; padding: 6px 13px; vertical-align: baseline; color: inherit;">It replaces<span>&nbsp;</span><code style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: SFMono-Regular, Menlo, Monaco, Consolas, &quot;Liberation Mono&quot;, &quot;Courier New&quot;, monospace; font-size: 13.6px; margin: 0px; padding: 3.2px 6.4px; vertical-align: baseline; background-color: rgba(27, 31, 35, 0.05); border-radius: 3px; color: rgb(57, 57, 57); word-break: normal;">"@babel/polyfill"</code><span>&nbsp;</span>entry imports</td></tr></tbody></table><p style="box-sizing: border-box; border: 0px; font-style: inherit; font-variant: inherit; font-weight: inherit; font-stretch: inherit; line-height: inherit; font-family: inherit; font-size: 16px; margin: 0px 0px 1em; padding: 0px; vertical-align: baseline;"></p></div></details>




**In**

```js
import "core-js";
```

**Out (different based on environment)**

```js
import "core-js/modules/es.string.pad-start";
import "core-js/modules/es.string.pad-end";
```





#### useBuiltIns: 'usage'

Adds specific imports for polyfills when they are used in each file. We take advantage of the fact that a bundler will load the same polyfill only once.

为每一个文件添加特定的import，这样只会填充一次相同的依赖



**In**

a.js

```js
var a = new Promise();
Copy
```

b.js

```js
var b = new Map();
Copy
```

**Out (if environment doesn't support it)**

a.js

```js
import "core-js/modules/es.promise";
var a = new Promise();
Copy
```

b.js

```js
import "core-js/modules/es.map";
var b = new Map();
Copy
```

**Out (if environment supports it)**

a.js

```js
var a = new Promise();
Copy
```

b.js

```js
var b = new Map();
```



#### useBuiltIns: false

Don't add polyfills automatically per file, and don't transform `import "core-js"` or `import "@babel/polyfill"` to individual polyfills.





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





## core-js

[https://juejin.cn/post/6844904055005773831](https://juejin.cn/post/6844904055005773831)

- **它是JavaScript标准库的 polyfill，它支持**
  - 最新的 [ECMAScript](https://en.wikipedia.org/wiki/ECMAScript) 标准
  - ECMAScript 标准库提案
  - 一些 [WHATGW](https://en.wikipedia.org/wiki/WHATWG) / [W3C](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium) 标准（跨平台或者 ECMAScript 相关）

- 它最大限度的模块化：你能仅仅加载你想要使用的功能
- 它能够不污染全局命名空间
- 它[和babel紧密集成](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md#Babel)：这能够优化`core-js`的导入



它是最普遍、[最流行](https://www.npmtrends.com/core-js-vs-es5-shim-vs-es6-shim-vs-airbnb-js-shims-vs-polyfill-library-vs-polyfill-service-vs-js-polyfills)给 JavaScript 标准库打补丁的方式，但是有很大一部分开发者并不知道他们间接的使用了`core-js`🙂



`babel` 和 `core-js` 是紧密集成的：`babel` 提供了优化 `core-js` 优化导入的可能性。`core-js@3` 开发中很重要的一部分是改进 `core-js` 相关的 `babel` 功能



---

[`@babel/polyfill`](https://babeljs.io/docs/en/next/babel-polyfill.html) 是一个包裹的包，里面仅仅包含 `core-js` 稳定版的引入（在Babel 6 中也包含提案）和 `regenerator-runtime/runtime`，用来转译 generators 和 async 函数。这个包没有提供从 `core-js@2` 到 `core-js@3` 平滑升级路径：因为这个原因，决定弃用 `@babel/polyfill` 代之以分别引入需要的 `core-js` 和 `regenerator-runtime` 。



原来

```js
import "@babel/polyfill";
```

现在使用两行代替：

```js
import "core-js/stable";
import "regenerator-runtime/runtime";
```





### core-js@3

[https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md](https://github.com/zloirock/core-js/blob/master/docs/2019-03-19-core-js-3-babel-and-a-look-into-the-future.md)

- `core-js` only has breaking changes in major releases, even if it is needed to reflect a change in a proposal.
- `core-js@2` entered feature freeze 1.5 years ago; all new features were added only to the `core-js@3` branch.









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



### 环境区分

> The env key will be taken from process.env.BABEL_ENV, when this is not available then it uses process.env.NODE_ENV if even that is not available then it defaults to "development".

env会取`process.env.BABEL_ENV`的值，如果这个变量没有设置，会取`process.env.NODE_ENV`的值，如果也没有提供，默认值是`"development"`。





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





### 项目开发配置

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
        "corejs": false // 默认值，即使如此依然需要 yarn add @babel/runtime
      }
    ]
  ]
}
```


### 类库开发

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









## 总结
* 具体项目还是需要使用 babel-polyfill，只使用 babel-runtime 的话，实例方法不能正常工作（例如 "foobar".includes("foo")）；
* JavaScript 库和工具可以使用 babel-runtime，在实际项目中使用这些库和工具，需要该项目本身提供 polyfill；

### babel 发展规律

babel就是为了 **把目标环境中不支持的语法和 api 进行转换或 polyfill，尽量的准确、配置尽量的简单、插件更容易书写能做到更多事情**。

所以针对这个目标，babel 一路发展而来， 设计出了 preset（babel 6）、preset-env (babel 7)、polyfill provider（babel 8），plugin-transform-runtime （babel 6）等。

