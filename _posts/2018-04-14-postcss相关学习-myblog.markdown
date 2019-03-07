---
layout:     post
title:      "PostCSS相关学习"
date:       2018-04-14 15:45:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - PostCSS
---

> “Yeah It's on. ”


## 正文
[网页链接](https://segmentfault.com/a/1190000011595620#articleHeader5)

**一个后处理器**

PostCSS 可以直观的理解为：它就是一个平台

PostCSS 它需要一个插件系统才能够发挥作用。我们可以通过“插件”来传递AST，然后再把AST转换成一个串，最后再输出到目标文件中去

[https://segmentfault.com/a/1190000003909268](https://segmentfault.com/a/1190000003909268)


postcss处理css的方式，主要区分三部分：

* parser过程：将css字符串解析成可供我们操作的JavaScript对象
* processor过程：我们应用postcss插件、或是自定义插件，都是在这个过程中，根据postcss提供的API，对parser生成的js对象做相应调整；
* stringfier过程：将我们处理后的js对象，再转换回为css字符串



[https://segmentfault.com/a/1190000015155639](https://segmentfault.com/a/1190000015155639)



### postcss-loader
[https://www.npmjs.com/package/postcss-loader](https://www.npmjs.com/package/postcss-loader)

Loader for webpack to process CSS with PostCSS

**Use it after css-loader and style-loader, but before other preprocessor loaders like e.g sass|less|stylus-loader, if you use any.**

```
webpack.config.js

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'postcss-loader' ]
      }
    ]
  }
}
```


>When postcss-loader is used standalone (without css-loader) don't use @import in your CSS, since this can lead to quite bloated bundles






### .postcssrc.js 
在vue项目中.postcssrc.js 的默认配置
```
// https://github.com/michael-ciniawsky/postcss-load-config

module.exports = {
  "plugins": {
    "postcss-import": {},
    "postcss-url": {},
    // to edit target browsers: use "browserslist" field in package.json
    "autoprefixer": {}
  }
}
```







### postcss-import
[https://www.npmjs.com/package/postcss-import](https://www.npmjs.com/package/postcss-import)



PostCSS plugin to transform @import rules by inlining content.


postcss-import主要功有是解决@import引入路径问题。使用这个插件，可以让你很轻易的使用本地文件、node_modules或者web_modules的文件。这个插件配合postcss-url让你引入文件变得更轻松。

Notes:
**This plugin should probably be used as the first plugin of your list. This way, other plugins will work on the AST as if there were only a single file to process, and will probably work as you can expect.**




### postcss-url
**PostCSS plugin** to rebase, inline or copy on url().




### autoprefixer
**PostCSS plugin** to parse CSS and add vendor prefixes to CSS rules using values from Can I Use. It is recommended by Google and used in Twitter and Taobao.


Because Autoprefixer is a postprocessor for CSS(后处理器), you can also use it with preprocessors such as Sass, Stylus or LESS.

#### Browsers
Autoprefixer uses Browserslist, so you can specify the browsers you want to target in your project by queries like last 2 versions or > 5%.

The best way to provide browsers is .browserslistrc config or package.json with browserslist key. Put it in your project root.

We recommend to avoid Autoprefixer option and use .browserslistrc config or package.json. In this case browsers will be shared with other tools like babel-preset-env or Stylelint.




### postcss-cssnext
postcss-cssnext其实就是cssnext。该插件可以让我们使用CSS未来的特性，其会对这些特性做相关的兼容性处理。其包含的特性主要有：

![enter description here][1]








### cssnano
[https://www.npmjs.com/package/cssnano](https://www.npmjs.com/package/cssnano)
A modular minifier, built on top of the PostCSS ecosystem.


cssnano主要用来压缩和清理CSS代码。在Webpack中，cssnano和css-loader捆绑在一起，所以不需要自己加载它。不过你也可以使用postcss-loader显式的使用cssnano


```
@svg square {
	@rect {
		fill: var(--color, black);
		width: 100%;
		height: 100%;
	}
}

#example {
	background: white svg(square param(--color #00b1ff));
}
```
转为
```
#example {
	background: white url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Crect fill='%2300b1ff' width='100%25' height='100%25'/%3E%3C/svg%3E");
}
```


### postcss-mpvue-wxss
专门为 wxss 格式化处理的的一个 postcss 插件，特别是在做 css 转 wxss 的时候好用到爆。

实现的功能
* 清理 wxss 不支持的选择器。
* 清理 wxss 不支持的注释。
* 转换 rem 单位到 rpx。
* 转换 Web 的标签选择器到小程序的 class 选择器。
* style scoped（postcss插件部分）。



### postcss-px2rem
This is a postcss plugin of px2rem.



 **"autoprefixer": {remUnit: 75}**




  [1]: https://www.w3cplus.com/sites/default/files/blogs/2018/1801/vw-layout-4.png