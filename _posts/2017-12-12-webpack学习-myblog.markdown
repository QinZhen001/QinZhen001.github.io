---
layout:     post
title:      "webpack学习"
date:       2017-12-12 14:34:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Webpack
---

> “Yeah It's on. ”


## 基础


[网页链接](http://www.jianshu.com/p/42e11515c10f)

[webpack从入门到工程实践](https://github.com/zhangwang1990/blogs/blob/master/articles/webpack%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%B7%A5%E7%A8%8B%E5%AE%9E%E8%B7%B5.md)

[https://webpack.toobug.net/zh-cn/](https://webpack.toobug.net/zh-cn/)

[深入理解 webpack 文件打包机制](https://zhuanlan.zhihu.com/p/32706935)



### 什么是Webpack
WebPack可以看做是**模块打包机**：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。


### WebPack和Grunt以及Gulp相比有什么特性
其实Webpack和另外两个并没有太多的可比性，Gulp/Grunt是一种能够优化前端的开发流程的工具，而WebPack是一种模块化的解决方案，不过Webpack的优点使得Webpack在很多场景下可以替代Gulp/Grunt类的工具。

Grunt和Gulp的工作方式是：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务。


Webpack的工作方式是：把你的项目当做一个整体，通过一个给定的主文件（如：index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。

如果实在要把二者进行比较，Webpack的处理速度更快更直接，能打包更多不同类型的文件。



## Webpack的强大功能

### 生成Source Maps（使调试更容易）

开发总是离不开调试，方便的调试能极大的提高开发效率，不过有时候通过打包后的文件，你是不容易找到出错了的地方，对应的你写的代码的位置的，Source Maps就是来帮我们解决这个问题的。

通过简单的配置，webpack就可以在打包时为我们生成的source maps，这为我们提供了一种对应编译文件和源文件的方法，使得编译后的代码可读性更高，也更容易调试。

在webpack的配置文件中配置`source maps`，需要配置`devtool`，它有以下四种不同的配置选项，各具优缺点，描述如下：

| devtool选项                  | 配置结果                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| source-map                   | 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包速度； |
| cheap-module-source-map      | 在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便； |
| eval-source-map              | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。**在开发阶段这是一个非常好的选项，在生产阶段则一定不要启用这个选项；** |
| cheap-module-eval-source-map | 这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点； |

正如上表所述，上述选项由上到下打包速度越来越快，不过同时也具有越来越多的负面作用，较快的打包速度的后果就是对打包后的文件的的执行有一定影响。

对小到中型的项目中，eval-source-map是一个很好的选项，**再次强调你只应该开发阶段使用它**

>cheap-module-eval-source-map方法构建速度更快，但是不利于调试，推荐在大型项目考虑时间成本时使用。


### 使用webpack构建本地服务器

想不想让你的浏览器监听你的代码的修改，并自动刷新显示修改后的结果，其实Webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现你想要的这些功能，不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖

>npm install --save-dev webpack-dev-server

devserver作为webpack配置选项中的一项，以下是它的一些配置选项，更多配置可参考

| devserver的配置选项 | 功能描述                                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| contentBase         | 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录） |
| port                | 设置默认监听端口，如果省略，默认为”8080“                                                                                                        |
| inline              | 设置为true，当源文件改变时会自动刷新页面                                                                                                          |
| historyApiFallback  | 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html                                                   |



### Bundle VS Chunk VS Module

[https://segmentfault.com/a/1190000015919928](https://segmentfault.com/a/1190000015919928)

首先对于“模块”(module)的概念相信大家都没有异议，它指的就是我们在编码过程中有意识的封装和组织起来的代码片段。狭义上我们首先联想到的是碎片化的 React 组件，或者是 CommonJS 模块又或者是 ES6 模块，但是对 Webpack 和 Loader 而言，广义上的模块还包括样式和图片，甚至说是不同类型的文件



而“包”(bundle) 就是把相关代码都打包进入的单个文件。如果你不想把所有的代码都放入一个包中，你可以把它们划分为多个包，也就是“块”(chunk) 中。从这个角度上看，“块”等于“包”，它们都是对代码再一层的组织和封装。如果必须要给一个区分的话，通常我们在讨论时，bundle 指的是所有模块都打包进入的单个文件，而 chunk 指的是按照某种规则的模块集合，chunk 的体积大于单个模块，同时小于整个 bundle



（但如果要仔细的深究，**Chunk**是 Webpack 用于管理打包流程中的技术术语，甚至能划分为不同类型的 chunk。我想我们不用从这个角度理解。只需要记住上一段的定义即可）



作者：李熠
链接：https://juejin.im/post/6844903846993461256
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



----



- chunk: 是指代码中引用的文件（如：js、css、图片等）会根据配置合并为一个或多个包，我们称一个包为 chunk。
- module: 是指将代码按照功能拆分，分解成离散功能块。拆分后的代码块就叫做 module。可以简单的理解为一个 export/import 就是一个 module。



**每个 chunk 包可含多个 module。** 

 比如：

```js
//9.xxxxxxxxx.js

//chunk id为 9 ，包含了Vc2m和JFUb两个module
(window.webpackJsonp = window.webpackJsonp || []).push([
  [9],
  {
    Vc2m: function(e, t, l) {},
    JFUb: function(e, t, l) {}
  }
]);
```





### filename VS chunkname

> 简单说明一下： `filename` 是指在你入口文件`entry`中引入生成出来的文件名，而`chunkname`是指那些未被在入口文件`entry`引入，但又通过按需加载（异步）模块的时候引入的文件。





### hash VS chunkhash  VS contenthash

[https://www.cnblogs.com/heyushuo/p/8543889.html](https://www.cnblogs.com/heyushuo/p/8543889.html)

项目发布时，为了解决缓存，需要进行md5签名



* **hash** 

  `hash` 和每次 `build`有关，没有任何改变的情况下，每次编译出来的 `hash`都是一样的，但当你改变了任何一点东西，它的`hash`就会发生改变。**简单理解，你改了任何东西，`hash` 就会和上次不一样了。**

* **chunkhash**

  `chunkhash`是根据具体每一个模块文件自己的的内容包括它的依赖计算所得的`hash`，**所以某个文件的改动只会影响它本身的`chunkhash`，不会影响其它文件。**

* **contenthash**

  它的出现主要是为了解决，让`css`文件不受`js`文件的影响。比如`foo.css`被`foo.js`引用了，所以它们共用相同的`chunkhash`值。但这样子是有问题的，如果`foo.js`修改了代码，`css`文件就算内容没有任何改变，由于是该模块的 `hash` 发生了改变，其`css`文件的`hash`也会随之改变。

这个时候我们就可以使用`contenthash`了，保证即使`css`文件所处的模块里有任何内容的改变，只要 css 文件内容不变，那么它的`hash`就不会发生变化。



**`contenthash` 你可以简单理解为是 `moduleId` + `content` 所生成的 `hash`。**





> **hash 字段是根据每次编译compilation的内容计算所得，也可以理解为项目总体文件的hash值，而不是针对每个具体文件的。(所以每一次编译都会有一个新的hash)**



`hash` generates some sort of global hash for all the assets we built, while `chunkhash` will only hash whatever is in its chunk.



#### 图片和字体图标的chunkhash问题

前面有提到，hash在js和css中不实用，所以在项目中所有的文件都准备用 chunkhash ，但是又有了新的问题-img和font等资源中，使用 chunkhash 会报错



**解决:因为 chunkhash 只适用于js和css，img中是没有这种东西的，仍然需要用到 hash (这个hash有点区别，每一个资源本身有自己的hash)**



#### chunkhash重复问题

打包时发现，js和js引入的css的 chunkhash 是相同的，导致无法区分css和js的更新，如下


```javascript
　　index2-ddcf83c3b574d7c94a42.css
　　index2-ddcf83c3b574d7c94a42.js
```


原因是因为webpack的编译理念，webpack将css视为js的一部分，所以在计算chunkhash时，会把所有的js代码和css代码混合在一起计算 

**解决:css是使用 ExtractTextPlugin 插件引入的，这时候可以使用到这个插件提供的 contenthash ，如下(使用后css就有独立于js外的指纹了)**，


```javascript
//提取css文件
new ExtractTextPlugin({
     filename:'css/[name].[chunkhash:8].css'  //提取chunkhash8位码
})
```







### watch

```javascript
 "scripts": {
    ...
    "watch": "webpack --watch"
  },
```



这样我们就可以使用npm run watch来调用 node_modules/.bin/webpack --watch了。


命令执行完之后,它会监控源码文件，然后只对改变的文件进行重编译。

>另外，npm run build，调用了node_modules/.bin/webpack，它是一个软链接，





### Loaders
Loaders是webpack提供的最激动人心的功能之一了。通过使用不同的loader，webpack有能力调用外部的脚本或工具，实现对不同格式的文件的处理，比如说分析转换scss为css，或者把下一代的JS文件（ES6，ES7)转换为现代浏览器兼容的JS文件，对React的开发而言，合适的Loaders可以把React的中用到的JSX文件转换为JS文件。

Loaders需要单独安装并且需要在webpack.config.js中的modules关键字下进行配置，Loaders的配置包括以下几方面：


* test：一个用以匹配loaders所处理文件的拓展名的正则表达式（必须）
* loader：loader的名称（必须）
* include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
* query：为loaders提供额外的设置选项（可选）



### 一切皆模块

Webpack有一个不可不说的优点，它把所有的文件都都当做模块处理，JavaScript代码，CSS和fonts以及图片等等通过合适的loader都可以被处理。

### 处理css
webpack提供两个工具处理样式表，css-loader 和 style-loader，二者处理的任务不同，css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能,style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

>// 安装
>npm install --save-dev style-loader css-loader

```
//使用
module.exports = {

   ...
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader"
                    }
                ]
            }
        ]
    }
};
```

>通常情况下，css会和js打包到同一个文件中，并不会打包为一个单独的css文件，不过通过合适的配置webpack也可以把css打包为单独的文件的。

### CSS预处理器
Sass 和 Less 之类的预处理器是对原生CSS的拓展，它们允许你使用类似于variables, nesting, mixins, inheritance等不存在于CSS中的特性来写CSS，CSS预处理器可以这些特殊类型的语句转化为浏览器可识别的CSS语句，

* Less Loader
* Sass Loader
* Stylus Loader

我们使用PostCSS来为CSS代码自动添加适应不同浏览器的CSS前缀。

首先安装postcss-loader 和 **autoprefixer（自动添加前缀的插件）**

>npm install --save-dev postcss-loader autoprefixer

### 插件（Plugins）
插件（Plugins）是用来拓展Webpack功能的，它们会在整个构建过程中生效，执行相关的任务。
Loaders和Plugins常常被弄混，但是他们其实是完全不同的东西，可以这么来说，loaders是在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个，插件并不直接操作单个文件，它直接对整个构建过程其作用。

Webpack有很多内置插件，同时也有很多第三方插件，可以让我们完成更加丰富的功能。

#### 使用插件的方法
要使用某个插件，我们需要通过npm安装它，然后要做的就是在webpack配置中的plugins关键字部分添加该插件的一个实例（plugins是一个数组）
```
const webpack = require('webpack');

module.exports = {
...
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader",
                        options: {
                            modules: true
                        }
                    }, {
                        loader: "postcss-loader"
                    }
                ]
            }
        ]
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究')
    ],
};
```

#### 优化插件
webpack提供了一些在发布阶段非常有用的优化插件，它们大多来自于webpack社区，可以通过npm安装，通过以下插件可以完成产品发布阶段所需的功能

* OccurenceOrderPlugin:为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID
* UglifyJsPlugin：压缩JS代码；
* ExtractTextPlugin：分离CSS和JS文件

#### 缓存
缓存无处不在，使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）

webpack可以把一个哈希值添加到打包的文件名中，使用方法如下,添加特殊的字符串混合体（[name], [id] and [hash]）到输出文件名前
```
onst webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
..
    output: {
        path: __dirname + "/build",
        filename: "bundle-[hash].js"
    },
   ...
```



### Scope-Hoisting

[https://segmentfault.com/a/1190000017953060](https://segmentfault.com/a/1190000017953060)

作用域提升，尽可能的把打散的模块合并到一个函数中，前提是不能造成代码冗余。**因此只有那些被引用了一次的模块才能被合并。**



可能不好理解，下面demo对比一下有无Scope-Hoisting的编译结果。

首先定义一个util.js文件

```js
export default 'Hello,Webpack';
```

然后定义入口文件main.js

```js
import str from './util.js'
console.log(str);
```

下面是无Scope-Hoisting结果：

![](https://s1.ax1x.com/2020/10/27/BQ5JzR.png)

然后是Scope-Hoisting后的结果：

![](https://s1.ax1x.com/2020/10/27/BQ5GW9.png)

>由于 Scope Hoisting 需要分析出模块之间的依赖关系，因此源码**「必须采用 ES6 模块化语句」**，不然它将无法生效。



在 webpack 的 `mode` 设置为 `production` 时，会默认自动启用 Scope Hoisting





### Splitting



#### Code Splitting

[https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE](https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE)


我们把所有代码分成一块一块，需要某块代码的时候再去加载它；利用浏览器的缓存，下次用到它的话，直接从缓存中读取。很显然，这种做法可以加快我们网页的加载速度。


**所以说，Code Splitting 其实就是把代码分成很多很多块（ chunk ）**



Code Splitting 主要有 2 种方式：
1. 分离业务代码和第三方库（ vendor ）
2. 按需加载（利用 import() 语法）

之所以把业务代码和第三方库代码分离出来，是因为产品经理的需求是源源不断的，因此业务代码更新频率大，相反第三方库代码更新迭代相对较慢且可以锁版本，所以可以充分利用浏览器的缓存来加载这些第三方库。

而按需加载的适用场景，比如说「访问某个路由的时候再去加载对应的组件」，用户不一定会访问所有的路由，所以没必要把所有路由对应的组件都先在开始的加载完；更典型的例子是「某些用户他们的权限只能访问某些页面」，所以没必要把他们没权限访问的页面的代码也加载。



#### Bundle splitting

打包分离 (Bundle splitting)：为了更好的缓存创建更多、更小的文件（但仍然以每一个文件一个请求的方式进行加载）



打包分离背后的思想非常简单。如果你有一个体积巨大的文件，并且只改了一行代码，用户仍然需要重新下载整个文件。但是如果你把它分为了两个文件，那么用户只需要下载那个被修改的文件，而浏览器则可以从缓存中加载另一个文件。

**值得注意的是因为打包分离与缓存相关，所以对站点的首次访问者来说没有区别**







### Runtime

runtime，以及伴随的 manifest 数据，主要是指：**在浏览器运行时，webpack 用来连接模块化的应用程序的所有代码。**runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。




### Manifest

那么，一旦你的应用程序中，形如 index.html 文件、一些 bundle 和各种资源加载到浏览器中，会发生什么？你精心安排的 /src 目录的文件结构现在已经不存在，所以 webpack 如何管理所有模块之间的交互呢？这就是 manifest 数据用途的由来……

当编译器(compiler)开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "Manifest"，当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块。无论你选择哪种模块语法，那些 import 或 require 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。



#### RuntimeChunk

webpack 4 提供了 runtimeChunk 能让我们方便的提取 `manifest`，以前我们需要这样配置

```js
// webpack3的时候

new webpack.optimize.CommonsChunkPlugin({
  name: "manifest",
  minChunks: Infinity
});
```

现在只要一行配置就可以了 (在optimization中)

```js
{
  runtimeChunk: true;
}
```

或者指定具体名字

```js
runtimeChunk: {
  name: 'runtime'
}
```

它的作用是将包含`chunks 映射关系的 list`单独从 `app.js`里提取出来，因为每一个 chunk 的 id 基本都是基于内容 hash 出来的，所以你每次改动都会影响它，如果不将它提取出来的话，等于`app.js`每次都会改变。缓存就失效了。







### output



#### libraryTarget

[https://webpack.js.org/configuration/output/#expose-via-object-assignment](https://webpack.js.org/configuration/output/#expose-via-object-assignment)



* libraryTarget: 'amd' - 这会将您的库作为AMD模块公开。
* libraryTarget: 'umd' - 这会在所有模块定义下公开您的库，允许它与CommonJS，AMD和全局变量一起使用



可以使用library属性来命名模块：

```javascript
module.exports = {
  //...
  output: {
    library: 'MyLibrary',
    libraryTarget: 'umd'
  }
};
```

最后输出是：



```javascript
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
    module.exports = factory();
  else if(typeof define === 'function' && define.amd)
    define([], factory);
  else if(typeof exports === 'object')
    exports['MyLibrary'] = factory();
  else
    root['MyLibrary'] = factory();
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_;
});
```

用户应该能够通过以下方式访问 library：



* ES2015 模块。例如 import webpackNumbers from 'webpack-numbers'。
* CommonJS 模块。例如 require('webpack-numbers').
* 全局变量，当通过 script 脚本引入时



---


AMD模块要求`<script>`使用特定属性定义条目块（例如，由标签加载的第一个脚本），例如define并且require通常由RequireJS或任何兼容的加载器（例如杏仁）提供。否则，直接加载生成的AMD包将导致错误，如define is not defined。

所以，通过以下配置......
```
module.exports = {
  //...
  output: {
    library: 'MyLibrary',
    libraryTarget: 'amd'
  }
};
```
生成的输出将使用名称“MyLibrary”定义，即
```
define('MyLibrary', [], function() {
  return _entry_return_;
});
```

---





#### libraryExport

[https://www.webpackjs.com/configuration/output/#output-libraryexport](https://www.webpackjs.com/configuration/output/#output-libraryexport)

`string` or `string[]` (since webpack 3.0.0)

> Default: `_entry_return_`



上面的默认值会导致一个小问题

[https://mp.weixin.qq.com/s/uAhqYgfFTrTfkiCicPmHpg](https://mp.weixin.qq.com/s/uAhqYgfFTrTfkiCicPmHpg)





Configure which module or modules will be exposed via the libraryTarget. The default entry return value is the namespace or default module returned by your entry file. The examples below demonstrate the effect of this config when using libraryTarget: "var", but any target may be used.

配置哪些或哪些模块将通过libraryTarget公开。默认条目返回值是条目文件返回的名称空间或默认模块。下面的例子演示了这个配置在使用libraryTarget: "var"时的效果，但是任何目标都可以使用。



---



例子：

`libraryExport: "default"` - The **default export of your entry point** will be assigned to the library target:

```js
// if your entry has a default export of `MyDefaultModule`
var MyDefaultModule = _entry_return_.default;
```





`libraryExport: "MyModule"` - The **specified module** will be assigned to the library target:

```js
var MyModule = _entry_return_.MyModule;
```











### context

Webpack 在寻找相对路径的文件时会以 context 为根目录，context 默认为执行启动 Webpack 时所在的当前工作目录。 如果想改变 context 的默认配置，则可以在配置文件里这样设置它：


```javascript
1 module.exports = {
2   context: path.resolve(__dirname, 'app')
3 }
```



**Entry 的路径和其依赖的模块的路径可能采用相对于 context 的路径来描述，context 会影响到这些相对路径所指向的真实文件**。



### external


[详细的讲解](https://www.tangshuang.net/3343.html)




[https://segmentfault.com/a/1190000013641662?utm_source=tag-newest](https://segmentfault.com/a/1190000013641662?utm_source=tag-newest)



这篇文章讨论Webpack打包library时经常需要用到的一个选项`external`，它用于避免将一些很通用的模块打包进你发布的library里，而是选择把它们声明成external的模块，在你的library被上层使用后，在最后阶段由Webpack统一把这个external的依赖模块打包进来。





`external`选项一般都是用在打包library上面，如果不是library而是一个最终的app的发布JS文件，那external也没有什么意义





定义一个库`util.js`：

```javascript
import $ from 'jquery'

function hideImages() {
  $('img').hide();
}

export default {
  "hideImages": hideImages
}
```

我们使用Webpack打包发布这个库：

```javascript
// 入口文件
entry: {
  util: './util.js',
}

// 输出文件
output: {
  path: './dist',
  filename: '[name].dist.js'

  library: 'util',
  libraryTarget: commonjs2,
  targetExport: 'default'
}
```



这样打包出来的`util.dist.js`文件会把`jquery`的代码完整地注入进去，因为你的源代码使用到了它。但是这往往并不是我们希望的，因为`jquery`是很通用的模块，在一个app中，很可能其它的库也会用到它，最顶层的入口文件app也可能用到它，如果每一个库模块的发布版本都将jquery原封不动地打包进了自己的bundle，最后拼到一起，在最终的app发布代码里就会有很多份jquery的复制，当然这可能并不会影响它的正常功能，但是会占据很大的代码体积。



所以通常情况下当你的库需要依赖到例如`jquery`，`bootstrap`这样的通用JS模块时，我们可以不将它打包进bundle，而是在Webpack的配置中声明`external`：

```javascript
externals: {
  jquery: {
    root: 'jquery',
    commonjs: 'jquery',
    commonjs2: 'jquery',
    amd: 'jquery',
  },
},
```

这就是在告诉Webpack：**请不要将这个模块注入编译后的JS文件里，对于我源代码里出现的任何`import/require`这个模块的语句，请将它保留**。



###  

对于external的依赖模块，通常你可以这样做，例如你使用npm发布你的库，你可以将jquery在`package.json`文件中添加到`dependencies`，这样别人`npm install`你发布的库时，jquery也会被自动下载到node_modules供别人打包使用。



















模块热替换

[https://webpack.docschina.org/api/hot-module-replacement](https://webpack.docschina.org/api/hot-module-replacement)


如果已经通过 HotModuleReplacementPlugin 启用了模块热替换(Hot Module Replacement)，则它的接口将被暴露在 module.hot 属性下面。通常，用户先要检查这个接口是否可访问，然后再开始使用它。举个例子，你可以这样 accept 一个更新的模块：

```javascript
if (module.hot) {
  module.hot.accept('./library.js', function() {
    // 使用更新过的 library 模块执行某些操作...
  });
}
```





###  optimization 





####  chunkIds 

```js
boolean = false
```

```js
string: 'natural' | 'named' | 'size' | 'total-size' | 'deterministic'
```



Tells webpack which algorithm to use when choosing chunk ids. Setting optimization.chunkIds to false tells webpack that none of built-in algorithms should be used, as custom one can be provided via plugin. There are couple of defaults for optimization.chunkIds



告诉webpack在选择块id时使用哪种算法。设置优化。chunkIds to false告诉webpack不应该使用内置算法，因为自定义算法可以通过插件提供。优化有两个默认值



 Also if the environment is development then `optimization.chunkIds` is set to `'named'`, while in production it is set to `'deterministic'` 



if none of the above, `optimization.chunkIds` will be defaulted to `'natural'`





| Option            | Description                                                  |
| :---------------- | :----------------------------------------------------------- |
| `'natural'`       | Numeric ids in order of usage.  数字id的使用顺序。           |
| `'named'`         | Readable ids for better debugging.  可读的id，以便更好地调试。 |
| `'deterministic'` | Short numeric ids which will not be changing between compilation. Good for long term caching. Enable by default for production mode.   简短的数字id，在编译之间不会改变。适合长期缓存。在生产模式下默认启用。 |
| `'size'`          | Numeric ids focused on minimal initial download size.  数字id关注最小的初始下载大小。 |
| `'total-size'`    | numeric ids focused on minimal total download size.  数字id集中于最小的下载大小。 |





### 输出文件分析

[https://segmentfault.com/a/1190000015088834#articleHeader8](https://segmentfault.com/a/1190000015088834#articleHeader8)


```javascript
(function(modules) {

  // 模拟 require 语句
  function __webpack_require__() {
  }

  // 执行存放所有模块数组中的第0个模块
  __webpack_require__(0);

})([/*存放所有模块的数组*/])
```



bundle.js 能直接运行在浏览器中的原因在于输出的文件中通过 __webpack_require__ 函数定义了一个可以在浏览器中执行的加载函数来模拟 Node.js 中的 require 语句。



原来一个个独立的模块文件被合并到了一个单独的 bundle.js 的原因在于浏览器不能像 Node.js 那样快速地去本地加载一个个模块文件，而必须通过网络请求去加载还未得到的文件。 如果模块数量很多，加载时间会很长，因此把所有模块都存放在了数组中，执行一次网络加载。



如果仔细分析 __webpack_require__ 函数的实现，你还有发现 Webpack 做了缓存优化： 执行加载过的模块不会再执行第二次，执行结果会缓存在内存中，当某个模块第二次被访问时会直接去内存中读取被缓存的返回值。






### webpack-dev-server

[https://www.npmjs.com/package/webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)

Webpack dev server 是一个轻量的node.js express服务器，实现了 webpack 编译代码实时输出更新。在前后端分离的前端项目开发中经常用到


Use webpack with a development server that provides live reloading. This should be used for development only.

将webpack与提供实时重新加载的开发服务器一起使用。这应该只用于开发。




It uses webpack-dev-middleware under the hood, which provides fast in-memory access to the webpack assets.


它使用底层的webpack-dev-middleware，它提供对webpack资产的快速内存访问。



### webpack-dev-middleware



[ https://juejin.im/post/5e7782dbf265da57584dc95e?utm_source=gold_browser_extension ]( https://juejin.im/post/5e7782dbf265da57584dc95e?utm_source=gold_browser_extension )

[https://webpack.docschina.org/guides/development/#%E4%BD%BF%E7%94%A8-watch-mode-%E8%A7%82%E5%AF%9F%E6%A8%A1%E5%BC%8F-](https://webpack.docschina.org/guides/development/#%E4%BD%BF%E7%94%A8-watch-mode-%E8%A7%82%E5%AF%9F%E6%A8%A1%E5%BC%8F-)

webpack-dev-middleware 是一个封装器(wrapper)，它可以把 webpack 处理过的文件发送到一个 server。 webpack-dev-server 在内部使用了它，然而它也可以作为一个单独的 package 来使用，以便根据需求进行更多自定义设置。下面是一个 webpack-dev-middleware 配合 express server 的示例。


特性：

* 不会在硬盘中写入文件，完全基于内存实现。
* 如果使用 watch 模式监听代码修改，Webpack 会自动编译，如果在 Webpack 编译过程中请求文件，Webpack dev middleware 会延迟请求，直到编译完成之后再开始发送编译完成的文件。



```js
const wdm = require('webpack-dev-middleware');
const express = require('express');
const webpack = require('webpack');
const webpackConf = require('./webapck.conf.js');
const compiler = webpack(webpackConf);
const app = express();
app.use(wdm(compiler));
app.listen(8080);

```








### webpack-hot-middleware

[https://www.npmjs.com/package/webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware)


Webpack hot reloading using only webpack-dev-middleware. This allows you to add hot reloading into an existing server without webpack-dev-server.




### 预取/预加载模块

[https://webpack.js.org/guides/code-splitting/](https://webpack.js.org/guides/code-splitting/)


**webpack 4.6.0+增加了对预取和预加载的支持。**


在声明导入时使用这些内联指​​令允许webpack输出“Resource Hint”，它告诉浏览器：





* prefetch：将来某些导航可能需要资源
* preload：当前导航期间可能需要资源


简单的预取示例可以是一个HomePage组件，该组件呈现一个LoginButton组件，然后在LoginModal点击后按需加载组件。

```javascript
LoginButton.js

//...
import(/* webpackPrefetch: true */ 'LoginModal');
```


这将导致`<link rel="prefetch" href="login-modal-chunk.js">`被附加在页面的头部，这将指示浏览器在空闲时间预取login-modal-chunk.js文件。


与prefetch相比，Preload指令有很多不同之处：



* 预加载的块开始与父块并行加载。父块完成加载后，将启动预取的块。
* 预加载的块具有中等优先级并立即下载。浏览器空闲时下载预取的块。
* 父组块应立即请求预加载的块。未来的任何时候都可以使用预取的块。
* 浏览器支持是不同的。


简单的预加载示例可以是一个Component总是依赖于应该位于单独块中的大型库。

让我们想象一个ChartComponent需要巨大的组件ChartingLibrary。它会LoadingIndicator在呈现时显示并立即执行按需导入ChartingLibrary：
```javascript
ChartComponent.js

//...
import(/* webpackPreload: true */ 'ChartingLibrary');
```

当ChartComponent请求使用该页面的页面时，也会通过请求图表库`<link rel="preload">`。假设页面块较小并且完成得更快，则页面将显示为a LoadingIndicator，直到已经请求charting-library-chunk完成为止。这将提供一点加载时间，因为它只需要一次往返而不是两次。特别是在高延迟环境中。

>错误地使用webpackPreload实际上会损害性能，因此在使用它时要小心。



### externals

[https://www.jianshu.com/p/283b17d17b3c](https://www.jianshu.com/p/283b17d17b3c)

**webpack 中的 externals 配置提供了不从 bundle 中引用依赖的方式**。



我们可以将一些JS文件存储在 `CDN` 上(减少 `Webpack`打包出来的 `js` 体积)，在 `index.html` 中通过 `<script>` 标签引入，如:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="root">root</div>
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
</body>
</html>
```

我们希望在使用时，仍然可以通过 `import` 的方式去引用(如 `import $ from 'jquery'`)，并且希望 `webpack` 不会对其进行打包，此时就可以配置 `externals`。

```js
//webpack.config.js
module.exports = {
    //...
    externals: {
        //jquery通过script引入之后，全局中即有了 jQuery 变量
        'jquery': 'jQuery'
    }
}
```

这样做的目的就是将不怎么需要更新的第三方库脱离webpack打包，不被打入bundle中，从而减少打包时间，但又不影响运用第三方库的方式，例如import方式等。




#### externals和libraryTarget的关系



libraryTarget配置如何暴露 library。如果不设置library,那这个library就不暴露。就相当于一个自执行函数

externals是决定的是以哪种模式去加载所引入的额外的包





### require.context

https://juejin.im/post/5c10dbcbf265da61441fe8e2



一个webpack的api,通过执行require.context函数获取一个特定的上下文,主要用来实现自动化导入模块,在前端工程中,如果遇到从一个文件夹引入很多模块的情况,可以使用这个api,它会遍历文件夹中的指定文件,然后自动导入,使得不需要每次显式的调用import导入模块





require.context函数接受三个参数

1. directory {String} -读取文件的路径
2. useSubdirectories {Boolean} -是否遍历文件的子目录
3. regExp {RegExp} -匹配文件的正则



> 语法: require.context(directory, useSubdirectories = false, regExp = /^.//);







### 动态import

>需要翻墙 [https://medium.com/webpack/webpack-4-import-and-commonjs-d619d626b655](https://medium.com/webpack/webpack-4-import-and-commonjs-d619d626b655)

目前，类函模import()块加载的语法建议——syntax proposal整体交给ECMAScript。
ES2015(es6)加载器说明定义import()作为一个方法用来动在运行时态加载es6模块。




在webpack中的import()是个分离点——**split-point**，用来把被请求的模块独立成一个单独的模块。**import()把模块的名字作为一个参数，并且返回一个Promise: import(name)->Promise.**



```javascript
function determineDate() {
  import('moment').then(function(moment) {
    console.log(moment().format());
  }).catch(function(err) {
    console.log('Failed to load moment', err);
  });
}

determineDate();
```






## 补充


### chunkFilename
```javascript
{
    entry: {
        "index": "pages/index.jsx"
    },
    output: {
        filename: "[name].min.js",
        chunkFilename: "[name].min.js"
    }
}
```


filename应该比较好理解，就是对应于entry里面生成出来的文件名。生成出来的文件名为index.min.js

**chunkname我的理解是未被列在entry中，却又需要被打包出来的文件命名配置。**


**chunk就是代码块的意思**

多个chunk合在一起就是bundle，一个bundle可以理解为一个大的js打包之后生成的文件，而多个bundle里可能有公共的部分，或者一个bundle里的东西并不需要一次性加载，需要按照路由按需加载，这个时候就需要按需加载，拆分成不同的chunk





### require.context


```javascript
const filename = 'first-level';
const func = require('./dir/' + filename + '.js'); // => Success

```

结构：成功获取文件中的内容。



这时 webpack 自动创建了一个 context，引入了所有路径符合 `./dir ^\.\/.*\.js$ `的文件。生成的 bundle.js 中打包了所有的文件的内容。

---


在刚才的过程中，webpack 会创建一个 require.context，通过正则匹配到可能的文件，全部引入。如果我们想自定义这个正则规则的话，可以自己写一个 require.context。



```javascript
const context = require.context('./dir', true, /\.js$/);
const keys = context.keys(); // => ["./another-first-level.js", "./first-level.js", "./sub-dir/second-level.js"]
const filename = './first-level.js';
const func = context(); // => Success
```

结果：成功获取文件中的内容。

其中第一个参数表示相对的文件目录，第二个参数表示是否包括子目录中的文件，第三个参数表示引入的文件匹配的正则表达式。

---


```javascript
const context = require.context('./dir', false, /\.js$/);
const keys = context.keys(); // => ["./another-first-level.js", "./first-level.js"]
const filename = './first-level.js';
const func = context(filename); // => Success
```


结果：成功获取第一层目录中的文件内容，但是不能拿到子目录中的文件。


通过这个方式就可以解决引入不必要的 node_modules 中的文件的问题




### UMD


```javascript
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD. Register as an anonymous module.
        define(['b'], factory);
    } else if (typeof module === 'object' && module.exports) {
        // Node. Does not work with strict CommonJS, but
        // only CommonJS-like environments that support module.exports,
        // like Node.
        module.exports = factory(require('b'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.b);
    }
}(this, function (b) {
    //use b in some fashion.

    // Just return a value to define the module export.
    // This example returns an object, but the module
    // can return a function as the exported value.
    return {};
}));

```






### 代码分割


[https://segmentfault.com/a/1190000015088834#articleHeader8](https://segmentfault.com/a/1190000015088834#articleHeader8)


[https://codepen.io/whjin/pen/yjmRyG/](https://codepen.io/whjin/pen/yjmRyG/)

```
// 异步加载 show.js
import('./show').then((show) => {
  // 执行 show 函数
  show('Webpack');
});
```

重新构建后会输出两个文件，分别是执行入口文件 bundle.js 和 异步加载文件 0.bundle.js。


其中 0.bundle.js 内容如下：

```javascript
// 加载在本文件(0.bundle.js)中包含的模块
webpackJsonp(
  // 在其它文件中存放着的模块的 ID
  [0],
  // 本文件所包含的模块
  [
    // show.js 所对应的模块
    (function (module, exports) {
      function show(content) {
        window.document.getElementById('app').innerText = 'Hello,' + content;
      }

      module.exports = show;
    })
  ]
);
```


bundle.js 内容如下：



```javascript
(function (modules) {
  /***
   * webpackJsonp 用于从异步加载的文件中安装模块。
   * 把 webpackJsonp 挂载到全局是为了方便在其它文件中调用。
   *
   * @param chunkIds 异步加载的文件中存放的需要安装的模块对应的 Chunk ID
   * @param moreModules 异步加载的文件中存放的需要安装的模块列表
   * @param executeModules 在异步加载的文件中存放的需要安装的模块都安装成功后，需要执行的模块对应的 index
   */
  window["webpackJsonp"] = function webpackJsonpCallback(chunkIds, moreModules, executeModules) {
    // 把 moreModules 添加到 modules 对象中
    // 把所有 chunkIds 对应的模块都标记成已经加载成功 
    var moduleId, chunkId, i = 0, resolves = [], result;
    for (; i < chunkIds.length; i++) {
      chunkId = chunkIds[i];
      if (installedChunks[chunkId]) {
        resolves.push(installedChunks[chunkId][0]);
      }
      installedChunks[chunkId] = 0;
    }
    for (moduleId in moreModules) {
      if (Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
        modules[moduleId] = moreModules[moduleId];
      }
    }
    while (resolves.length) {
      resolves.shift()();
    }
  };

  // 缓存已经安装的模块
  var installedModules = {};

  // 存储每个 Chunk 的加载状态；
  // 键为 Chunk 的 ID，值为0代表已经加载成功
  var installedChunks = {
    1: 0
  };

  // 模拟 require 语句，和上面介绍的一致
  function __webpack_require__(moduleId) {
    // ... 省略和上面一样的内容
  }

  /**
   * 用于加载被分割出去的， 对应的文件
   * @param chunkId 需要异步加载的 Ch需要异步加载的 Chunkunk 对应的 ID
   * @returns {Promise}
   */
  __webpack_require__.e = function requireEnsure(chunkId) {
    // 从上面定义的 installedChunks 中获取 chunkId  Chunk 的对应的加载状态
    var installedChunkData = installedChunks[chunkId];
    // 如果加载状态为0表示该 Chunk 已经加载成功了，直接返回 resolve Promise
    if (installedChunkData === 0) {
      return new Promise(function (resolve) {
        resolve();
      });
    }

    // installedChunkData 不为空且不为0表示该 Chunk 正在网络加载中
    if (installedChunkData) {
      // 返回存放在 installedChunkData 数组中的 Promise 对象
      return installedChunkData[2];
    }

    // installedChunkData 为空，表示该 Chunk 还没有加载过，去加载该 Chunk 对应的文件
    var promise = new Promise(function (resolve, reject) {
      installedChunkData = installedChunks[chunkId] = [resolve, reject];
    });
    installedChunkData[2] = promise;

    // 通过 DOM 操作，往 HTML head 中插入一个 script 标签去异步加载 Chunk 对应的 JavaScript 文件
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.charset = 'utf-8';
    script.async = true;
    script.timeout = 120000;

    // 文件的路径为配置的 publicPath、chunkId 拼接而成
    script.src = __webpack_require__.p + "" + chunkId + ".bundle.js";

    // 设置异步加载的最长超时时间
    var timeout = setTimeout(onScriptComplete, 120000);
    script.onerror = script.onload = onScriptComplete;

    // 在 script 加载和执行完成时回调
    function onScriptComplete() {
      // 防止内存泄露
      script.onerror = script.onload = null;
      clearTimeout(timeout);

      // 去检查 chunkId 对应的 Chunk 是否安装成功，安装成功时才会存在于 installedChunks 中
      var chunk = installedChunks[chunkId];
      if (chunk !== 0) {
        if (chunk) {
          chunk[1](new Error('Loading chunk ' + chunkId + ' failed.'));
        }
        installedChunks[chunkId] = undefined;
      }
    };
    head.appendChild(script);

    return promise;
  };

  // 加载并执行入口模块，和上面介绍的一致
  return __webpack_require__(__webpack_require__.s = 0);
})
(
  // 存放所有没有经过异步加载的，随着执行入口文件加载的模块
  [
    // main.js 对应的模块
    (function (module, exports, __webpack_require__) {
      // 通过 __webpack_require__.e 去异步加载 show.js 对应的 Chunk
      __webpack_require__.e(0).then(__webpack_require__.bind(null, 1)).then((show) => {
        // 执行 show 函数
        show('Webpack');
      });
    })
  ]
);
```



* 多了一个 `__webpack_require__.e` 用于加载被分割出去的，需要异步加载的 Chunk 对应的文件;
* 多了一个 webpackJsonp 函数用于从异步加载的文件中安装模块。



在使用了 CommonsChunkPlugin 去提取公共代码时输出的文件和使用了异步加载时输出的文件是一样的，都会有 `__webpack_require__.e` 和 webpackJsonp。 原因在于提取公共代码和异步加载本质上都是代码分割。







### webpack-dev-middleware和webpack-dev-server的区别


其实就是因为webpack-dev-server只负责启动服务和前置准备工作，所有文件相关的操作都抽离到webpack-dev-middleware库了，**主要是本地文件的编译和输出以及监听**，无非就是职责的划分更清晰了。


我们来看下webpack-dev-middleware源码里做了什么事

```javascript
// node_modules/webpack-dev-middleware/index.js
compiler.watch(options.watchOptions, (err) => {
    if (err) { /*错误处理*/ }
});

// 通过“memory-fs”库将打包后的文件写入内存
setFs(context, compiler); 
```





为什么代码的改动保存会自动编译，重新打包？这一系列的重新检测编译就归功于compiler.watch这个方法了。**监听本地文件的变化主要是通过文件的生成时间是否有变化**，这里就不细讲了。



执行setFs方法，这个方法主要目的就是将编译后的文件打包到内存。这就是为什么在开发的过程中，你会发现dist目录没有打包后的代码，因为都在内存中。原因就在于访问内存中的代码比访问文件系统中的文件更快，而且也减少了代码写入文件的开销，这一切都归功于memory-fs。





### loader-utils

[https://www.npmjs.com/package/loader-utils](https://www.npmjs.com/package/loader-utils)

Utils for webpack loaders


当我们在写自己的webpack-loader时经常会用到这个库


例如：

```javascript
import loaderUtils from 'loader-utils';


module.exports = function(source){
    // 获取传入进来的options
  const options = loaderUtils.getOptions(this) || {};
}

```



### schema-utils


[https://www.npmjs.com/package/schema-utils](https://www.npmjs.com/package/schema-utils)


Package for validate options in loaders and plugins.

用于验证options


// options.json
```json
{
  "type": "object",
  "properties": {
    "option": {
      "type": ["boolean"]
    }
  },
  "additionalProperties": false
}
```




```javascript
import validateOptions from 'schema-utils';
import schema from './options.json';

module.exports = function(source){
  const options = loaderUtils.getOptions(this) || {};

  validateOptions(schema, options, {
    name: 'Style Loader',
    baseDataPath: 'options',
  });
}

```





### Share Variables Between Javascript and Sass

[https://www.bluematador.com/blog/how-to-share-variables-between-js-and-sass](https://www.bluematador.com/blog/how-to-share-variables-between-js-and-sass)



```scss
// styles/animation.scss
$animation-length: 250;
$animation-length-ms: $animation-length + 0ms;

:export {
  animationMillis: $animation-length-ms;
}

.component-enter {
  ...

  transition: all $animation-length-ms ease-in;
}

```



```js
// js/animation.js
import styles from '../styles/animation.scss'
import CSSTransitionGroup from 'react-transition-group/CSSTransitionGroup'

const millis = parseInt(styles.animationMillis)

...

<CSSTransitionGroup
  transitionName="component"
  transitionEnterTimeout={millis}
  transitionLeaveTimeout={millis}
/>

...
```





### webpack术语表

[https://webpack.docschina.org/glossary/](https://webpack.docschina.org/glossary/)



## optimization



### splitChunks

`cacheGroups`配置才是最重要，它允许自定义规则分离 chunk。并且每条`cacheGroups`规则下都允许定义上面提到的`chunks`和`minSize`字段用于覆盖全局配置（又或者将`cacheGroups`规则中`enforce`参数设为`true`来忽略全局配置）

```js
    optimization: {
      splitChunks: {
        cacheGroups: {
          antv: {
            test: /[\\/]node_modules[\\/]@antv[\\/]/,
            name: 'antv',
            priority: 20, // 优先级，否则会被打包到vendors
            chunks: 'all'
          },
          vendors: {
            test: /node_modules/,
            name: 'vendors',
            reuseExistingChunk: true,
            chunks: 'all'
          }
        }
      }
    }
```



----

**有一点非常重要：**



**`cacheGroups`里默认自带`vendors`配置来分离`node_modules`里的类库模块**，它的默认配置如下：

```js
cacheGroups: {
  vendors: {
    test: /[\\/]node_modules[\\/]/,
    priority: -10
  },
  default: {
    minChunks: 2,
    priority: -20,
    reuseExistingChunk: true
   }
}     
```

如果你不想使用它的配置，你可以把它设为`false`又或者重写它。这里我选择重写，并且加入了额外的配置`name`和`enforce`:

```js
vendors: {
  test: /[\\/]node_modules[\\/]/,
  name: 'vendors',
  enforce: true,
},
```



最后介绍以上并没有出现但是仍然常用的两个配置：`priority`和`reuseExistingChunk`

* `reuseExistingChunk`: 该选项只会出现在`cacheGroups`的分离规则中，意味重复利用现有的 chunk。例如 chunk 1 拥有模块 A、B、C；chunk 2 拥有模块 B、C。如果 `reuseExistingChunk` 为 `false` 的情况下，在打包时插件会为我们单独创建一个 chunk 名为 `common~for~1~2`，它包含公共模块 B 和 C。而如果该值为`true`的话，因为 chunk 2 中已经拥有公共模块 B 和 C，所以插件就不会再为我们创建新的模块
* `priority`: 很容易想象到我们会在`cacheGroups`中配置多个 chunk 分离规则。如果同一个模块同时匹配多个规则怎么办，`priority`解决的这个问题。注意所有默认配置的`priority`都为负数，所以自定义的`priority`必须大于等于0才行



> reuseExistingChunk  复用已经存在的chunk



---

`maxInitialRequests`和`minSize`确实就是插件自作多情的杰作了。插件自带一些分离 chunk 的规则：如果即将分离的 chunk 文件体积小于 30KB 的话，那么就不会将该 chunk 分离出来；并且限制并行下载的 chunk 最大请求个数为 3 个。通过覆盖 `minSize` 和 `maxInitialRequests` 配置就能够重写这两个参数。




`maxInitialRequests`和`minSize`是在`splitChunks`根目录中的，我们暂且称它为全局配置



**除了js，splitChunks也适用于css**

**除了js，splitChunks也适用于css**

**除了js，splitChunks也适用于css**





关于splitChunks的几个例子：

[Create a commons chunk, which includes all code shared between entry points](https://www.webpackjs.com/plugins/split-chunks-plugin/#split-chunks-example-1)

[Create a `vendors` chunk, which includes all code from `node_modules` in the whole application](https://www.webpackjs.com/plugins/split-chunks-plugin/#split-chunks-example-2)







### vendors

上面vendors会打包出来chunk-vendors，这个chunk会在首屏加载的时候就去加载(**提取了node_modules公共部分**)



为了让vendors更加的小，我们可以吧不是首屏需要的一些巨大的三方库提取出来



提取前的vendors (2.41mb parsed)

![](https://s3.ax1x.com/2020/12/14/rnRP0g.png)

提取后的vendors (1.37mb parsed)

![](https://s3.ax1x.com/2020/12/14/rnRJ91.png)

配置：

```js
  splitChunks: {
        cacheGroups: {
          antv: {
            test: /[\\/]node_modules[\\/]@antv[\\/]/,
            name: 'antv',
            priority: 20, // 优先级，否则会被打包到vendors
            chunks: 'all'
          },
          vendors: {
            test: /node_modules/,
            name: 'vendors',
            reuseExistingChunk: true,
            chunks: 'all'
          }
        }
      },
```









## 面试



### webpack 是如何解决两次引入的

[https://segmentfault.com/a/1190000008521430](https://segmentfault.com/a/1190000008521430)



不同文件中多次import同一个文件，webpack并不会多次打包，只会在打包后的文件中会多次引用打包后的该文件对应的函数。



**关键点installedModules**

```js
// dist/index.xxxx.js
(function(modules) {
  // 已经加载过的模块
  var installedModules = {};

  // 模块加载函数
  function __webpack_require__(moduleId) {
    if(installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }
    var module = installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {}
    };
    modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
    module.l = true;
    return module.exports;
  }
  return __webpack_require__(__webpack_require__.s = 3);
})([
/* 0 */
(function(module, exports, __webpack_require__) {
  var util = __webpack_require__(1);
  console.log(util);
  module.exports = "index 2";
}),
/* 1 */
(function(module, exports) {
  module.exports = "Hello World";
}),
/* 2 */
(function(module, exports, __webpack_require__) {
  var index2 = __webpack_require__(0);
  index2 = __webpack_require__(0);
  var util = __webpack_require__(1);
  console.log(index2);
  console.log(util);
}),
/* 3 */
(function(module, exports, __webpack_require__) {
  module.exports = __webpack_require__(2);
})]);
```

1. 首先 webpack 将所有模块(可以简单理解成文件)包裹于一个函数中，并传入默认参数，这里有三个文件再加上一个入口模块一共四个模块，将它们放入一个数组中，取名为 modules，并通过数组的下标来作为 moduleId。
2. 将 modules 传入一个自执行函数中，自执行函数中包含一个 installedModules 已经加载过的模块和一个模块加载函数，最后加载入口模块并返回。
3. *__webpack_require__* 模块加载，先判断 installedModules 是否已加载，加载过了就直接返回 exports 数据，没有加载过该模块就通过 *modules[moduleId].call(module.exports, module, module.exports, __webpack_require__)* 执行模块并且将 module.exports 给返回。

















### webpack是如何实现动态导入的

[https://juejin.im/post/6844903888319954952](https://juejin.im/post/6844903888319954952)



![](https://user-gold-cdn.xitu.io/2019/7/11/16be0d655244d397?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



```
import被转化成了__webpack_require__.e(/*! import() */ 0)
```



`__webpack_require__.e`就是requireEnsure



![](https://user-gold-cdn.xitu.io/2019/7/11/16be0df85e18743c?imageslim)



代码可能有点眼花，看下来无非就是做了这么一件事情。

1. 根据 `installedChunks` 检查是否加载过该 **chunk**
2. 假如没加载过，则发起一个 **`JSONP`** 请求去加载 **chunk**
3. 设置一些请求的错误处理，然后返回一个 **Promise**。



当 Promise 返回之后，就会继续执行我们之前的异步请求回调

```js
__webpack_require__.e(/*! import() */ 0)
    .then(
        __webpack_require__.bind(null, /*! ./async */ "./src/async.js")
    )
  ...
```

这里直接调用了 `__webpack_require__` 去加载我们的 `异步模块` 。



**这里就有两个问题？**

 

1. **`__webpack_require__` 是根据我们之前传入的 `modules` 来获取 `module` 的，但是，在 `__webpack_require__.e` 中并没有看到有对 `modules` 执行操作的代码。那 `modules` 到底是什么时候被更新的呢？**
2. `promise` 把 `resolve` 和 `reject` 全部存入了 `installedChunks` 中， 并没有在获取异步chunk成功的`onload` 回调中执行 `resolve`，那么，`resolve` 是什么时候被执行的呢?











