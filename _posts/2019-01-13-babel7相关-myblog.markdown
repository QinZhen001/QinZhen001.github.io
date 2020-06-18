---
layout:     post
title:      "babel7相关"
date:       2019-04-16 19:44:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Babel
---

> “Yeah It's on. ”


## 正文
[https://www.jianshu.com/p/cbd48919a0cc](https://www.jianshu.com/p/cbd48919a0cc)


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
```
$ babel test.js
```
使用@babel/node编译
```
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

### @babel/preset-env

[ https://jdc.jd.com/archives/212962 ]( https://jdc.jd.com/archives/212962 )




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



>Without any configuration options, babel-preset-env behaves exactly the same as babel-preset-latest (or babel-preset-es2015, babel-preset-es2016, and babel-preset-es2017 together).


**第一个问题是这个：**

```javascript
{
 "targets": {
      "node": "4"
  }
}
```


这个targets实际上是针对上面的@babel/preset-env这个插件包的一个配置参数，它所代表的是你编译代码所针对的目标平台，我们这里的目标是版本号为4的node(友情提示：node -v  命令可以检查node的版本)，也就是我编译之后的代码能够在node版本号为4的环境下运行，同样大家可以做个试验，如果将node这个4改为6，再次编译，你会发现编译之后的代码和编译之前的代码没有任何变化，这表明原始的代码实际上已经可以直接在版本为6的node上直接运行，不需要babel的编译了。

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
>
>


>我对polyfill的理解：polyfill我们又称垫片，见名知意，所谓垫片也就是垫平不同浏览器或者不同环境下的差异，因为有的环境支持这个函数，有的环境不支持这种函数，解决的是有与没有的问题，这个是靠单纯的@babel/preset-env不能解决的，因为@babel/preset-env解决的是将高版本写法转化成低版本写法的问题，因为不同环境下低版本的写法有可能不同而已。



------





####  useBuiltIns 



`@babel/preset-env` 与按需加载 polyfill 相关的选项是 `useBuiltIns`，它有两个值需要重点关注： `entry` 和 `usage`。



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



-----



 https://www.babeljs.cn/docs/babel-preset-env#modules 




 



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
















### @babel/plugin-transform-runtime

@babel/plugin-transform-runtime的作用是提供统一的模块化的helper，那什么是helper，我们举个例子：


我们编译之后的index.js代码里面有不少新增加的函数，如_classCallCheck，_defineProperties，_createClass，这种函数就是helper。

那这种helper跟我们的@babel/runtime有什么关系了，我们接着看，比如像这个_createClass就是我们将es6的class关键字转化成传统js时生成的一个函数，那么如果我有很多个js文件中都定义了class类，那么在编译转化时就会产生大量相同的_createClass方法，那这些_createClass这样的helper方法是不是冗余太多，因为它们基本都是一样的，所以我们能不能采用一个统一的方式提供这种helper，也就是利用es或者node的模块化的方式提供helper，将这些helper做成一个模块来引入到代码中，岂不是可以减少这些helper函数的重复书写。


那我们现在就
```
npm install --save @babel/runtime @babel/plugin-transform-runtime
```
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
[
    "@babel/preset-env"     
]   ],    
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




### @babel/plugin-proposal-decorators



[https://babeljs.io/docs/en/babel-plugin-proposal-decorators](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)











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

```
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

```
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



>这里又坑了好久


最终这个插件还是没办法正常使用


换成babel6，使用babel6的插件也不行。


使用gulp-remove-use-strict也不行。

----

**最终解决办法：**


**由于一些原因还是使用了babel6，增加了配置"modules": false**

[https://segmentfault.com/q/1010000013533162](https://segmentfault.com/q/1010000013533162)

```javascript
{
  presets: [
    ["env", { "modules": false }]
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








