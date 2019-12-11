---
layout:     post
title:      "webpack中的plugin"
date:       2018-05-19 15:34:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Webpack
---

> “Yeah It's on. ”


# 前文

[https://www.webpackjs.com/concepts/plugins/#%E5%89%96%E6%9E%90](https://www.webpackjs.com/concepts/plugins/#%E5%89%96%E6%9E%90)



插件是 webpack 的支柱功能。webpack 自身也是构建于，你在 webpack 配置中用到的相同的插件系统之上！

插件目的在于解决 loader 无法实现的其他事。


--------------------


**剖析**

webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。

```javascript
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
    apply(compiler) {
        compiler.hooks.run.tap(pluginName, compilation => {
            console.log("webpack 构建过程开始！");
        });
    }
}
```

>compiler hook 的 tap 方法的第一个参数，应该是驼峰式命名的插件名称。建议为此使用一个常量，以便它可以在所有 hook 中复用。







# 正文

## 常用的plugin

### CommonsChunkPlugin
[https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE](https://zhuanlan.zhihu.com/p/26710831?refer=ElemeFE)

CommonsChunkPlugin 插件，是一个可选的用于建立一个独立文件(又称作 chunk)的功能，这个文件包括多个入口 chunk 的公共模块。

>The CommonsChunkPlugin 已经从 webpack v4 legato 中移除。想要了解在最新版本中如何处理 chunk，请查看 SplitChunksPlugin。 



通过将公共模块拆出来，最终合成的文件能够在最开始的时候加载一次，便存到缓存中供后续使用。这个带来速度上的提升，因为浏览器会迅速将公共的代码从缓存中取出来，而不是每次访问一个新页面时，再去加载一个更大的文件。



 vendor chunk 里面包含了 webpack 的 runtime 代码（用来解析和加载模块之类的运行时代码）,这样会导致vendor打包的hash值一直在改变，所以要把runtime 代码提取出来
 
```javascript
    // extract webpack runtime and module manifest to its own file in order to
    // prevent vendor hash from being updated whenever app bundle is updated
    new webpack.optimize.CommonsChunkPlugin({
      name: 'manifest',
      chunks: ['vendor']
    })
```

新版vue-cli中的webpack配置
```javascript
    // split vendor js into its own file
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      minChunks: function (module) {
        // any required modules inside node_modules are extracted to vendor
        //模块是来自 node_modules 目录的
        //都移到 vendor chunk 里去
        return (
          module.resource &&
          /\.(js|vue|styl|ttf|woff)$/.test(module.resource) &&
          module.resource.indexOf(
            path.join(__dirname, '../node_modules')
          ) === 0
        )
      }
    }),
    // extract webpack runtime and module manifest to its own file in order to
    // prevent vendor hash from being updated whenever app bundle is updated
    new webpack.optimize.CommonsChunkPlugin({
      name: 'manifest',
      minChunks: Infinity
    }),
    // This instance extracts shared chunks from code splitted chunks and bundles them
    // in a separate chunk, similar to the vendor chunk
    // see: https://webpack.js.org/plugins/commons-chunk-plugin/#extra-async-commons-chunk
    new webpack.optimize.CommonsChunkPlugin({
      name: 'app',
      async: 'vendor-async',
      children: true,
      minChunks: 3
    }),
```

用到 minChunks想把所有 node_modules 目录下的所有 .js 都自动分离到 vendor.js


----------


为了Dynamic Import时抽取出一些chunk中共有的模块，我们需要用到 CommonsChunkPlugin 的 async  (上面就是一个很好的例子)

```javascript
// webpack.config.js

new webpack.optimize.CommonsChunkPlugin({
  async: 'common-in-lazy',  //抽取出来的chunk的名字
  minChunks: ({ resource } = {}) => (
    resource &&
    resource.includes('node_modules') &&
    /axios/.test(resource)
  ),
}),
```

Webpack在所有的 async chunk 中，找到来自 node_modules ，并且名字带有 axios 的模块。



>例子Emoji.chunk.js 和 Photos.chunk.js 都包含了 axios ，所以把他移动到名叫 common-in-lazy 的 chunk 中(如果common-in-lazy chunk 并不存在，那就新建一个吧)


**所有的 async chunk ，就是 import() 产生的 chunk**




### mini-css-extract-plugin
**This plugin extracts CSS into separate files.**


 It creates a CSS file per JS file which contains CSS. It supports On-Demand-Loading of CSS and SourceMaps.



**It builds on top of a new webpack v4 feature (module types) and requires webpack 4 to work.**



(与extract-text-webpack-plugin相比：)
Compared to the extract-text-webpack-plugin:


* Async loading (异步加载)
* No duplicate compilation (performance)               没有重复的编译（性能）
* Easier to use  更容易使用
* Specific to CSS  特定于CSS


```javascript
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: "[name].css",
      chunkFilename: "[id].css"
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              // you can specify a publicPath here
              // by default it use publicPath in webpackOptions.output
              publicPath: '../'
            }
          },
          "css-loader"
        ]
      }
    ]
  }
}



```




### extract-text-webpack-plugin
Extract text from a bundle, or bundles, into a separate file.(提取文本到单独的文件)

```javascript
const ExtractTextPlugin = require("extract-text-webpack-plugin");
 
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin("styles.css"),
  ]
}
```

它将*.css输入块中的所有必需模块移动到单独的CSS文件中。所以你的样式不再被内联到JS包中，而是在一个单独的CSS文件（styles.css）中。如果您的样式表总量很大，那么它会更快，因为CSS包与JS包并行加载。



#### Options
**allChunks 	{Boolean}** 

Extract from all additional chunks too (by default it extracts only from the initial chunk(s))
When using CommonsChunkPlugin and there are extracted chunks (from ExtractTextPlugin.extract) **in the commons chunk, allChunks must be set to true**


----------


**filename	{String\|Function}**


结果文件的名称。可能含有[name]，[id]和[contenthash]

* [name] name of the chunk
* [id] number of the chunk
* [contenthash] hash of the content of the extracted file(提取文件内容的散列)
* [<hashType>:contenthash:<digestType>:<length>] 您可以选择配置
  * other hashTypes, e.g. sha1, md5, sha256, sha512
  * ther digestTypes, e.g. hex, base26, base32, base36, base49, base52, base58, base62, base64
  * and length, the length of the hash in chars



#### #extract
ExtractTextPlugin.extract(options: loader | object)

Creates an extracting loader from an existing loader


----------


options.use	  **{String}/ {Array}/{Object}**

应该用于将资源转换为CSS导出模块的加载程序（必需）


----------


options.fallback	**{String}/ {Array}/{Object}**

加载器（例如'style-loader'），当CSS没有被提取时应该被使用（例如在一个额外的块中allChunks: false）


----------


options.publicPath	{String}

覆盖publicPath此加载器的设置


----------


### html-webpack-plugin
Plugin that simplifies creation of HTML files to serve your bundles(简化创建HTML文件)

这是一个webpack插件，它可以简化创建HTML文件来为你的webpack包提供服务。这对于webpack在文件名中包含散列的bundle 来说尤其有用，它可以改变每个编译。您可以让插件为您生成一个HTML文件，使用lodash模板提供您自己的模板或使用您自己的加载器。



The html-webpack-plugin provides **hooks** to extend it to your needs.


The plugin will generate an HTML5 file for you that includes all your webpack bundles in the body using script tags. Just add the plugin to your webpack config as follows:



webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
 
module.exports = {
  entry: 'index.js',
  output: {
    path: __dirname + '/dist',
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin()
  ]
}
```
This will generate a file dist/index.html containing the following
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script> 
  </body>
</html>
```



--------



[https://www.jianshu.com/p/08a60756ffda](https://www.jianshu.com/p/08a60756ffda)

```
let srcPath = path.resolve(__dirname, '../src')
let icoPath = path.resolve(srcPath, 'common/images/favicon.ico')




  plugins: [
    new HtmlWebpackPlugin({ // 打包输出HTML
      title: 'Hello World app',
      minify: { // 压缩HTML文件
        removeComments: true, // 移除HTML中的注释
        collapseWhitespace: true, // 删除空白符与换行符
        minifyCSS: true// 压缩内联css
      },
      filename: 'index.html',
      template: 'index.html',
      favicon: icoPath
    }),
  ]
```


----------------


 **title属性不起作用**
 
 
 
[https://segmentfault.com/q/1010000004555431](https://segmentfault.com/q/1010000004555431)



 
应该是webpack.config.js的配置文件里面加了 html-loader，加了之后会正常解析html文件作为模版，就会直接把 `<%= htmlWebpackPlugin.options.title %>`解析成字符串。如果有html-loader ,去掉就可以了







### HashedModuleIdsPlugin 
[https://zhuanlan.zhihu.com/p/27710902](https://zhuanlan.zhihu.com/p/27710902)


![enter description here][1]

keep module.id stable when vender modules does not change


webpack 里每个模块都有一个 module id ，module id 是该模块在模块依赖关系图里按顺序分配的序号，如果这个 module id 发生了变化，那么他的 chunkhash 也会发生变化。


HashedModuleIdsPlugin是根据模块所在路径来映射其 module id ，这样就算引入了新的模块，也不会影响 module id 的值，只要模块的路径不改变的话。

```javascript
// webpack.config.js

plugins: [
  new webpack.HashedModuleIdsPlugin(),
  // ...
],
```



**这样修改了某个模块的代码，就不会破坏其他模块的缓存，这就是我们想要实现的持久性缓存**

```
                    Asset       Size  Chunk Names
common-in-lazy.fbe5ebcb.chunk.js    11.9 kB  common-in-lazy
    used-twice.166ea824.chunk.js    17.2 kB  used-twice
        Photos.c2430756.chunk.js    8.66 kB  Photos
         Emoji.96ddcf33.chunk.js     1.2 kB  Emoji
                 app.6dd02fc7.js    2.81 kB  app
              vendor.794774d5.js     103 kB  vendor
            manifast.31b01d25.js    1.54 kB  manifast
```

只有正真代码变化的模块hash值才会改变






### CopyWebpackPlugin
将单个文件或整个目录复制到构建目录

webpack.config.js
```javascript
const CopyWebpackPlugin = require('copy-webpack-plugin')
 
const config = {
  plugins: [
    new CopyWebpackPlugin([ ...patterns ], options)
  ]
}
```

#### Patterns

A simple pattern looks like this
```
{ from: 'source', to: 'dest' }
```

Or, in case of just a from with the default destination, you can also use a {String} as shorthand instead of an {Object}

```
'source'
```


### BundleAnalyzerPlugin 

使用交互式可缩放树形图可视化webpack输出文件的大小。

```javascript
if (config.build.bundleAnalyzerReport) {
  const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
  webpackConfig.plugins.push(new BundleAnalyzerPlugin())
}
```

This module will help you:

1. Realize what's really inside your bundle
2. Find out what modules make up the most of its size
3. Find modules that got there by mistake
4. Optimize it!




And the best thing is it supports minified bundles! It parses them to get real size of bundled modules. And it also shows their gzipped sizes!




### HashedModuleIdsPlugin



```javascript
 // keep module.id stable when vendor modules does not change
    new webpack.HashedModuleIdsPlugin(),
```


增加、删除一些模块，可能会导致不相关文件的 hash 发生变化，这是因为 webpack 打包时，按照导入模块的顺序，module.id 自增，会导致某些模块的 module.id 发生变化，进而导致文件的 hash 变化。


解决方式： 使用 webpack 内置的 HashedModuleIdsPlugin，该插件基于导入模块的相对路径生成相应的 module.id，这样如果内容没有变化加上 module.id 也没变化，则生成的 hash 也就不会变化了。




### NamedModulesPlugin

[https://www.jianshu.com/p/8499842defbe](https://www.jianshu.com/p/8499842defbe)

当开启 HMR 的时候使用该插件会显示模块的相对路径，建议用于开发环境。

```javascript
new webpack.NamedModulesPlugin()
```




### NoEmitOnErrorsPlugin 

[https://segmentfault.com/q/1010000013357755/a-1020000013363200](https://segmentfault.com/q/1010000013357755/a-1020000013363200)

在编译出现错误时，使用 NoEmitOnErrorsPlugin 来跳过输出阶段。这样可以确保输出资源不会包含错误。对于所有资源，统计资料(stat)的 emitted 标识都是 false。





### webpack-dev-middleware

[https://segmentfault.com/a/1190000011761306](https://segmentfault.com/a/1190000011761306)





我们在使用webpack 编译文件时，每次改动文件都要去重新编译，是不是很麻烦，这时候我们就用到了webpack-dev-middleware 插件，该插件对更改的文件进行监控，编译, 一般和 webpack-hot-middleware 配合使用，实现热加载功能





### @vue/preload-webpack-plugin


[https://www.npmjs.com/package/@vue/preload-webpack-plugin](https://www.npmjs.com/package/@vue/preload-webpack-plugin)



* Uses a combination of htmlWebpackPluginBeforeHtmlProcessing and htmlWebpackPluginAlterAssetTags hooks to inject links as objects rather than strings. This allows for more flexibility when the tags need to be altered by other plugins.
* include option can be an object in the shape of { type?, chunks?, entries? }. For example, to prefetch async chunks for a specific entry point:

```javascript
{
  rel: 'prefetch',
  include: {
    type: 'asyncChunks',
    entries: ['app']
  }
}
```

**A Webpack plugin for automatically wiring up asynchronous (and other types) of JavaScript chunks using `<link rel='preload'>`. This helps with lazy-loading.**


>Note: This is an extension plugin for html-webpack-plugin - a plugin that simplifies the creation of HTML files to serve your webpack bundles.




## 编写plugin


Webpack 通过 Plugin 机制让其更加灵活，以适应各种应用场景。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

一个最基础的 Plugin 的代码是这样的：

```javascript
class BasicPlugin{
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options){
  }

  // Webpack 会调用 BasicPlugin 实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler){
    compiler.plugin('compilation',function(compilation) {
    })
  }
}

// 导出 Plugin
module.exports = BasicPlugin;
```


在使用这个 Plugin 时，相关配置代码如下：


```javascript
const BasicPlugin = require('./BasicPlugin.js');
module.export = {
  plugins:[
    new BasicPlugin(options),
  ]
}
```



### Compiler 和 Compilation

在开发 Plugin 时最常用的两个对象就是 Compiler 和 Compilation，它们是 Plugin 和 Webpack 之间的桥梁。 Compiler 和 Compilation 的含义如下：





* **Compiler 对象包含了 Webpack 环境所有的的配置信息**，包含 options，loaders，plugins 这些信息，这个对象在 Webpack 启动时候被实例化，它是全局唯一的，可以简单地把它理解为 Webpack 实例；
* **Compilation 对象包含了当前的模块资源、编译生成资源、变化的文件等**。当 Webpack 以开发模式运行时，每当检测到一个文件变化，一次新的 Compilation 将被创建。Compilation 对象也提供了很多事件回调供插件做扩展。通过 Compilation 也能读取到 Compiler 对象。




**Compiler 和 Compilation 的区别在于：Compiler 代表了整个 Webpack 从启动到关闭的生命周期，而 Compilation 只是代表了一次新的编译。**





### 事件流

Webpack 就像一条生产线，要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的，多个流程之间有存在依赖关系，只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能，在特定的时机对生产线上的资源做处理。


**Webpack 通过 Tapable 来组织这条复杂的生产线。 Webpack 在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条生产线中，去改变生产线的运作。 Webpack 的事件流机制保证了插件的有序性，使得整个系统扩展性很好。**






Webpack 的事件流机制应用了观察者模式，和 Node.js 中的 EventEmitter 非常相似。Compiler 和 Compilation 都继承自 Tapable，可以直接在 Compiler 和 Compilation 对象上广播和监听事件，方法如下：


```javascript
/**
* 广播出事件
* event-name 为事件名称，注意不要和现有的事件重名
* params 为附带的参数
*/
compiler.apply('event-name',params);

/**
* 监听名称为 event-name 的事件，当 event-name 事件发生时，函数就会被执行。
* 同时函数中的 params 参数为广播事件时附带的参数。
*/
compiler.plugin('event-name',function(params) {

});


```


同理，compilation.apply 和 compilation.plugin 使用方法和上面一致。



在开发插件时，你可能会不知道该如何下手，因为你不知道该监听哪个事件才能完成任务。

在开发插件时，还需要注意以下两点：



* 只要能拿到 Compiler 或 Compilation 对象，就能广播出新的事件，所以在新开发的插件中也能广播出事件，给其它插件监听使用。
* 传给每个插件的 Compiler 和 Compilation 对象都是同一个引用。也就是说在一个插件中修改了 Compiler 或 Compilation 对象上的属性，会影响到后面的插件。
* 有些事件是异步的，这些异步的事件会附带两个参数，第二个参数为回调函数，在插件处理完任务时需要调用回调函数通知 Webpack，才会进入下一处理流程。例如：



```javascript
 compiler.plugin('emit',function(compilation, callback) {
    // 支持处理逻辑

    // 处理完毕后执行 callback 以通知 Webpack 
    // 如果不执行 callback，运行流程将会一直卡在这不往下执行 
    callback();
  });
```



### 常用 API


插件可以用来修改输出文件、增加输出文件、甚至可以提升 Webpack 性能、等等，总之插件通过调用 Webpack 提供的 API 能完成很多事情。 由于 Webpack 提供的 API 非常多，有很多 API 很少用的上，又加上篇幅有限，下面来介绍一些常用的 API。

**读取输出资源、代码块、模块及其依赖**


有些插件可能需要读取 Webpack 的处理结果，例如输出资源、代码块、模块及其依赖，以便做下一步处理。


在 emit 事件发生时，代表源文件的转换和组装已经完成，在这里可以读取到最终将输出的资源、代码块、模块及其依赖，并且可以修改输出资源的内容


```javascript
class Plugin {
  apply(compiler) {
    compiler.plugin('emit', function (compilation, callback) {
      // compilation.chunks 存放所有代码块，是一个数组
      compilation.chunks.forEach(function (chunk) {
        // chunk 代表一个代码块
        // 代码块由多个模块组成，通过 chunk.forEachModule 能读取组成代码块的每个模块
        chunk.forEachModule(function (module) {
          // module 代表一个模块
          // module.fileDependencies 存放当前模块的所有依赖的文件路径，是一个数组
          module.fileDependencies.forEach(function (filepath) {
          });
        });

        // Webpack 会根据 Chunk 去生成输出的文件资源，每个 Chunk 都对应一个及其以上的输出文件
        // 例如在 Chunk 中包含了 CSS 模块并且使用了 ExtractTextPlugin 时，
        // 该 Chunk 就会生成 .js 和 .css 两个文件
        chunk.files.forEach(function (filename) {
          // compilation.assets 存放当前所有即将输出的资源
          // 调用一个输出资源的 source() 方法能获取到输出资源的内容
          let source = compilation.assets[filename].source();
        });
      });

      // 这是一个异步事件，要记得调用 callback 通知 Webpack 本次事件监听处理结束。
      // 如果忘记了调用 callback，Webpack 将一直卡在这里而不会往后执行。
      callback();
    })
  }
}
```



**监听文件变化**



Webpack 会从配置的入口模块出发，依次找出所有的依赖模块，当入口模块或者其依赖的模块发生变化时， 就会触发一次新的 Compilation。





在开发插件时经常需要知道是哪个文件发生变化导致了新的 Compilation，为此可以使用如下代码：


```javascript
// 当依赖的文件发生变化时会触发 watch-run 事件
compiler.plugin('watch-run', (watching, callback) => {
    // 获取发生变化的文件列表
    const changedFiles = watching.compiler.watchFileSystem.watcher.mtimes;
    // changedFiles 格式为键值对，键为发生变化的文件路径。
    if (changedFiles[filePath] !== undefined) {
      // filePath 对应的文件发生了变化
    }
    callback();
});
```




默认情况下 Webpack 只会监视入口和其依赖的模块是否发生变化，在有些情况下项目可能需要引入新的文件，例如引入一个 HTML 文件。 由于 JavaScript 文件不会去导入 HTML 文件，Webpack 就不会监听 HTML 文件的变化，编辑 HTML 文件时就不会重新触发新的 Compilation。 为了监听 HTML 文件的变化，我们需要把 HTML 文件加入到依赖列表中，为此可以使用如下代码：


```javascript
compiler.plugin('after-compile', (compilation, callback) => {
  // 把 HTML 文件添加到文件依赖列表，好让 Webpack 去监听 HTML 模块文件，在 HTML 模版文件发生变化时重新启动一次编译
    compilation.fileDependencies.push(filePath);
    callback();
});
```



**修改输出资源**


有些场景下插件需要修改、增加、删除输出的资源，要做到这点需要监听 emit 事件，因为发生 emit 事件时所有模块的转换和代码块对应的文件已经生成好， 需要输出的资源即将输出，因此 emit 事件是修改 Webpack 输出资源的最后时机。


所有需要输出的资源会存放在 compilation.assets 中，compilation.assets 是一个键值对，键为需要输出的文件名称，值为文件对应的内容。

设置 compilation.assets 的代码如下：

```javascript
compiler.plugin('emit', (compilation, callback) => {
  // 设置名称为 fileName 的输出资源
  compilation.assets[fileName] = {
    // 返回文件内容
    source: () => {
      // fileContent 既可以是代表文本文件的字符串，也可以是代表二进制文件的 Buffer
      return fileContent;
      },
    // 返回文件大小
      size: () => {
      return Buffer.byteLength(fileContent, 'utf8');
    }
  };
  callback();
});
```



读取 compilation.assets 的代码如下：


```javascript
compiler.plugin('emit', (compilation, callback) => {
  // 读取名称为 fileName 的输出资源
  const asset = compilation.assets[fileName];
  // 获取输出资源的内容
  asset.source();
  // 获取输出资源的文件大小
  asset.size();
  callback();
});
```


**判断 Webpack 使用了哪些插件**



在开发一个插件时可能需要根据当前配置是否使用了其它某个插件而做下一步决定，因此需要读取 Webpack 当前的插件配置情况。 以判断当前是否使用了 ExtractTextPlugin 为例，可以使用如下代码：


```javascript
// 判断当前配置使用使用了 ExtractTextPlugin，
// compiler 参数即为 Webpack 在 apply(compiler) 中传入的参数
function hasExtractTextPlugin(compiler) {
  // 当前配置所有使用的插件列表
  const plugins = compiler.options.plugins;
  // 去 plugins 中寻找有没有 ExtractTextPlugin 的实例
  return plugins.find(plugin=>plugin.__proto__.constructor === ExtractTextPlugin) != null;
}
```

### 实战


该插件的名称取名叫 EndWebpackPlugin，作用是在 Webpack 即将退出时再附加一些额外的操作，例如在 Webpack 成功编译和输出了文件后执行发布操作把输出的文件上传到服务器。 同时该插件还能区分 Webpack 构建是否执行成功。使用该插件时方法如下：




```javascript
module.exports = {
  plugins:[
    // 在初始化 EndWebpackPlugin 时传入了两个参数，分别是在成功时的回调函数和失败时的回调函数；
    new EndWebpackPlugin(() => {
      // Webpack 构建成功，并且文件输出了后会执行到这里，在这里可以做发布文件操作
    }, (err) => {
      // Webpack 构建失败，err 是导致错误的原因
      console.error(err);        
    })
  ]
}

```

要实现该插件，需要借助两个事件：


* done：在成功构建并且输出了文件后，Webpack 即将退出时发生；
* failed：在构建出现异常导致构建失败，Webpack 即将退出时发生；



实现该插件非常简单，完整代码如下：


```javascript
class EndWebpackPlugin {

  constructor(doneCallback, failCallback) {
    // 存下在构造函数中传入的回调函数
    this.doneCallback = doneCallback;
    this.failCallback = failCallback;
  }

  apply(compiler) {
    compiler.plugin('done', (stats) => {
        // 在 done 事件中回调 doneCallback
        this.doneCallback(stats);
    });
    compiler.plugin('failed', (err) => {
        // 在 failed 事件中回调 failCallback
        this.failCallback(err);
    });
  }
}
// 导出插件 
module.exports = EndWebpackPlugin;
```


**从开发这个插件可以看出，找到合适的事件点去完成功能在开发插件时显得尤为重要。 在 工作原理概括 中详细介绍过 Webpack 在运行过程中广播出常用事件，你可以从中找到你需要的事件。**



### 调试 Webpack
在编写 Webpack 的 Plugin 和 Loader 时，可能执行结果会和你预期的不一样，就和你平时写代码遇到了奇怪的 Bug 一样。 对于无法一眼看出问题的 Bug，通常需要调试程序源码才能找出问题所在。




虽然可以通过 console.log 的方式完成调试，但这种方法非常不方便也不优雅，本节将教你如何断点调试 工作原理概括 中的插件代码。 由于 Webpack 运行在 Node.js 之上，调试 Webpack 就相对于调试 Node.js 程序。


## 关于Tapable














  [1]: https://pic2.zhimg.com/80/v2-5a4b6bef5809e00512873d481a3670e7_hd.jpg