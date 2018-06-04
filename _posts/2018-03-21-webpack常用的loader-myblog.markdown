---
layout:     post
title:      "webpack常用的loader"
date:       2018-03-21 16:29:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Webpack
---

> “Yeah It's on. ”


## 正文
[网页链接](http://blog.csdn.net/dongfengkuayue/article/details/52513011)

### style-loader
[https://www.npmjs.com/package/style-loader](https://www.npmjs.com/package/style-loader)


Adds CSS to the DOM by injecting a `<style>` tag
使用`<style>`将css-loader内部样式注入到我们的HTML页面

It's recommended to combine style-loade

| Name      | Type    | Default   | Description |
| --------- | ------- | --------- | ----------- |
| singleton | Boolean | undefined |      Reuses a single `<style></style>` element, instead of adding/removing individual elements for each required module.       |


>Description 也就是说只会插入一个 style标签




#### style-loader/url
css单独打包，并用url引入(link标签)  要配合publicPath

#### style-loader/useable
use()    unuse()


### css-loader
用处：加载.css文件


[https://www.npmjs.com/package/css-loader](https://www.npmjs.com/package/css-loader)

The css-loader interprets @import and url() like import/require() and will resolve them.

Good loaders for requiring your assets are the file-loader and the url-loader which you should specify in your config (see below).


#### 注意点
For URLs that start with a /, the default behavior is to not translate them.

以“/”开始的url，默认行为是不翻译它们

```javascript
url(/image.png) => url(/image.png)
```


### file-loader
[https://www.npmjs.com/package/file-loader](https://www.npmjs.com/package/file-loader)
Instructs webpack to emit the required object as file and to return its public URL

Install
`npm install --save-dev file-loader`

Usage

By default the filename of the resulting file is the MD5 hash of the file's contents with the original extension of the required resource.

import img from './file.png'

webpack.config.js
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {}  
          }
        ]
      }
    ]
  }
}
```
Emits file.png as file in the output directory and returns the public URL
```
"/public/path/0dcbbaa7013869e351f.png"
```

如果我们希望在页面引入图片（包括img的src和background的url）。当我们基于webpack进行开发时，引入图片会遇到一些问题。

其中一个就是引用路径的问题。拿background样式用url引入背景图来说，我们都知道，webpack最终会将各个模块打包成一个文件，因此我们样式中的url路径是相对入口html页面的，而不是相对于原始css文件所在的路径的。这就会导致图片引入失败。这个问题是用file-loader解决的，file-loader可以解析项目中的url引入（不仅限于css），根据我们的配置，将图片拷贝到相应的路径，再根据我们的配置，修改打包后文件引用路径，使之指向正确的文件。

另外，如果图片较多，会发很多http请求，会降低页面性能。这个问题可以通过url-loader解决。url-loader会将引入的图片编码，生成dataURl。相当于把图片数据翻译成一串字符。再把这串字符打包到文件中，最终只需要引入这个文件就能访问图片了。当然，如果图片较大，编码会消耗性能。因此url-loader提供了一个limit参数，小于limit字节的文件会被转为DataURl，大于limit的还会使用file-loader进行copy。

url-loader和file-loader是什么关系呢？**简答地说，url-loader封装了file-loader**。url-loader不依赖于file-loader，即使用url-loader时，只需要安装url-loader即可，不需要安装file-loader，因为url-loader内置了file-loader。通过上面的介绍，我们可以看到，url-loader工作分两种情况：
1. 文件大小小于limit参数，url-loader将会把文件转为DataURL
2. 文件大小大于limit，url-loader会调用file-loader进行处理，参数也会直接传给file-loader。**因此我们只需要安装url-loader即可。**

### url-loader
[https://www.npmjs.com/package/url-loader](https://www.npmjs.com/package/url-loader)

Loads files as `base64` encoded URL

The url-loader works like the file-loader, but can return a DataURL if the file is smaller than a byte limit.


Install
`npm install --save-dev url-loader`

import img from './image.png'

webpack.config.js
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}
```



limit

If the file is greater than the limit (in bytes) the file-loader is used by default and all query parameters are passed to it. You can use other loader using fallback option.

The limit can be specified via loader options and defaults to no limit.

webpack.config.js
```
{
  loader: 'url-loader',
  options: {
    limit: 8192
  }
}
```





### img-loader
[https://www.npmjs.com/package/img-loader](https://www.npmjs.com/package/img-loader)


Image minimizing loader for webpack 2, meant to be used with **url-loader, file-loader, or raw-loader**

Minify PNG, JPEG, GIF and SVG images with imagemin

 Share your code. npm Orgs help your team discover, share, and reuse code. Create a free org »
img-loaderpublic

npm Version Greenkeeper badge Build Status

JS Standard Style MIT License

Image minimizing loader for webpack 2, meant to be used with url-loader, file-loader, or raw-loader

    Minify PNG, JPEG, GIF and SVG images with imagemin

Issues with the minimized output should be reported to imagemin.

Comes with the following optimizers:
* gifsicle — Compress GIF images
* mozjpeg — Compress JPEG images
* optipng — Compress PNG images
* pngquant — Compress PNG images
* svgo — Compress SVG images

Install
`npm install img-loader --save-de

Usage

Documentation: Using loaders
```
module: {
  rules: [
    {
      test: /\.(jpe?g|png|gif|svg)$/i,
      use: [
        'url-loader?limit=10000',
        'img-loader'
      ]
    }
  ]
}
```
The default minification includes: gifsicle, mozjpeg, optipng, & svgo. Each with their default settings.

pngquant can be enabled by configuring it in the options.


Options
Options can also be passed by specifying properties matching each optimizer in your rule options. false or null can be used to disable one of the default optimizers.

[https://github.com/imagemin](https://github.com/imagemin)

```
{
  module: {
    rules: [
      {
        test: /\.(jpe?g|png|gif|svg)$/i,
        use: [
          'url-loader?limit=10000',
          {
            loader: 'img-loader',
            options: {
              enabled: process.env.NODE_ENV === 'production',
              gifsicle: {
                interlaced: false
              },
              mozjpeg: {
                progressive: true,
                arithmetic: false
              },
              optipng: false, // disabled
              pngquant: {
                floyd: 0.5,
                speed: 2
              },
              svgo: {
                plugins: [
                  { removeTitle: true },
                  { convertPathData: false }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```









### vue-loader 

[网页链接](https://vue-loader.vuejs.org/zh-cn/)

vue-loader 是一个 webpack 的 loader，可以将用下面这个格式编写的 Vue 组件转换为 JavaScript 模块：

![enter description here][1]

#### vue-loader 提供的很酷的特性：
* 默认支持 ES2015；
* 允许对 Vue 组件的组成部分使用其它 webpack loader，比如对 `<style>` 使用 SASS 和对 `<template>` 使用 Jade；
* .vue 文件中允许自定义节点，然后使用自定义的 loader 进行处理；
把 `<style>` 和 `<template>` 中的静态资源当作模块来对待，并使用 webpack loader 进行处理；
* 对每个组件模拟出 CSS 作用域；
* 支持开发期组件的热重载。


你可以像下面这样使用 SASS 语法编写样式：
```
<style lang="sass">
  /* write SASS! */
</style>
```

简而言之，编写 Vue.js 应用程序时，组合使用 webpack 和 vue-loader 能带来一个现代，灵活并且非常强大的前端工作流程。








### Webpack的Loader为什么是从右往左写？

比如说下面的Loader写法：
```
style-loader!css-loader!sass-loader
```

**其实为啥是从右往左，而不从左往右，只是Webpack选择了compose方式，而不是pipe的方式而已**



* less-loader 是将less文件编译成css
* sass-loader 是将sass文件编译成css
* css-loader 是处理css文件中的url()等
* style-loader 将css插入到页面的style标签顺便告诉你



### 补充

#### 在output中path和publicPath

* path 是你打包的路径
* **publicpath 是你在html 中引入的路径**



#### browserslist

为了让所有插件共享browserslist

我们在package.json文件中加入
```
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not ie <= 8"
  ]
```
上面是vue默认配置


#### postcss-loader
[网页链接](https://www.npmjs.com/package/postcss-loader)


>由于postcss-loader东西太多这里只是简单介绍，详情见postcss-loader的blog


**Loader for webpack to process CSS with PostCSS**

**Use it after css-loader and style-loader, but before other preprocessor loaders like e.g sass|less|stylus-loader**



postcss.config.js
```
module.exports = {
  parser: 'sugarss',
  plugins: {
    'postcss-import': {},
    'postcss-cssnext': {},
    'cssnano': {}
  }
}
```


**webpack.config.js (recommended)**

When postcss-loader is used standalone (without css-loader) don't use @import in your CSS, since this can lead to quite bloated bundles


```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'postcss-loader'
        ]
      }
    ]
  }
}
```




  [1]: http://blog.evanyou.me/images/vue-component.png
