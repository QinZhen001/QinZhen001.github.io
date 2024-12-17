---
layout:     post
title:      "webpack相关"
date:       2017-12-12 14:34:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”

# 基础

[网页链接](http://www.jianshu.com/p/42e11515c10f)

[webpack从入门到工程实践](https://github.com/zhangwang1990/blogs/blob/master/articles/webpack%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%B7%A5%E7%A8%8B%E5%AE%9E%E8%B7%B5.md)

[https://webpack.toobug.net/zh-cn/](https://webpack.toobug.net/zh-cn/)

[深入理解 webpack 文件打包机制](https://zhuanlan.zhihu.com/p/32706935)

[Webpack设计理念](https://juejin.cn/post/7170852747749621791)



## 什么是Webpack

WebPack可以看做是**模块打包机**：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。

## WebPack和Grunt以及Gulp相比有什么特性

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

### devserver

[https://webpack.js.org/configuration/dev-server/](https://webpack.js.org/configuration/dev-server/)

想不想让你的浏览器监听你的代码的修改，并自动刷新显示修改后的结果，其实Webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现你想要的这些功能，不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖



#### compress

boolean

Enable [gzip compression](https://betterexplained.com/articles/how-to-optimize-your-site-with-gzip-compression/) for everything served:

为所有服务启用gzip压缩



devserver作为webpack配置选项中的一项，以下是它的一些配置选项，更多配置可参考

| devserver的配置选项 | 功能描述                                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| contentBase         | 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录） |
| port                | 设置默认监听端口，如果省略，默认为”8080“                                                                                                        |
| inline              | 设置为true，当源文件改变时会自动刷新页面                                                                                                          |
| historyApiFallback  | 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html                                                   |

## Bundle VS Chunk VS Module

[https://segmentfault.com/a/1190000015919928](https://segmentfault.com/a/1190000015919928)

首先对于“模块”(module)的概念相信大家都没有异议，它指的就是我们在编码过程中有意识的封装和组织起来的代码片段。狭义上我们首先联想到的是碎片化的 React 组件，或者是 CommonJS 模块又或者是 ES6 模块，但是对 Webpack 和 Loader 而言，广义上的模块还包括样式和图片，甚至说是不同类型的文件



而“包”(bundle) 就是把相关代码都打包进入的单个文件。如果你不想把所有的代码都放入一个包中，你可以把它们划分为多个包，也就是“块”(chunk) 中。从这个角度上看，“块”等于“包”，它们都是对代码再一层的组织和封装。如果必须要给一个区分的话，通常我们在讨论时，bundle 指的是所有模块都打包进入的单个文件，而 chunk 指的是按照某种规则的模块集合，chunk 的体积大于单个模块，同时小于整个 bundle



（但如果要仔细的深究，**Chunk**是 Webpack 用于管理打包流程中的技术术语，甚至能划分为不同类型的 chunk。我想我们不用从这个角度理解。只需要记住上一段的定义即可）



作者：李熠
链接：https://juejin.im/post/6844903846993461256
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





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





## publicPath

[Webpack中publicPath详解](https://juejin.cn/post/6844903601060446221)

静态资源最终访问路径 = output.publicPath + 资源loader或插件等配置路径





## watch

```javascript
 "scripts": {
    ...
    "watch": "webpack --watch"
  },
```



这样我们就可以使用npm run watch来调用 node_modules/.bin/webpack --watch了。


命令执行完之后,它会监控源码文件，然后只对改变的文件进行重编译。

>另外，npm run build，调用了node_modules/.bin/webpack，它是一个软链接，

## 一切皆模块

Webpack有一个不可不说的优点，它把所有的文件都都当做模块处理，JavaScript代码，CSS和fonts以及图片等等通过合适的loader都可以被处理。

## 处理css

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

## Scope-Hoisting

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

## Splitting

### Code Splitting

[https://segmentfault.com/a/1190000015088834#articleHeader8](https://segmentfault.com/a/1190000015088834#articleHeader8)

[https://codepen.io/whjin/pen/yjmRyG/](https://codepen.io/whjin/pen/yjmRyG/)

[https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE](https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE)




我们把所有代码分成一块一块，需要某块代码的时候再去加载它；利用浏览器的缓存，下次用到它的话，直接从缓存中读取。很显然，这种做法可以加快我们网页的加载速度。

**所以说，Code Splitting 其实就是把代码分成很多很多块（ chunk ）**

Code Splitting 主要有 2 种方式：
1. 分离业务代码和第三方库（ vendor ）
2. 按需加载（利用 import() 语法）

之所以把业务代码和第三方库代码分离出来，是因为产品经理的需求是源源不断的，因此业务代码更新频率大，相反第三方库代码更新迭代相对较慢且可以锁版本，所以可以充分利用浏览器的缓存来加载这些第三方库。

而按需加载的适用场景，比如说「访问某个路由的时候再去加载对应的组件」，用户不一定会访问所有的路由，所以没必要把所有路由对应的组件都先在开始的加载完；更典型的例子是「某些用户他们的权限只能访问某些页面」，所以没必要把他们没权限访问的页面的代码也加载。



### Bundle splitting

打包分离 (Bundle splitting)：为了更好的缓存创建更多、更小的文件（但仍然以每一个文件一个请求的方式进行加载）



打包分离背后的思想非常简单。如果你有一个体积巨大的文件，并且只改了一行代码，用户仍然需要重新下载整个文件。但是如果你把它分为了两个文件，那么用户只需要下载那个被修改的文件，而浏览器则可以从缓存中加载另一个文件。

**值得注意的是因为打包分离与缓存相关，所以对站点的首次访问者来说没有区别**

## Runtime

runtime，以及伴随的 manifest 数据，主要是指：**在浏览器运行时，webpack 用来连接模块化的应用程序的所有代码。**runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。

### RuntimeChunk

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





## Manifest

那么，一旦你的应用程序中，形如 index.html 文件、一些 bundle 和各种资源加载到浏览器中，会发生什么？你精心安排的 /src 目录的文件结构现在已经不存在，所以 webpack 如何管理所有模块之间的交互呢？这就是 manifest 数据用途的由来……

当编译器(compiler)开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "Manifest"，当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块。无论你选择哪种模块语法，那些 import 或 require 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。

## output

### libraryTarget

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

### libraryExport

[https://www.webpackjs.com/configuration/output/#output-libraryexport](https://www.webpackjs.com/configuration/output/#output-libraryexport)

`string` or `string[]` (since webpack 3.0.0)

> Default: `_entry_return_`



上面的默认值会导致一个小问题

[https://mp.weixin.qq.com/s/uAhqYgfFTrTfkiCicPmHpg](https://mp.weixin.qq.com/s/uAhqYgfFTrTfkiCicPmHpg)



Configure which module or modules will be exposed via the libraryTarget. The default entry return value is the namespace or default module returned by your entry file. The examples below demonstrate the effect of this config when using libraryTarget: "var", but any target may be used.

配置哪些或哪些模块将通过libraryTarget公开。默认条目返回值是条目文件返回的名称空间或默认模块。下面的例子演示了这个配置在使用libraryTarget: "var"时的效果，但是任何目标都可以使用。



例子：

`libraryExport: "default"` - The **default export of your entry point** will be assigned to the library target:

```js
// if your entry has a default export of `MyDefaultModule`
var MyDefaultModule = _entry_return_.default;
```

只会暴露default上面的东西。





## context

Webpack 在寻找相对路径的文件时会以 context 为根目录，context 默认为执行启动 Webpack 时所在的当前工作目录。 如果想改变 context 的默认配置，则可以在配置文件里这样设置它：


```javascript
1 module.exports = {
2   context: path.resolve(__dirname, 'app')
3 }
```



**Entry 的路径和其依赖的模块的路径可能采用相对于 context 的路径来描述，context 会影响到这些相对路径所指向的真实文件**。

## external

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

## optimization 

### chunkIds 

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

### splitChunks

[https://mp.weixin.qq.com/s/oOHDsuGnkPXnJNTNXQ0Zqw](https://mp.weixin.qq.com/s/oOHDsuGnkPXnJNTNXQ0Zqw)



```tsx
   optimization: {
        splitChunks: {
            chunks: "initial",         // 必须三选一： "initial" | "all"(默认就是all) | "async"
            minSize: 0,                // 最小尺寸，默认0
            minChunks: 1,              // 最小 chunk ，默认1
            maxAsyncRequests: 1,       // 最大异步请求数， 默认1
            maxInitialRequests: 1,    // 最大初始化请求书，默认1
            name: () => {
            },              // 名称，此选项课接收 function
            cacheGroups: {                 // 这里开始设置缓存的 chunks
                priority: "0",                // 缓存组优先级 false | object |
                vendor: {                   // key 为entry中定义的 入口名称
                    chunks: "initial",        // 必须三选一： "initial" | "all" | "async"(默认就是异步)
                    test: /react|lodash/,     // 正则规则验证，如果符合就提取 chunk
                    name: "vendor",           // 要缓存的 分隔出来的 chunk 名称
                    minSize: 0,
                    minChunks: 1,
                    enforce: true,
                    maxAsyncRequests: 1,       // 最大异步请求数， 默认1
                    maxInitialRequests: 1,    // 最大初始化请求书，默认1
                    reuseExistingChunk: true   // 可设置是否重用该chunk（查看源码没有发现默认值）
                }
            }
        }
    }
```

CommonChunksPlugin 会找到多数模块中都共有的东西，并且把它提取出来（common.js），也就意味着如果你加载了 common.js，那么里面可能会存在一些当前模块不需要的东西。


而 SplitChunksPlugin 采用了完全不同的 heuristics 方法，它会根据模块之间的依赖关系，自动打包出很多很多（而不是单个）通用模块，可以保证加载进来的代码一定是会被依赖到的。

下面是一个简单的例子，假设我们有 4 个 chunk，分别依赖了以下模块：

| chunk   | 依赖模块                            |
| ------- | ----------------------------------- |
| chunk-a | react, react-dom, componentA, utils |
| chunk-b | react, react-dom, componentB, utils |
| chunk-c | angular, componentC, utils          |
| chunk-d | angular, componentD, utils          |


如果是以前的 CommonChunksPlugin，那么默认配置会把它们打包成下面这样：


| common  | utils                        |
| ------- | ---------------------------- |
| chunk-a | react, react-dom, componentA |
| chunk-b | react, react-dom, componentB |
| chunk-c | angular, componentC          |
| chunk-d | angular, componentD          |


显然在这里，react、react-dom、angular 这些公用的模块没有被抽出成为独立的包，存在进一步优化的空间。


现在，新的 SplitChunksPlugin 会把它们打包成以下几个包：


| 包名                              | 包含的模块       |
| --------------------------------- | ---------------- |
| `chunk-a~chunk-b~chunk-c~chunk-d` | utils            |
| chunk-a~chunk-b                   | react, react-dom |
| chunk-c~chunk-d                   | angular          |
| chunk-a                           | componentA       |
| chunk-b                           | componentB       |
| chunk-c                           | componentC       |
| chunk-d                           | componentD       |



这就保证了所有公用的模块，都会被抽出成为独立的包，几乎完全避免了多页应用中，重复加载相同模块的问题。



-----





举个例子：

```js
  optimization: {
    splitChunks: {
      chunks: 'all',
    }
  }
```



`optimization.splitChunks.chunks = 'all'`配置也就是在说：“把所有`node_modules`里的东西都放到`vendors~main.js`的文件中去



####  cacheGroups

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





### sideEffects

[https://webpack.docschina.org/configuration/optimization/#optimizationsideeffects](https://webpack.docschina.org/configuration/optimization/#optimizationsideeffects)

告知 webpack 去辨识 `package.json` 中的 [`副作用`](https://github.com/webpack/webpack/blob/master/examples/side-effects/README.md) 标记或规则，以跳过那些当导出不被使用且被标记不包含副作用的模块



sideEffects: 副作用

[https://zhuanlan.zhihu.com/p/41795312](https://zhuanlan.zhihu.com/p/41795312)

简单说来就是 JS 引用类型属性读/写所带来的副作用



webpack通过配置`optimization.sideEffects`为`true`，表示打包时跳过那些没有被使用的且被package.json标记为无副作用的模块。

**注意：这个和package.json里面的sideEffects表示的含义不一样**



package.json里面的sideEffects 表示当前项目发npm包时，无副作用







## bundle文件分析

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

webpack 5 打包的bundle文件内容:

```tsx
(() => { // webpackBootstrap
    var __webpack_modules__ = ({
        'file-A-path': ((modules) => { // ... })
        'index-file-path': ((__unused_webpack_module, __unused_webpack_exports, __webpack_require__) => { // ... })
    })
    
    // The module cache
    var __webpack_module_cache__ = {};
    
    // The require function
    function __webpack_require__(moduleId) {
        // Check if module is in cache
        var cachedModule = __webpack_module_cache__[moduleId];
        if (cachedModule !== undefined) {
                return cachedModule.exports;
        }
        // Create a new module (and put it into the cache)
        var module = __webpack_module_cache__[moduleId] = {
                // no module.id needed
                // no module.loaded needed
                exports: {}
        };

        // Execute the module function
        __webpack_modules__[moduleId](module, module.exports, __webpack_require__);

        // Return the exports of the module
        return module.exports;
    }
    
    // startup
    // Load entry module and return exports
    // This entry module can't be inlined because the eval devtool is used.
    var __webpack_exports__ = __webpack_require__("./src/index.js");
})
```

和`webpack4`相比，`webpack5`打包出来的bundle做了相当的精简。在上面的打包`demo`中，整个立即执行函数里边只有三个变量和一个函数方法，`__webpack_modules__`存放了编译后的各个文件模块的JS内容，`__webpack_module_cache__ `用来做模块缓存，`__webpack_require__`是`Webpack`内部实现的一套依赖引入函数。最后一句则是代码运行的起点，从入口文件开始，启动整个项目。

其中值得一提的是`__webpack_require__`模块引入函数，我们在模块化开发的时候，通常会使用`ES Module`或者`CommonJS`规范导出/引入依赖模块，`webpack`打包编译的时候，会统一替换成自己的`__webpack_require__`来实现模块的引入和导出，从而实现模块缓存机制，以及抹平不同模块规范之间的一些差异性。

## webpack-dev-server

> webpack 5.x 版本 之后 使用 webpack serve 代替 

> 从 `webpack-dev-server` v4.0.0 开始，热模块替换是默认开启的。

[https://www.npmjs.com/package/webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)

Webpack dev server 是一个轻量的node.js express服务器，实现了 webpack 编译代码实时输出更新。在前后端分离的前端项目开发中经常用到


Use webpack with a development server that provides live reloading. This should be used for development only.

将webpack与提供实时重新加载的开发服务器一起使用。这应该只用于开发。


It uses webpack-dev-middleware under the hood, which provides fast in-memory access to the webpack assets.


它使用底层的webpack-dev-middleware，它提供对webpack资产的快速内存访问。

```tsx
const wdm = require('webpack-dev-middleware');
const express = require('express');
const webpack = require('webpack');
const webpackConf = require('./webapck.conf.js');
const compiler = webpack(webpackConf);
const app = express();
app.use(wdm(compiler));
app.listen(8080);
```





### webpack-dev-middleware

[ https://juejin.im/post/5e7782dbf265da57584dc95e?utm_source=gold_browser_extension ]( https://juejin.im/post/5e7782dbf265da57584dc95e?utm_source=gold_browser_extension )

[https://webpack.docschina.org/guides/development/#%E4%BD%BF%E7%94%A8-watch-mode-%E8%A7%82%E5%AF%9F%E6%A8%A1%E5%BC%8F-](https://webpack.docschina.org/guides/development/#%E4%BD%BF%E7%94%A8-watch-mode-%E8%A7%82%E5%AF%9F%E6%A8%A1%E5%BC%8F-)

webpack-dev-middleware 是一个封装器(wrapper)，它可以把 webpack 处理过的文件发送到一个 server。 webpack-dev-server 在内部使用了它，然而它也可以作为一个单独的 package 来使用，以便根据需求进行更多自定义设置。下面是一个 webpack-dev-middleware 配合 express server 的示例。


特性：

* 不会在硬盘中写入文件，完全基于内存实现。
* 如果使用 watch 模式监听代码修改，Webpack 会自动编译，如果在 Webpack 编译过程中请求文件，Webpack dev middleware 会延迟请求，直到编译完成之后再开始发送编译完成的文件。


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




### webpack-hot-middleware

[https://www.npmjs.com/package/webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware)


Webpack hot reloading using only webpack-dev-middleware. This allows you to add hot reloading into an existing server without webpack-dev-server.



### webpack-dev-server 和 webpack serve

**webpack-dev-server 是在 webpack 4.x 版本之前使用的开发服务器，而 webpack serve 是在 webpack 5.x 版本之后引入的新的开发服务器。所以如果你使用的是 webpack 4.x 版本及之前的版本，应该使用 webpack-dev-server；如果你使用的是 webpack 5.x 版本及之后的版本，应该使用 webpack serve。**

1. 配置文件：使用 webpack-dev-server 需要将配置信息写在 webpack.config.js 文件中的 devServer 属性中，而使用 webpack serve 则是写在 webpack.config.js 文件中的 devServer 属性下的 serve 属性中。
2. 默认值：使用 webpack-dev-server 时，默认的选项值可能需要通过命令行参数来修改，而 webpack serve 则会自动读取 webpack.config.js 文件中的配置，可以避免在命令行中设置参数。

webpack serve 是 webpack 5.x 版本及之后的新的开发服务器，相比 webpack-dev-server 更加灵活和方便使用。但是两者的核心功能都是一样的，都是用于在开发环境下提供热更新、自动刷新等功能的开发服务器。





## 预取/预加载模块

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

## mode 

[https://segmentfault.com/a/1190000013712229?utm_source=tag-newest](https://segmentfault.com/a/1190000013712229?utm_source=tag-newest)

现在可以在两种模式中选择 (mode or --mode) : 生产模式或开发模式

一旦开启了 --mode production，会自动开启代码压缩、scope hoist 等插件，以及自动传递环境变量给 lib 包，所以已经不需要 plugins 这个配置项了。

同理，开启了 --mode development 会自动开启 sourceMap 等开发插件，我们只要关心更简单的配置，这就是 4.0 零配置的重要改变。

```javascript
//parent chunk中解决了的chunk会被删除
optimization.removeAvailableModules:true
//删除空的chunks
optimization.removeEmptyChunks:true
//合并重复的chunk
optimization.mergeDuplicateChunks:true
````

**在 develoment mode 默认**

```javascript
//调试
devtool:eval
//缓存模块, 避免在未更改时重建它们。
cache:true
//缓存已解决的依赖项, 避免重新解析它们。
module.unsafeCache:true
//在 bundle 中引入「所包含模块信息」的相关注释
output.pathinfo:true
//在可能的情况下确定每个模块的导出,被用于其他优化或代码生成。
optimization.providedExports:true
//找到chunk中共享的模块,取出来生成单独的chunk
optimization.splitChunks:true
//为 webpack 运行时代码创建单独的chunk
optimization.runtimeChunk:true
//编译错误时不写入到输出
optimization.noEmitOnErrors:true
//给模块有意义的名称代替ids
optimization.namedModules:true
//给模chunk有意义的名称代替ids
optimization.namedChunks:true
```

**在 production mode 默认**

```javascript
//性能相关配置
performance:{hints:"error"....}
//某些chunk的子chunk已一种方式被确定和标记,这些子chunks在加载更大的块时不必加载
optimization.flagIncludedChunks:true
//给经常使用的ids更短的值
optimization.occurrenceOrder:true
//确定每个模块下被使用的导出
optimization.usedExports:true
//识别package.json or rules sideEffects 标志
optimization.sideEffects:true
//尝试查找模块图中可以安全连接到单个模块中的段。- -
optimization.concatenateModules:true
//使用uglify-js压缩代码
optimization.minimize:true
```

## sideEffects 

[https://segmentfault.com/a/1190000015689240](https://segmentfault.com/a/1190000015689240)

> sideEffects这个设置项介绍，因为这并不是官方标准字段。而是webpack为了更好实现tree shaking所提出的配置项

sideEffects 属性不仅适用于 webpack，它是一个在 JavaScript 生态系统中通用的属性，可以在各种打包工具和环境中使用。

webpack v4 开始新增了一个 sideEffects 特性，通过给 **package.json 加入 sideEffects: false** 声明该包模块是否包含 sideEffects(副作用)，从而可以为 tree-shaking 提供更大的优化空间。


什么是sideEffects？

中心思想是：

whenever a module reexports all exports (regardless if used or unused) need to be evaluated and executed in the case that one of those exports created a side-effect with another.
每当一个模块重导出了所有导出(无论是否会被用) 需要被计算和执行时，其中一个导出就对其他的导出产生了副作用。



### 使用场景

上面也说到，通常我们发布到 npm 上的包很难保证其是否包含副作用（可能是代码的锅可能是 transformer 的锅），但是我们基本能确保这个包是否会对包以外的对象产生影响，比如是否修改了 window 上的属性，是否复写了原生对象方法等。如果我们能保证这一点，其实我们就能知道整个包是否能设置 sideEffects: false了，至于是不是真的有副作用则并不重要，这对于 webpack 而言都是可以接受的。**这也就能解释为什么能给 vue 这个本身充满副作用的包加上 sideEffects: false 了。**

所以其实 webpack 里的 sideEffects: false 的意思并不是我这个模块真的没有副作用，而只是为了在摇树时告诉 webpack：**我这个包在设计的时候就是期望没有副作用的，即使他打完包后是有副作用的，webpack 同学你摇树时放心的当成无副作用包摇就好啦！**

也就是说，只要你的包不是用来做 polyfill 或 shim 之类的事情，就尽管放心的给他加上 sideEffects: false 吧！

最后，翻译成大白话



### 处理css

[https://juejin.cn/post/6844903937443627021](https://juejin.cn/post/6844903937443627021)

或者配置个数组，表示不进行删减的文件，尤其是css,

```json
"sideEffects": [ 
    "*.css"
]
```



### externals和libraryTarget的关系

libraryTarget配置如何暴露 library。如果不设置library,那这个library就不暴露。就相当于一个自执行函数

**externals是决定的是以哪种模式去加载所引入的额外的包**

### 动态导入 import

使用 `await import("xxxx")` 进行动态导入时，不会造成重复引用。动态导入的模块会被 Webpack 处理并确保模块只被加载一次，无论你在应用中多次动态导入相同的模块。

1. **按需加载**：动态导入可以实现按需加载，减少初始加载时间，提高应用性能。
2. **代码分割**：Webpack 会自动将动态导入的模块分割成单独的 chunk，有助于优化资源加载。
3. **避免重复加载**：Webpack 会缓存已经加载的模块，确保模块只加载一次，避免重复引用。

工作原理：

使用 `await import("xxxx")` 时，Webpack 会将这个模块分割成一个单独的 chunk，并在需要时动态加载它。Webpack 会自动管理这些 chunk，并确保每个模块只加载一次，即使在多个地方动态导入相同的模块。

### 示例

假设你有一个模块 `module.js`，内容如下：

```javascript
// module.js
export default function sayHello() {
  console.log("Hello, world!");
}
```

你可以在多个组件中动态导入这个模块：

```jsx
// ComponentA.js
import React, { useEffect } from 'react';

const ComponentA = () => {
  useEffect(() => {
    const loadModule = async () => {
      const module = await import('./module');
      module.default();
    };
    loadModule();
  }, []);

  return <div>Component A</div>;
};

export default ComponentA;
// ComponentB.js
import React, { useEffect } from 'react';

const ComponentB = () => {
  useEffect(() => {
    const loadModule = async () => {
      const module = await import('./module');
      module.default();
    };
    loadModule();
  }, []);

  return <div>Component B</div>;
};

export default ComponentB;
```

在这个例子中，`ComponentA` 和 `ComponentB` 都动态导入了 `module.js`，但 Webpack 会确保 `module.js` 只被加载一次。



## tree shaking

> Rather than *excluding dead code*, we’re *including live code*.

[https://wangtunan.github.io/blog/webpack/webpack/advanced.html](https://wangtunan.github.io/blog/webpack/webpack/advanced.html)

Tree shaking是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 Dead code elimination

tree shaking 生效的条件：

- 使用 ES6 的模块语法 (`import` 和 `export`)，因为 CommonJS 的模块导入方式并不支持静态分析。
- **在生产环境中构建（使用 `mode: 'production'`），因为开发模式下没有启动优化。**



### tree shaking 的原理

- `ES6 Module`引入进行静态分析，故而编译的时候正确判断到底加载了那些模块
- 静态分析程序流，判断那些模块和变量未被使用或者引用，进而删除对应代码

### 和 Rollup 的tree-shaking比较

rollup有着更好的`tree-shaking`对应开发类库属实好用的

> - rollup 是在编译打包过程中分析程序流，得益于于 ES6 静态模块（exports 和 imports 不能在运行时修改），我们在打包时就可以确定哪些代码时我们需要的。
> - webpack 本身在打包时只能标记未使用的代码而不移除，而识别代码未使用标记并完成 tree-shaking 的 其实是 UglifyJS、babili、terser 这类压缩代码的工具。简单来说，就是压缩工具读取 webpack 打包结果，在压缩之前移除 bundle 中未使用的代码



## esm打包

[https://webpack.js.org/configuration/output/#module-definition-systems](https://webpack.js.org/configuration/output/#module-definition-systems)

Output ES Module.

```tsx
module.exports = {
  // …
  experiments: {
    outputModule: true,
  },
  output: {
    library: {
      // do not specify a `name` here
      type: 'module',
    },
  },
};

```



还需要 babel的配置

1. 配置`@babel/preset-env`的`modules`选项为`false`，关闭模块转换；
2. 配置`@babel/plugin-transform-runtime`的`useESModules`选项为`true`，使用`ES module`形式引入`helper`函数。

```tsx
module.exports = {
  presets: [
    [
      '@babel/env',
      {
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









# 补充

## require.context

https://juejin.cn/post/6844903736209309703

require.context函数接受三个参数

1. directory {String} -读取文件的路径
2. useSubdirectories {Boolean} -是否遍历文件的子目录
3. regExp {RegExp} -匹配文件的正则



webpack 可以通过创建一个 require.context，通过正则匹配到可能的文件。

如果我们想自定义这个正则规则的话，可以自己写一个 require.context。

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

## UMD


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

## Share Variables Between Javascript and Sass

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

## cross-env

cross-env能跨平台地设置及使用环境变量

大多数情况下，在windows平台下使用类似于: NODE_ENV=production的命令行指令会卡住，windows平台与POSIX在使用命令行时有许多区别（例如在POSIX，使用$ENV_VAR,在windows，使用%ENV_VAR%。。。）

cross-env让这一切变得简单，不同平台使用唯一指令，无需担心跨平台问题

```tsx
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
  }
}
```

### 设置失败

例子：

```tsx
"test": "cross-env NODE_ENV=dev && node test.js"
```

test.js 只有一句 console.log(process.env.NODE_ENV)
但结果显示undefined



为什么？

cross-env模块下，它划分出前后两个环境，后一句的环境没有设置到NODE_ENV变量，值为undefined；

解决：

```tsx
"test": "cross-env NODE_ENV=dev node test.js"
```

## webpack术语表

[https://webpack.docschina.org/glossary/](https://webpack.docschina.org/glossary/)



## vendors

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





## webpack 5

[https://webpack.js.org/blog/2020-10-10-webpack-5-release/](https://webpack.js.org/blog/2020-10-10-webpack-5-release/)



This release focus on the following:

- Improve build performance with Persistent Caching. (使用持久缓存提高构建性能。)
- Improve Long Term Caching with better algorithms and defaults. (使用更好的算法和默认值改进长期缓存。)
- Improve bundle size with better Tree Shaking and Code Generation.(通过更好的摇树和代码生成来改进bundle的大小。)
- Improve compatibility with the web platform. (提高与web平台的兼容性。)
- Clean up internal structures that were left in a weird state while implementing features in v4 without introducing any breaking changes.
- Prepare for future features by introducing breaking changes now, allowing us to stay on v5 for as long as possible.



### Long Term Caching

[https://webpack.js.org/blog/2020-10-10-webpack-5-release/#major-changes-long-term-caching](https://webpack.js.org/blog/2020-10-10-webpack-5-release/#major-changes-long-term-caching)

New algorithms were added for long term caching. These are enabled by default in production mode.  (生产模式的默认配置)

```
chunkIds: "deterministic"` 
`moduleIds: "deterministic"`
`mangleExports: "deterministic"
```

The algorithms assign short (3 or 5 digits) numeric IDs to modules and chunks and short (2 characters) names to exports in a deterministic way. This is a trade-off between bundle size and long term caching.

算法以确定的方式将短(3或5位数字)的数字id分配给模块和块，将短(2个字符)的名称分配给导出。这是捆绑包大小和长期缓存之间的权衡。

[https://juejin.cn/post/6844903967793627149](https://juejin.cn/post/6844903967793627149)

以下操作均会让 webpack 使 entry 缓存失效：

- 当 npm 升级 loader 或 plugin 时
- 当更改配置时
- 当更改在配置中读取的文件时
- 当 npm 升级配置中使用的 dependencies 时
- 当不同命令行参数传递给 build 脚本时
- 当有自定义构建脚本并进行更改时



这变得非常棘手。 开箱即用的情况下，webpack 无法处理所有这些情况。 这就是我们为什么选择安全的方式，并将持久化缓存变为可选特性的原因





### Real Content Hash

Webpack 5 will use a real hash of the file content when using [contenthash] now. Before it "only" used a hash of the internal structure. This can be positive impact on long term caching when only comments are changed or variables are renamed. These changes are not visible after minimizing.

Webpack 5将会在使用[contenthash]的时候使用一个真正的文件内容散列。在此之前，它“仅”使用了内部结构的散列。当只更改注释或重命名变量时，这会对长期缓存产生积极影响。这些更改在最小化后是不可见的。



### Module Federation（模块联合）

[https://webpack.docschina.org/concepts/module-federation/](https://webpack.docschina.org/concepts/module-federation/)

[基于 MF 的组件化共享工作流](https://mp.weixin.qq.com/s?__biz=MzIxMzExMjYwOQ==&mid=2651899353&idx=1&sn=2d2b300da8c4a0b0554dff4c2fb7d7da&chksm=8c5fa797bb282e81a9592ede083a0ea3d7d0fafe24744dcb69ad872a17166a6d4d265ad4578b#rd)

Webpack 5 adds a new feature called "Module Federation", which allows multiple webpack builds to work together. From runtime perspective modules from multiple builds will behave like a huge connected module graph. From developer perspective modules can be imported from specified remote builds and used with minimal restrictions.

Webpack 5增加了一个名为“模块联合”的新特性，它允许多个Webpack构建一起工作。从运行时的角度来看，来自多个构建的模块将表现为一个巨大的连接模块图。从开发人员的角度来看，可以从指定的远程构建中导入模块，并以最小的限制使用它们。

[https://juejin.cn/post/6847902220298649607](https://juejin.cn/post/6847902220298649607)

webpack 5引入联邦模式是为了**更好的共享代码**。 在此之前，我们共享代码一般用npm发包来解决。 npm发包需要经历构建，发布，引用三阶段，而联邦模块可以**直接引用其他应用代码**,实现热插拔效果。对比npm的方式更加简洁、快速、方便。

联邦模块和微前端的关系：因为expose这个属性即可以暴露单个组件，也可以把整个应用暴露出去。同时由于share属性存在，技术栈必须一致。所以加上路由，可以用来实现single-spa这种模式的微前端。

使用场景：新建专门的组件应用服务来管理所有组件和应用，其他业务层只需要根据自己业务所需载入对应的组件和功能模块即可。模块管理统一管理，代码质量高，搭建速度快。**特别适用矩阵app，或者可视化页面搭建等场景。**



![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f716494c298941e5bcb4874256d83dba~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

MF 相对于 NPM 共享方式，有哪些优势？

- 组件更新链路更短，实时更新
- 可以实现依赖共享，在运行时动态去判断加载哪一方的依赖
- 不需要重复编译，因为本身 MF 资源是已经编译过的代码





#### 原理

[https://juejin.cn/post/6963326546606030856#heading-7](https://juejin.cn/post/6963326546606030856#heading-7)

[https://github.com/Vincent0700/learning-webpack/blob/master/docs/Webpack%E6%A8%A1%E5%9D%97%E8%81%94%E9%82%A6%E5%8E%9F%E7%90%86.md](https://github.com/Vincent0700/learning-webpack/blob/master/docs/Webpack%E6%A8%A1%E5%9D%97%E8%81%94%E9%82%A6%E5%8E%9F%E7%90%86.md)

下载并执行 `remoteEntry.js`，挂载入口点对象到 `window.app1`，他有两个函数属性，`init` 和 `get`。`init` 方法用于初始化作用域对象 initScope，`get` 方法用于下载 `moduleMap` 中导出的远程模块。

用户 `import` 远程模块时调用 `app1.get(moduleName)` 通过 `Jsonp` 懒加载远程模块，然后缓存在全局对象 window['webpackChunk' + appName]

通过 **webpack_require** 读取缓存中的模块，执行用户回调



### Async modules

Webpack 5 supports so called "async modules". That are modules that do not evaluate synchronously, but are async and Promise-based instead.

Webpack 5支持所谓的“异步模块”。它们是不同步计算的模块，而是基于异步和承诺的。

Importing them via `import` is automatically handled and no additional syntax is needed and difference is hardly notice-able.

Importing them via `require()` will return a Promise that resolves to the exports.

In webpack there are multiple ways to have async modules:

- async externals
- WebAssembly Modules in the new spec
- ECMAScript Modules that are using Top-Level-Await



### Optimization



#### Nested tree-shaking

webpack is now able to track access to nested properties of exports. This can improve Tree Shaking (Unused export elimination and export mangling) when reexporting namespace objects.

webpack现在可以跟踪对导出的嵌套属性的访问。这可以在重新导出名称空间对象时改善摇树(未使用的导出消除和导出破坏)。

```js
// inner.js
export const a = 1;
export const b = 2;

// module.js
export * as inner from './inner';
// or import * as inner from './inner'; export { inner };

// user.js
import * as module from './module';
console.log(module.inner.a);

```

In this example, the export `b` can be removed in production mode.





### NodeJS 的 polyfill 脚本被移除

最开始，Webpack 目标是允许在浏览器中运行 Node 模块。但是现在在 Webpack 看来，大多模块就是专门为前端开发的。在 v4 及以前的版本中，对于大多数的 Node 模块会自动添加 polyfill 脚本，polyfill 会加到最终的 bundle 中，其实通常情况下是没有必要的。在 v5 中将停止这一行为。



作者：BDEEFE
链接：https://juejin.cn/post/6844904169405415432
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。







### **Asset Modules**

[https://webpack.docschina.org/guides/asset-modules/](https://webpack.docschina.org/guides/asset-modules/)

资源模块(asset module)是一种模块类型，它允许使用资源文件（字体，图标等）而无需配置额外 loader。

在 webpack 5 之前，通常使用：

- [`raw-loader`](https://v4.webpack.js.org/loaders/raw-loader/) 将文件导入为字符串
- [`url-loader`](https://v4.webpack.js.org/loaders/url-loader/) 将文件作为 data URI 内联到 bundle 中
- [`file-loader`](https://v4.webpack.js.org/loaders/file-loader/) 将文件发送到输出目录

资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader：

- `asset/resource` 发送一个单独的文件并导出 URL。之前通过使用 `file-loader` 实现。
- `asset/inline` 导出一个资源的 data URI。之前通过使用 `url-loader` 实现。
- `asset/source` 导出资源的源代码。之前通过使用 `raw-loader` 实现。
- `asset` 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 `url-loader`，并且配置资源体积限制实现。





### 处理 ES 导入 CommonJS 

```tsx
(function(modules) {
  // ...
  function __webpack_require__ (moduleId) {
    // ...
  }

  // ...

  __webpack_require__.d = function(exports, name, getter) {
    if(!__webpack_require__.o(exports, name)) {
      Object.defineProperty(exports, name, { enumerable: true, get: getter });
    }
  };

  __webpack_require__.r = function(exports) {
    if(typeof Symbol !== 'undefined' && Symbol.toStringTag) {
      Object.defineProperty(exports, Symbol.toStringTag, { value: 'Module' });
    }
    Object.defineProperty(exports, '__esModule', { value: true }); // <-- 重点
  };

  __webpack_require__.n = function(module) {
    var getter = module && module.__esModule ?
      function getDefault() { return module['default']; } :
      function getModuleExports() { return module; }; // <-- 兼容处理
    __webpack_require__.d(getter, 'a', getter);
    return getter;
  };

  return __webpack_require__(__webpack_require__.s = 0);
})({
  "./mod.js": function (module, exports) {
    function foo () {}
    function bar () {}
    module.exports = foo
    module.exports.bar = bar
  },
  "./index.js": function (module, __webpack_exports__, __webpack_require__) {
;
    __webpack_require__.r(__webpack_exports__); // <-- 标识 ES 模块
    var _mod_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__("./mod.js");
    var _mod_js__WEBPACK_IMPORTED_MODULE_0___default = __webpack_require__.n(_mod_js__WEBPACK_IMPORTED_MODULE_0__);

    console.log(_mod_js__WEBPACK_IMPORTED_MODULE_0__["bar"])
    console.log(_mod_js__WEBPACK_IMPORTED_MODULE_0___default.a)
    console.log(_mod_js__WEBPACK_IMPORTED_MODULE_0___default()())
  },
  0: function (module, exports, __webpack_require__) {
    module.exports = __webpack_require__("./index.js");
  }
  // ...
})
```

如果 __esModule 为 `true` 那么 `a` 就是 `module.exports.default`，Getter 调用也返回 `module.exports.default`，否则 `a` 的值和 Getter 返回值就是 `module.exports`



# 面试

- 总体的文件就是一个 `IIFE`——立即执行函数
- `webpack` 会对加载过的文件进行缓存，从而优化性能
- 主要是通过 `__webpack_require__ `来模拟 `import` 一个模块，并在最后返回模块 `export` 的变量
- `webpack` 是如何支持 `ES Module` 的
- 动态加载 `import()` 的实现主要是使用 `JSONP` 动态加载模块，并通过 `webpackJsonpCallback` 判断加载的结果



## 打包的过程

* 读取文件，分析模块依赖
* 对模块进行解析执行（深度遍历）
* 针对不同的模块使用不同的 loader
* 编译模块，生成抽象语法树（AST）
* 遍历 AST，输出 JS





## 热更新原理

[webpack的热更新原理](https://juejin.cn/post/7038767740059926565)

[Webpack HMR 原理解析](https://zhuanlan.zhihu.com/p/30669007)

[https://juejin.cn/post/7038767740059926565](https://juejin.cn/post/7038767740059926565)

[https://juejin.cn/post/6844904008432222215#heading-0](https://juejin.cn/post/6844904008432222215#heading-0)

启动`websocket`服务，可以建立服务器和浏览器之间的双向通信。当监听到本地代码发生改变时，主动向浏览器发送新`hash`以及`ok`字段。

客户端`websocket`注册和服务端一样注册了两个事件：

- `hash` : `webpack`重新编辑打包后的新`hash`值 。
- `ok` : 进行`reloadApp`热更新检查 。

HMR 成功与否的关键步骤，HotModulePlugin 将会对新旧模块进行对比，决定是否更新模块，在决定更新模块后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用。

热更新会修改webpack.config.js的entry配置

```tsx
{ entry:
    { index: 
        [
            // 获取的clientEntry
            'xxx/node_modules/webpack-dev-server/client/index.js?http://localhost:8080',
            // 获取的hotEntry
            'xxx/node_modules/webpack/hot/dev-server.js',
            // 开发配置的入口
            './src/index.js'
    	],
    },
}      
```

在入口默默增加了 2 个文件，那就意味会一同打包到`bundle`文件中去，也就是线上运行时。

```tsx
// webpack-dev-server/client/index.js
var socket = require('./socket');
var onSocketMessage = {
    hash: function hash(_hash) {
        // 更新currentHash值
        status.currentHash = _hash;
    },
    ok: function ok() {
        sendMessage('Ok');
        // 进行更新检查等操作
        reloadApp(options, status);
    },
};
// 连接服务地址socketUrl，?http://localhost:8080，本地服务地址
socket(socketUrl, onSocketMessage);

function reloadApp() {
	if (hot) {
        log.info('[WDS] App hot update...');
        
        // hotEmitter其实就是EventEmitter的实例
        var hotEmitter = require('webpack/hot/emitter');
        hotEmitter.emit('webpackHotUpdate', currentHash);
    } 
}

```

热更新检查事件是调用`reloadApp`方法。比较奇怪的是，这个方法又利用`node.js`的`EventEmitter`，发出`webpackHotUpdate`消息。这是为什么？为什么不直接进行检查更新呢？

个人理解就是为了更好的维护代码，以及职责划分的更明确。`websocket`仅仅用于客户端（浏览器）和服务端进行通信。而真正做事情的活还是交回给了`webpack`。





## 模块打包运行原理

[https://juejin.cn/post/6943468761575849992](https://juejin.cn/post/6943468761575849992)

首先我们应该简单了解一下`webpack`的整个打包流程：

- 读取`webpack`的配置参数；
- 启动`webpack`，创建`Compiler`对象并开始解析项目；
- 从入口文件（`entry`）开始解析，并且找到其导入的依赖模块，递归遍历分析，形成依赖关系树，组装代码块 `chunk`
- 对不同文件类型的依赖模块文件使用对应的`Loader`进行编译
- 整个过程中`webpack`会通过发布订阅模式，向外抛出一些`hooks`，而`webpack`的插件即可通过监听这些关键的事件节点，执行插件任务进而达到干预输出结果的目的。

其中文件的解析与构建是一个比较复杂的过程，在`webpack`源码中主要依赖于`compiler`和`compilation`两个核心对象实现。

`compiler`对象是一个全局单例，他负责把控整个`webpack`打包的构建流程。 `compilation`对象是每一次构建的上下文对象，它包含了当次构建所需要的所有信息，每次热更新和重新构建，`compiler`都会重新生成一个新的`compilation`对象，负责此次更新的构建过程。

而每个模块间的依赖关系，则依赖于`AST`语法树。每个模块文件在通过`Loader`解析完成之后，会通过`acorn`库生成模块代码的`AST`语法树，通过语法树就可以分析这个模块是否还有依赖的模块，进而继续循环执行下一个模块的编译解析。

## webpack 是如何解决两次引入的

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





## 异步模块原理  (动态导入)

[https://juejin.im/post/6844903888319954952](https://juejin.im/post/6844903888319954952)

[https://juejin.cn/post/7087488024883232799](https://juejin.cn/post/7087488024883232799)

采用JSONP的思路，首先，将动态引入模块单独打成一个js文件；其次，在import执行时创建script标签传入src为引入模块地址；从而实现动态加载的效果，注意，JSONP必然是异步的，所以必须要结合Promise；

```
import被转化成了__webpack_require__.e(/*! import() */ 0)
```

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

promise已经resolve，此时异步模块的代码已经在`modules`上了，所以可以直接加载。

**这里就有两个问题？**

1. **`__webpack_require__` 是根据我们之前传入的 `modules` 来获取 `module` 的，但是，在 `__webpack_require__.e` 中并没有看到有对 `modules` 执行操作的代码。那 `modules` 到底是什么时候被更新的呢？**

2. `promise` 把 `resolve` 和 `reject` 全部存入了 `installedChunks` 中， 并没有在获取异步chunk成功的`onload` 回调中执行 `resolve`，那么，`resolve` 是什么时候被执行的呢?

上面两个问题 我们可以从**webpackJsonpCallback**找到答案

```tsx
function webpackJsonpCallback(data) {
        // chunkid
	var chunkIds = data[0];
       // chunkid对应的模块
	var moreModules = data[1]
	var moduleId, chunkId, i = 0, resolves = [];


	for(;i < chunkIds.length; i++) {
		chunkId = chunkIds[i];
		if(Object.prototype.hasOwnProperty.call(installedChunks, chunkId) && installedChunks[chunkId]) {
                        // 收集chunk对应的resolve方法
			resolves.push(installedChunks[chunkId][0]);
		}
                // 标记该chunk已经加载
		installedChunks[chunkId] = 0;
	}
	for(moduleId in moreModules) {
		if(Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
                         // 添加chunk模块，到全局modules对象中
			modules[moduleId] = moreModules[moduleId];
		}
	}
	if(parentJsonpFunction) parentJsonpFunction(data)

         // 依次执行chunk对应promise的resolve方法
	while(resolves.length) {
		resolves.shift()();
	}
};
```

- 收集chunk对应的resolve方法, 前面执行`__webpack_require__.e`时放在了`installedChunks[chunkId]`里
- 将异步chunk下的所有模块 添加到 全局modules
- 依次执行chunk对应promise的resolve方法





----



详细版本：

```tsx
__webpack_require__.e = function requireEnsure(chunkId) {
	var promises = []

	var installedChunkData = installedChunks[chunkId];

        // 如果这个chunk已经加载过了 就不需要加载了
	if(installedChunkData !== 0) { // 0 means "already installed"
		if(installedChunkData) {
			promises.push(installedChunkData[2]);
		} else {

                        // 为这个chunk创建一个promise
			var promise = new Promise(function(resolve, reject) {
                               // 记录这个chunk对应promise的resolve和reject方法
				installedChunkData = installedChunks[chunkId] = [resolve, reject];
			});

                         // promises数组里添加这个chunk对应的promise
			promises.push(installedChunkData[2] = promise)

                        // ============== 动态创建script =================
			var script = document.createElement('script');
			var onScriptComplete
			script.charset = 'utf-8';
			script.timeout = 120;
			if (__webpack_require__.nc) {
				script.setAttribute("nonce", __webpack_require__.nc);
			}
			script.src = jsonpScriptSrc(chunkId)
			// create error before stack unwound to get useful stacktrace later
			var error = new Error();
                        // =================================================

			onScriptComplete = function (event) {
				// avoid mem leaks in IE.
				script.onerror = script.onload = null;
				clearTimeout(timeout);
				var chunk = installedChunks[chunkId];
				if(chunk !== 0) {
					if(chunk) {
						var errorType = event && (event.type === 'load' ? 'missing' : event.type);
						var realSrc = event && event.target && event.target.src;
						error.message = 'Loading chunk ' + chunkId + ' failed.\n(' + errorType + ': ' + realSrc + ')';
						error.name = 'ChunkLoadError';
						error.type = errorType;
						error.request = realSrc;
						chunk[1](error);
					}
					installedChunks[chunkId] = undefined;
				}
			};
			var timeout = setTimeout(function(){
				onScriptComplete({ type: 'timeout', target: script });
			}, 120000);
			script.onerror = script.onload = onScriptComplete;
			document.head.appendChild(script);
		}
	}
	return Promise.all(promises);
};

```

- 如果chunk没有被加载过，会为这个chunk`创建一个promise对象`
- 将promise对象存在`promises数组`中
- 将promise的`resolve 和 reject`存在`installedChunks[chunkId]`中





![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ef4ee3b49a8473a870421ed1201b98e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp?)






## webpack解析不同的模块

webpack根据webpack.config.js中的入口文件，在入口文件里识别模块依赖，不管这里的模块依赖是用CommonJS写的，还是ES6 Module规范写的，webpack会自动进行分析，并通过转换、编译代码，打包成最终的文件。`最终文件中的模块实现是基于webpack自己实现的webpack_require（es5代码）`，所以打包后的文件可以跑在浏览器上。



同时以上意味着在webapck环境下，你可以只使用ES6 模块语法书写代码（通常我们都是这么做的），也可以使用CommonJS模块语法，甚至可以两者混合使用。**webpack会对各种模块进行语法分析，并做转换编译**



## Webpack 本质

Webpack 本质上是一个函数，它接受一个配置信息作为参数，执行后返回一个 [compiler 对象](https://link.juejin.cn/?target=https%3A%2F%2Fwebpack.js.org%2Fplugins%2Finternal-plugins%2F%23compiler)，调用 `compiler` 对象中的 [run](https://link.juejin.cn/?target=https%3A%2F%2Fwebpack.js.org%2Fapi%2Fnode%2F%23run) 方法就会启动编译。`run` 方法接受一个回调，可以用来查看编译过程中的错误信息或编译信息。

```tsx
const webpack = require("./webpack"); //手写webpack
const webpackOptions = require("./webpack.config.js"); //这里一般会放配置信息
const compiler = webpack(webpackOptions);

compiler.run((err, stats) => {
  console.log(err);
  console.log(
    stats.toJson({
      assets: true, //打印本次编译产出的资源
      chunks: true, //打印本次编译产出的代码块
      modules: true, //打印本次编译产出的模块
    })
  );
});
```







## 打包成es module 和 cjs 的区别

[Webpack 打包commonjs 和esmodule 模块的产物对比](https://juejin.cn/post/7093428816785670152)

`esmodule` 在挂载属性的时候只定义了 `get` 。

```tsx
__webpack_require__.d = (exports, definition) => {
  for (var key in definition) {
    if (
      __webpack_require__.o(definition, key) &&
      !__webpack_require__.o(exports, key)
    ) {
      Object.defineProperty(exports, key, {
        enumerable: true,
        get: definition[key],
      });
    }
  }
};

```

---

两个的打包产物对比：

```tsx
// commonjs
var __webpack_modules__ = {
        "./src/commonjs/add.js": (module, exports) => {
            console.log("add开始引入");
            module.exports.add = (a, b) => {
                return a + b;
            };
            exports.PI = 3.14;
        },
    };

//esmodule
var __webpack_modules__ = {
        "./src/esmodule/add.js": (
            __unused_webpack_module,
            __webpack_exports__,
            __webpack_require__
        ) => {
            __webpack_require__.r(__webpack_exports__);// 标识该模块是 esmodule
            __webpack_require__.d(__webpack_exports__, {// 将该模块里的属性、方法挂到 __webpack_exports__ 上
                add: () => add,
                PI: () => PI,
                default: () => __WEBPACK_DEFAULT_EXPORT__,
            });
            console.log("add开始引入");
            const add = (a, b) => {
                return a + b;
            };
            const PI = 3.14;
            const test = 3;
            const __WEBPACK_DEFAULT_EXPORT__ = test;
        },
    };

```

简单对比了下 `commonjs` 和 `esmodule` 模块的产物，其中 `commonjs` 比较简单，就是普通的导出对象和解构对象。但对于 `esmodule` 的话，导出的每一个属性会映射到一个函数，因此值是可以动态改变的。

此外 `require` 会按我们代码中的顺序执行，但 `import` 会被提升到代码最前边首先执行。







# 问题

## webpack不是内部或者外部命令

首先你需要安装一个全局的webpack

>npm install webpack -g

这样你才可以正确的使用webpack这个命令

## extract-text-webpack-plugin

```
(node:9624) DeprecationWarning: Tapable.plugin is deprecated. Use new API on `.hooks` instead

(node:9624) DeprecationWarning: Tapable.apply is deprecated. Call apply on the plugin directly instead

C:\Users\zsl08.000\Desktop\Vue-Webpack-todo\node_modules\webpack\lib\Chunk.js:460

                throw new Error(

                ^

Error: Chunk.entrypoints: Use Chunks.groupsIterable and filter by instanceof Entrypoint instead
```

可以换成mini-css-extract-plugin

[https://www.npmjs.com/package/mini-css-extract-plugin](https://www.npmjs.com/package/mini-css-extract-plugin)


This plugin extract CSS into separate files. It creates a CSS file per JS file which contains CSS. It supports On-Demand-Loading of CSS and SourceMaps.

It builds on top of a new webpack v4 feature (module types) and requires webpack 4 to work.


Compared to the extract-text-webpack-plugin:

* Async loading
* No duplicate compilation (performance)
* Easier to use
* Specific to CSS

## css独立成文件夹后图片路径错误

[https://blog.csdn.net/logan_LG/article/details/82107390](https://blog.csdn.net/logan_LG/article/details/82107390)


使用webpack构建项目时将css文件独立出来是常见的做法，在webpack4.0以前是使用extract-text-webpack-plugin，

webpack4.0以后使用mini-css-extract-plugin，这里要讲的是后者


经过url-loader处理后的图片是导出到了 dist/img/ 目录下




可以看到url是img/webpack.xxx.jpg，而正确的路径应该是 ../img/webpack.xxx.jpg


如果直接把css文件直接放在dist根目录下是没有问题的，但是按照习惯，我们一般会把css文件单独放到一个css文件夹下。



那么我们怎么解决这个问题呢？

在此之前我们先看看网上见得最多的解决方案，也就是在output的配置中加入一个 publicPath: '../' 配置，



**css中的url路径的确可以了，但是可以看到，控制台出现了更多报错，并且css文件本身也不能正确引入了，因此这种方案在这种情况下是解决不了问题的**。



**由于output中的publicPatch配置会在所有用相对路径引用资源的地方都将上一个 ‘../’因此导致其他原本正确的路径也错了。**



因此正确的解决方案应该是在引入MiniCssExtractPlugin.loader时使用对象方式，并在options目录下添加 publicPath: '../' 配置，这样就只会在css文件中引入的资源中添加 “../”，就不会影响其他文件中的路径了。



```tsx
  module: {
        rules: [
            {
                test: /\.(sa|sc|c)ss$/,
                use: [
                    devMode?'style-loader':{
                        loader:MiniCssExtractPlugin.loader,
                        options: {
                            publicPath: '../'
                        }
                    },
                    'css-loader',
                    //'postcss-loader',
                    //'sass-loader'
                ]
            },
        ]
    },
```

## 别名地址错误

webpack 的别名好处大家也都了解, 但是 vue 的模板中, 对图片地址使用别名时总出现问题

```tsx
alias: {
  'src': path.resolve(__dirname, '../src'),
  'assets': path.resolve(__dirname, '../src/assets'),
  'components': path.resolve(__dirname, '../src/components')
}
```

```html
<template>
  <img src="assets/images/logo.jpg" />
</template>

<script>
	import 'assets/css/style.css'
</script>

<style>
.logo {
  background: url(asset/images/bg.jpg)
}
</style>
```



上面的代码, 你会发现只有引入style.css是成功的, 图片地址和背景图片地址都会解析失败...

经过各种搜索找原因(这时候, 你会发现百度搜索这些技术型的内容, 真是垃圾中的战斗机), 最终还是找到原因了...

**vue-html-loader and css-loader translates non-root URLs to relative paths. In order to treat it like a module path, prefix it with ~**

**就是要在别名前面加一个~**


改成这样:

```html
<template>
    <img src="~assets/images/logo.jpg" />
</template>

<script>
	import 'assets/css/style.css'
</script>

<style>
.logo {
    background: url(~asset/images/bg.jpg)
}
</style>
```

意思就是: 告诉加载器它是一个模块，而不是相对路径


注意: 只有在template中的静态文件地址和style中的静态文件地址需要加~, 在script里的, 别名定义成什么就写什么.

## Entrypoint undefined

[webpack Entrypoint undefined = index.html](https://www.cnblogs.com/guangzhou11/p/11427354.html)



`module.exports`增加配置`stats: { children: false }`即可解决；

## Cannot assign to read only property

```
webpack报错：Cannot assign to read only property 'exports' of object '#<Object>'
```

The code above is ok. You can mix require and export. You can‘t mix import and module.exports.

也就是说，在webpack打包的时候，可以在js文件中混用require和export。**但是不能混用import 以及module.exports。**

## node-sass 安装失败

```tsx
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```



## **Can't resolve 'crypto'**

```
ERROR in ../../node_modules/agora-access-token/src/AccessToken.js 1:13-30

Module not found: Error: Can't resolve 'crypto' in '/Users/qz/Documents/company_project/cloudclass-desktop/node_modules/agora-access-token/src'
```



```ts
BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
	- add a fallback 'resolve.fallback: { "crypto": require.resolve("crypto-browserify") }'
	- install 'crypto-browserify'
If you don't want to include a polyfill, you can use an empty module like this:
	resolve.fallback: { "crypto": false }
```





在运行过程中出现了很多这样的报错信息，是由于在webpack5中移除了nodejs核心模块的polyfill自动引入，所以需要手动引入，如果打包过程中有使用到nodejs核心模块，webpack会提示进行相应配置

```tsx
  // webpack.config.js
  module.exports = {
    ...
    resolve: {
      // https://github.com/babel/babel/issues/8462
      // https://blog.csdn.net/qq_39807732/article/details/110089893
      // 如果确认需要node polyfill，设置resolve.fallback安装对应的依赖
      fallback: {
        crypto: require.resolve('crypto-browserify'),
        path: require.resolve('path-browserify'),
        url: require.resolve('url'),
        buffer: require.resolve('buffer/'),
        util: require.resolve('util/'),
        stream: require.resolve('stream-browserify/'),
        vm: require.resolve('vm-browserify')
      },
    }
  }
```

或者

```tsx
  // webpack.config.js
  module.exports = {
    ...
    resolve: {
      // 如果确认不需要node polyfill，设置resolve.alias设置为false
      alias: {
        crypto: false
      }
    }
  }
```





## include polyfills for node.js core modules

同上

## omit LICENSE.txt

[https://stackoverflow.com/questions/64818489/webpack-omit-creation-of-license-txt-files](https://stackoverflow.com/questions/64818489/webpack-omit-creation-of-license-txt-files)

webpack 5  打包会生成LICENSE.txt  如何可以忽略这个文件的生成呢？

```tsx
  optimization: {
    minimizer: [new TerserPlugin({
      extractComments: false,
    })],
  },
```

## ReferenceError: Buffer is not defined

https://github.com/agoncal/swagger-ui-angular6/issues/2

```tsx
 "devDependencies": {
    "buffer": "6.0.3"
  }
```

webpack.config

```tsx
   plugins: [
      new webpack.ProvidePlugin({
        process: 'process/browser',
        Buffer: ['buffer', 'Buffer'],
      }),
    ],
```

也有可能不需要自己引入Buffer

举个例子:

https://hyrious.me/npm-browser/?q=atob@2.1.2/package/package.json:18

```ts
  "main": "node-atob.js",
  "browser": "browser-atob.js",
```

我们应该拿browser的

nodeResolve browser  以浏览器的形式查找

解决：举一个rollup插件解决的

```ts
import { nodeResolve } from "@rollup/plugin-node-resolve";

nodeResolve({
      browser: true,
      mainFields: ['browser', 'module', 'main'],
      preferBuiltins: false,
      extensions: [".mjs", ".js", ".json", ".ts"]
    }),
```





## not provide export named 'default'

[https://bobbyhadz.com/blog/javascript-requested-module-not-provide-export-named-default](https://bobbyhadz.com/blog/javascript-requested-module-not-provide-export-named-default)

例子：

```ts
// index.js
// 👇️ named export
export function sum(a, b) {
  return a + b;
}
```

引入：

```tsx
// ⛔️ The requested module './index.js' does not provide
// an export named 'default'
import sum from './index.js';
console.log(sum(10, 10));
```



## 引入 mjs 文件

**ModuleNotFoundError: Module not found**

[https://stackoverflow.com/questions/70964723/webpack-5-in-ceate-react-app-cant-resolve-not-fully-specified-routes](https://stackoverflow.com/questions/70964723/webpack-5-in-ceate-react-app-cant-resolve-not-fully-specified-routes)

[https://webpack.js.org/configuration/module/#resolvefullyspecified](https://webpack.js.org/configuration/module/#resolvefullyspecified)

When enabled, you should provide the file extension when `import`ing a module in `.mjs` files or any other `.js` files when their nearest parent `package.json` file contains a `"type"` field with a value of `"module"`, otherwise webpack would fail the compiling with a `Module not found` error

在导入.mjs文件或任何其他.js文件中最接近父包的模块时，您应该提供文件扩展名。json文件包含一个值为"module"的"type"字段，否则webpack将编译失败，并显示一个"module not found "错误。

```ts
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.m?js$/,
        resolve: {
          fullySpecified: false, // disable the behaviour
        },
      },
    ],
  },
};
```









