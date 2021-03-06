---
layout:     post
title:      "构建自己的vue ui库"
date:       2019-03-22 15:51:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”


>今天是3月22号,闲得蛋疼，逛了逛掘金，发现有关vue组件库，猛然想起自己一直想要做个ui组件库，这就开始了折腾之旅


## 正文

[ue cli3 库模式搭建组件库并发布到 npm](https://juejin.im/post/5bbab9de5188255c8c0cb0e3)



[用vue-cli3从0打造一个完整的UI库](https://juejin.im/post/5c934511f265da60d82dc7ab)



[vue-cli 3.0 构建目标：库](https://cli.vuejs.org/zh/guide/build-targets.html#%E5%BA%94%E7%94%A8)


[自己的组件库](https://github.com/QinZhen001/qz-ui)


---

文件的目录

* examples 使用案例，用于做成静态页面展示
* packages 组件编写的地方
* lib  打包后的库，用于扔上npm 




---


### vue-cli3.0 构建目标：库


在库模式中，Vue 是外置的。这意味着包中不会有 Vue，即便你在代码中导入了 Vue。如果这个库会通过一个打包器使用，它将尝试通过打包器以依赖的方式加载 Vue；否则就会回退到一个全局的 Vue 变量。


你可以通过下面的命令将一个单独的入口构建为一个库：

```
vue-cli-service build --target lib --name myLib [entry]
```


在 Web Components 模式中，Vue 是外置的。这意味着包中不会有 Vue，即便你在代码中导入了 Vue。这里的包会假设在页面中已经有一个可用的全局变量 Vue。

### Git Hook
@vue/cli-service 也会安装 yorkie，它会让你在 package.json 的 gitHooks 字段中方便地指定 Git hook：



```javascript
{
  "gitHooks": {
    "pre-commit": "lint-staged"
  }
}
```



### vue.config.js

>cli3 提供一个可选的 vue.config.js 配置文件。如果这个文件存在则他会被自动加载，所有的对项目和webpack的配置，都在这个文件中。


新版 Vue CLI 支持使用 vue.config.js 中的 pages 选项构建一个多页面的应用。


这里使用 pages 修改入口到 examples



```javascript
module.exports = {
  // 修改 src 目录 为 examples 目录
  pages: {
    index: {
      entry: 'examples/main.js',
      template: 'public/index.html',
      filename: 'index.html'
    }
  }
}

```


支持对 packages 目录的处理，修改配置中的 chainWebpack 选项


chainWebpack 是一个函数，会接收一个基于 webpack-chain 的 ChainableConfig 实例。允许对内部的 webpack 配置进行更细粒度的修改。


```javascript
module.exports = {
  // 修改 src 为 examples
  pages: {
    index: {
      entry: 'examples/main.js',
      template: 'public/index.html',
      filename: 'index.html'
    }
  },
  // 扩展 webpack 配置，使 packages 加入编译
  chainWebpack: config => {
    config.module
      .rule('js')
      .include
        .add('/packages')
        .end()
      .use('babel')
        .loader('babel-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
  }
}
```


### webpack-chain
[https://segmentfault.com/a/1190000017547171?utm_source=tag-newest](https://segmentfault.com/a/1190000017547171?utm_source=tag-newest)


webpack 的核心配置的创建和修改基于一个有潜在难于处理的 JavaScript 对象。虽然这对于配置单个项目来说还是 OK 的，但当你尝试跨项目共享这些对象并使其进行后续的修改就会变的混乱不堪，因为您需要深入了解底层对象的结构以进行这些更改。

webpack-chain 尝试通过提供可链式或顺流式的 API 创建和修改webpack 配置。API的 Key 部分可以由用户指定的名称引用，这有助于 跨项目修改配置方式 的标准化。

>webpack-chain 需要 Node.js v6.9及更高版本. 
>webpack-chain 也只创建并被设计于使用webpack的2，3，4版本的配置对象。



```
npm install --save-dev webpack-chain
```


当你安装了 webpack-chain， 你就可以开始创建一个webpack的配置。 对于本指南，我们的示例基本配置 webpack.config.js 将位于我们项目的根目录。


```
// 导入 webpack-chain 模块，该模块导出了一个用于创建一个webpack配置API的单一构造函数。
const Config = require('webpack-chain');

// 对该单一构造函数创建一个新的配置实例
const config = new Config();

// 用链式API改变配置
// 每个API的调用都会跟踪对存储配置的更改。

config
  // 修改 entry 配置
  .entry('index')
    .add('src/index.js')
    .end()
  // 修改 output 配置
  .output
    .path('dist')
    .filename('[name].bundle.js');

// 创建一个具名规则，以后用来修改规则
config.module
  .rule('lint')
    .test(/\.js$/)
    .pre()
    .include
      .add('src')
      .end()
    // 还可以创建具名use (loaders)
    .use('eslint')
      .loader('eslint-loader')
      .options({
        rules: {
          semi: 'off'
        }
      });

config.module
  .rule('compile')
    .test(/\.js$/)
    .include
      .add('src')
      .add('test')
      .end()
    .use('babel')
      .loader('babel-loader')
      .options({
        presets: [
          ['@babel/preset-env', { modules: false }]
        ]
      });

// 也可以创建一个具名的插件!
config
  .plugin('clean')
    .use(CleanPlugin, [['dist'], { root: '/dir' }]);

// 导出这个修改完成的要被webpack使用的配置对象
module.exports = config.toConfig();
```javascript


共享配置也很简单。仅仅导出配置 和 在传递给webpack之前调用 .toConfig() 方法将配置导出给webpack使用。


```javascript
// webpack.core.js
const Config = require('webpack-chain');
const config = new Config();

// 跨目标共享配置
// Make configuration shared across targets
// ...

module.exports = config;

// webpack.dev.js
const config = require('./webpack.core');

// Dev-specific configuration
// 开发具体配置
// ...
module.exports = config.toConfig();

// webpack.prod.js
const config = require('./webpack.core');

// Production-specific configuration
// 生产具体配置
// ...
module.exports = config.toConfig();
```

#### 速记方法

存在许多简写方法，用于 使用与简写方法名称相同的键在 ChainedMap 设置一个值
例如, devServer.hot 是一个速记方法, 因此它可以用作:

```javascript
// 在 ChainedMap 上设置一个值的 速记方法
devServer.hot(true);

// 上述方法等效于:
devServer.set('hot', true);
```

一个速记方法是可链式的，因此调用它将返回 原实例，允许你继续链式使用


配置速记方法

```javascript
config
  .amd(amd)
  .bail(bail)
  .cache(cache)
  .devtool(devtool)
  .context(context)
  .externals(externals)
  .loader(loader)
  .mode(mode)
  .parallelism(parallelism)
  .profile(profile)
  .recordsPath(recordsPath)
  .recordsInputPath(recordsInputPath)
  .recordsOutputPath(recordsOutputPath)
  .stats(stats)
  .target(target)
  .watch(watch)
  .watchOptions(watchOptions)
```














