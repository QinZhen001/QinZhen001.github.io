---
layout:     post
title:      "vue-cli相关"
date:       2019-10-09 19:56:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”


# 正文


在这里记录一下，使用vue-cli3过程中一些重要的知识点


## 环境变量和模式

[https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F)

具体的一个例子：

[https://github.com/wsdo/vuejs-best-practice](https://github.com/wsdo/vuejs-best-practice)



## 处理静态资源


[https://cli.vuejs.org/zh/guide/html-and-static-assets.html#%E5%A4%84%E7%90%86%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90](https://cli.vuejs.org/zh/guide/html-and-static-assets.html#%E5%A4%84%E7%90%86%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90)





* 在 JavaScript 被导入或在 template/CSS 中通过**相对路径**被引用。**这类引用会被 webpack 处理。**
* 放置在 public 目录下或通过**绝对路径**被引用。**这类资源将会直接被拷贝，而不会经过 webpack 的处理。**



### 从相对路径导入

当你在 JavaScript、CSS 或 `*.vue` 文件中使用相对路径 (必须以 . 开头) 引用一个静态资源时，该资源将会被包含进入 webpack 的依赖图中。在其编译过程中，所有诸如 `<img src="...">`、background: url(...) 和 CSS @import 的资源 URL **都会被解析为一个模块依赖**


例如，url(./image.png) 会被翻译为 require('./image.png')，而：




```
<img src="./image.png">
```

将会被编译到：



```
h('img', { attrs: { src: require('./image.png') }})
```



**在其内部，我们通过 file-loader 用版本哈希值和正确的公共基础路径来决定最终的文件路径，再用 url-loader 将小于 4kb 的资源内联，以减少 HTTP 请求的数量。**


### URL 转换规则

* 如果 URL 是一个绝对路径 (例如 /images/foo.png)，它将会被保留不变。
* 如果 URL 以 . 开头，它会作为一个相对模块请求被解释且基于你的文件系统中的目录结构进行解析。
* 如果 URL 以 ~ 开头，其后的任何内容都会作为一个模块请求被解析。这意味着你甚至可以引用 Node 模块中的资源：

```
<img src="~some-npm-package/foo.png">
```

* 如果 URL 以 @ 开头，它也会作为一个模块请求被解析。它的用处在于 Vue CLI 默认会设置一个指向 `<projectRoot>/src` 的别名 @。(仅作用于模版中)







## serve本地预览

[ https://cli.vuejs.org/zh/guide/deployment.html ]( https://cli.vuejs.org/zh/guide/deployment.html )



如果你用 Vue CLI 处理静态资源并和后端框架一起作为部署的一部分，那么你需要的仅仅是确保 Vue CLI 生成的构建文件在正确的位置，并遵循后端框架的发布方式即可。

如果你独立于后端部署前端应用——也就是说后端暴露一个前端可访问的 API，然后前端实际上是纯静态应用。那么你可以将 `dist` 目录里构建的内容部署到任何静态文件服务器中，但要确保正确的 [publicPath](https://cli.vuejs.org/zh/config/#publicpath)。



----



 `dist` 目录需要启动一个 HTTP 服务器来访问 (除非你已经将 `publicPath` 配置为了一个相对的值)，所以以 `file://` 协议直接打开 `dist/index.html` 是不会工作的。在本地预览生产环境构建最简单的方式就是使用一个 Node.js 静态文件服务器，例如 [serve](https://github.com/zeit/serve)： 



```
npm install -g serve
# -s 参数的意思是将其架设在 Single-Page Application 模式下
# 这个模式会处理即将提到的路由问题
serve -s dist
```









### devServer.proxy

Type: string | Object


如果你的前端应用和后端 API 服务器没有运行在同一个主机上，你需要在开发环境下将 API 请求代理到 API 服务器。这个问题可以通过 vue.config.js 中的 devServer.proxy 选项来配置。

devServer.proxy 可以是一个指向开发环境 API 服务器的字符串：

```javascript
module.exports = {
  devServer: {
    proxy: 'http://localhost:4000'
  }
}
```

如果你想要更多的代理控制行为，也可以使用一个 path: options 成对的对象。完整的选项可以查阅 http-proxy-middleware 。


```javascript
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  }
}
```


### **parallel**

* Type: boolean
* Default: require('os').cpus().length > 1


是否为 Babel 或 TypeScript 使用 thread-loader。该选项在系统的 CPU 有多于一个内核时自动启用，仅作用于生产构建。


>这个相当于默认开启了





## css


### **loaderOptions**

* Type: Object
* Default: {}


向 CSS 相关的 loader 传递选项。例如：

```javascript
module.exports = {
  css: {
    loaderOptions: {
      css: {
        // 这里的选项会传递给 css-loader
      },
      postcss: {
        // 这里的选项会传递给 postcss-loader
      }
    }
  }
}
```

支持的 loader 有：

* css-loader
* postcss-loader
* sass-loader
* less-loader
* stylus-loader


另外，也可以使用 scss 选项，针对 scss 语法进行单独配置（区别于 sass 语法）。



>相比于使用 chainWebpack 手动指定 loader 更推荐上面这样做，因为这些选项需要应用在使用了相应 loader 的多个地方。



#### scss配置全局变量

[https://github.com/webpack-contrib/sass-loader#additionaldata](https://github.com/webpack-contrib/sass-loader#additionaldata)

我们利用`additionalData`



Type: `String|Function` Default: `undefined`

Prepends `Sass`/`SCSS` code before the actual entry file. In this case, the `sass-loader` will not override the `data` option but just **prepend** the entry's content.

This is especially useful when some of your Sass variables depend on the environment:

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.s[ac]ss$/i,
        use: [
          "style-loader",
          "css-loader",
          {
            loader: "sass-loader",
            options: {
              additionalData: "$env: " + process.env.NODE_ENV + ";",
            },
          },
        ],
      },
    ],
  },
};
```

#### **scss配置全局变量报错**

[https://blog.csdn.net/qq_43471802/article/details/108979593](https://blog.csdn.net/qq_43471802/article/details/108979593)

**由于sass-loader版本不同，loaderOptions 中 additionalData的键名也不同**



```js
sass-loader v8-，这个选项名是 "data"
sass-loader v8 中，这个选项名是 "prependData"
sass-loader v10+，这个选项名是 "additionalData"
```

---



variable.scss 和 mixins.scss 会优先于 global.css 加载，并且可以不通过 import 的方式在项目中任何位置使用这些变量和 mixins。



```js
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      sass: {
        prependData: `
        @import '@/styles/variable.scss';
        @import '@/styles/mixins.scss';
        `,
      },
    },
  },
}
```





## Generator 类

[ https://meixg.cn/2019/03/03/vue-cli-create/ ]( https://meixg.cn/2019/03/03/vue-cli-create/ )



[ https://cli.vuejs.org/dev-guide/plugin-dev.html#generator ]( https://cli.vuejs.org/dev-guide/plugin-dev.html#generator )



 Generator 类是 Vue-cli 提供的与插件之间交流机制，编写插件时可利用这个机制，进行文件、package.json 依赖的修改 



它是怎么工作的呢？Generator 类初始化时，会给每个插件初始化一个 GeneratorAPI 类，作为插件与 cli 之间的接口。

```js
// apply generators from plugins
plugins.forEach(({ id, apply, options }) => {
  const api = new GeneratorAPI(id, this, options, rootOptions)
  apply(api, options, rootOptions, invoking)
})
```










## webpack 相关

[https://cli.vuejs.org/zh/guide/webpack.html#webpack-%E7%9B%B8%E5%85%B3](https://cli.vuejs.org/zh/guide/webpack.html#webpack-%E7%9B%B8%E5%85%B3)



### configureWebpack

Type: Object | Function

如果这个值是一个对象，则会通过 webpack-merge 合并到最终的配置中。


如果这个值是一个函数，则会接收被解析的配置作为参数。该函数及可以修改配置并不返回任何东西，也可以返回一个被克隆或合并过的配置版本。



### chainWebpack

Type: Function


是一个函数，会接收一个基于 webpack-chain 的 ChainableConfig 实例。允许对内部的 webpack 配置进行更细粒度的修改。






### 简单的配置方式


调整 webpack 配置最简单的方式就是在 vue.config.js 中的 configureWebpack 选项提供一个对象：

```javascript
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```


该对象将会被 webpack-merge 合并入最终的 webpack 配置。



>有些 webpack 选项是基于 vue.config.js 中的值设置的，所以不能直接修改。例如你应该修改 vue.config.js 中的 outputDir 选项而不是修改 output.path；你应该修改 vue.config.js 中的 publicPath 选项而不是修改 output.publicPath。这样做是因为 vue.config.js 中的值会被用在配置里的多个地方，以确保所有的部分都能正常工作在一起。





如果你需要基于环境有条件地配置行为，或者想要直接修改配置，那就换成一个函数 (该函数会在环境变量被设置之后懒执行)。该方法的第一个参数会收到已经解析好的配置。在函数内，你可以直接修改配置，或者返回一个将会被合并的对象：

```javascript
// vue.config.js
module.exports = {
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
  }
}
```


### 链式操作 (高级)

Vue CLI 内部的 webpack 配置是通过 webpack-chain 维护的。这个库提供了一个 webpack 原始配置的上层抽象，使其可以定义具名的 loader 规则和具名插件，并有机会在后期进入这些规则并对它们的选项进行修改。


它允许我们更细粒度的控制其内部配置。接下来有一些常见的在 vue.config.js 中的 chainWebpack 修改的例子。


**修改 Loader 选项**


```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
        .loader('vue-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
  }
}
```


>对于 CSS 相关 loader 来说，我们推荐使用 css.loaderOptions 而不是直接链式指定 loader。这是因为每种 CSS 文件类型都有多个规则，而 css.loaderOptions 可以确保你通过一个地方影响所有的规则。


**添加一个新的 Loader**



```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    // GraphQL Loader
    config.module
      .rule('graphql')
      .test(/\.graphql$/)
      .use('graphql-tag/loader')
        .loader('graphql-tag/loader')
        .end()
  }
}
```





### 审查项目的 webpack 配置

因为 @vue/cli-service 对 webpack 配置进行了抽象，所以理解配置中包含的东西会比较困难，尤其是当你打算自行对其调整的时候。



vue-cli-service 暴露了 inspect 命令用于审查解析好的 webpack 配置。那个全局的 vue 可执行程序同样提供了 inspect 命令，这个命令只是简单的把 vue-cli-service inspect 代理到了你的项目中。


该命令会将解析出来的 webpack 配置、包括链式访问规则和插件的提示打印到 stdout。



你可以将其输出重定向到一个文件以便进行查阅：


```
vue inspect > output.js
```



注意它输出的并不是一个有效的 webpack 配置文件，而是一个用于审查的被序列化的格式。















## Babel


Babel 可以通过 babel.config.js 进行配置。



>Vue CLI 使用了 Babel 7 中的新配置格式 babel.config.js。和 .babelrc 或 package.json 中的 babel 字段不同，这个配置文件不会使用基于文件位置的方案，而是会一致地运用到项目根目录以下的所有文件，包括 node_modules 内部的依赖。我们推荐在 Vue CLI 项目中始终使用 babel.config.js 取代其它格式。


所有的 Vue CLI 应用都使用 @vue/babel-preset-app，它包含了 babel-preset-env、JSX 支持以及为最小化包体积优化过的配置。通过它的文档可以查阅到更多细节和 preset 选项。



### @vue/babel-preset-app

[https://www.npmjs.com/package/@vue/babel-preset-app](https://www.npmjs.com/package/@vue/babel-preset-app)



This is the default Babel preset used in all Vue CLI projects. **Note: this preset is meant to be used exclusively in projects created via Vue CLI and does not consider external use cases.**



```javascript
module.exports = {
  presets: [
    '@vue/app'
  ]
}
```


观察package.json并未发现@vue/babel-preset-app



深入观察

在@vue/cli-plugin-babel中找到了@vue/babel-preset-app

```javascript
    "@vue/cli-plugin-babel": {
      "version": "3.11.0",
      "resolved": "https://registry.npmjs.org/@vue/cli-plugin-babel/-/cli-plugin-babel-3.11.0.tgz",
      "integrity": "sha512-RSq9goefilvUMYxbeQS8cZxt6zZnvBIb1xK4NWX0NBtqKzJmIjtJzDAQvKgjnZ/USRu6p6X2IRj4Wum8UCUqow==",
      "dev": true,
      "requires": {
        "@babel/core": "^7.0.0",
        "@vue/babel-preset-app": "^3.11.0",
        "@vue/cli-shared-utils": "^3.11.0",
        "babel-loader": "^8.0.5",
        "webpack": "^4.0.0"
      }
    },
```


polyfills



Default: ['es6.array.iterator', 'es6.promise', 'es6.object.assign', 'es7.promise.finally']











# vue-cli4

[https://github.com/vuejs/vue-cli](https://github.com/vuejs/vue-cli)

[https://cli.vuejs.org/migrating-from-v3/#the-global-vue-cli](https://cli.vuejs.org/migrating-from-v3/#the-global-vue-cli)

- In v3, only `vuex` or `vue-router` will be added to the project;
- In v4, there will also be `@vue/cli-plugin-vuex` or `@vue/cli-plugin-router` installed.







## 支持的webpack版本

[https://github.com/vuejs/vue-cli/blob/dev/packages/%40vue/cli-service/package.json](https://github.com/vuejs/vue-cli/blob/dev/packages/%40vue/cli-service/package.json)

```
    "webpack": "^5.4.0",
    "webpack-bundle-analyzer": "^4.1.0",
    "webpack-chain": "^6.5.1",
    "webpack-dev-server": "^3.11.0",
    "webpack-merge": "^5.5.0",
    "webpack-virtual-modules": "^0.4.1"
```

我们去查看源码，可以发现vue-cli4的webpack版本是5以上的版本












