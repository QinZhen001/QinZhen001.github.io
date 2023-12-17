---
layout:     post
title:      "Source Map详解"
date:       2018-03-31 23:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)


### 从源码转换讲起
JavaScript脚本正变得越来越复杂。大部分源码（尤其是各种函数库和框架）都要经过转换，才能投入生产环境。

常见的源码转换，主要是以下三种情况：

1. 压缩，减小体积。比如jQuery 1.9的源码，压缩前是252KB，压缩后是32KB。
2. 多个文件合并，减少HTTP请求数。
3. 其他语言编译成JavaScript。最常见的例子就是CoffeeScript。



这三种情况，都使得实际运行的代码不同于开发代码，除错（debug）变得困难重重。

通常，JavaScript的解释器会告诉你，第几行第几列代码出错。但是，这对于转换后的代码毫无用处。举例来说，jQuery 1.9压缩后只有3行，每行3万个字符，所有内部变量都改了名字。你看着报错信息，感到毫无头绪，根本不知道它所对应的原始位置。


### 什么是Source map

简单说，Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，所对应的转换前的位置。

有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。这无疑给开发者带来了很大方便。


![enter description here][1]


### 如何启用Source map
正如前文所提到的，只要在转换后的代码尾部，加上一行就可以了。
```
    　　//@ sourceMappingURL=/path/to/file.js.map
```

map文件可以放在网络上，也可以放在本地文件系统。


### 如何生成Source map
webpack里
```
// Source maps are resource heavy and can cause out of memory issue for large source files.

const shouldUseSourceMap = process.env.GENERATE_SOURCEMAP !== 'false';
```

```
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false,
        // Disabled because of an issue with Uglify breaking seemingly valid code:
        // https://github.com/facebookincubator/create-react-app/issues/2376
        // Pending further investigation:
        // https://github.com/mishoo/UglifyJS2/issues/2011
        comparisons: false,
      },
      output: {
        comments: false,
        // Turned on because emoji and regex is not minified properly using default
        // https://github.com/facebookincubator/create-react-app/issues/2488
        ascii_only: true,
      },
      sourceMap: shouldUseSourceMap,
    })
```













## 补充



### bootstrap sourceMap 404

[https://stackoverflow.com/questions/21773376/bootstrap-trying-to-load-map-file-how-to-disable-it-do-i-need-to-do-it](https://stackoverflow.com/questions/21773376/bootstrap-trying-to-load-map-file-how-to-disable-it-do-i-need-to-do-it)

bootstrap ui 库会自己去查找sourceMap，导致console控制台会有 404 error

```basic
http://localhost:3001/assets/bootstrap.min.css.mapFailed to load resource: the server responded with a status of 404 (Not Found)

/assets/bootstrap.min.css.map
```

解决：

Delete the line `/*# sourceMappingURL=bootstrap.css.map */` from bootstrap.css







[1]: http://www.ruanyifeng.com/blogimg/asset/201301/bg2013012204.png