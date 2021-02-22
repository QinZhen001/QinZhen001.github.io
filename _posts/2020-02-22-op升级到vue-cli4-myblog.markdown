---
layout:     post
title:      "op升级到vue-cli4"
date:       2020-02-22 10:30:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



# 前序

原op系统是基于webpack4自定义了一套配置，编译速度偏慢，并不能跟上webapck5的潮流

遂决定用vue-cli4重新搭建工作流 （vue-cli4内部基于webpack5）

```js
    "webpack": "^5.21.2",
    "webpack-bundle-analyzer": "^2.13.1",
    "webpack-cli": "^4.5.0",
    "webpack-dev-server": "^3.11.2",
```



| 服务                            | 本地开发时间（平均值） | build打包时间（平均值） |
| ------------------------------- | ---------------------- | ----------------------- |
| oto-operation（原基于webpack4） | 62s                    | 44s                     |
| oto-operation（vue-cli4）       | 35s                    | 31s                     |



分析：

基于vue-cli4 第一次本地开发时间 和 build打包时间 都和基于webpack4的原配置所差无几

但是第二次 第三次编译 所时间耗费时间会大幅度降低



判断vue-cli4 内部内置了cache配置

> 下图 是网上找的

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9cd181662a8b4160b5588ca4a38bf3db~tplv-k3u1fbpfcp-watermark.image)

[https://webpack.docschina.org/configuration/other-options/#cache](https://webpack.docschina.org/configuration/other-options/#cache)







# 问题

在此记录一下遇到到问题



## Error: Cannot find module

Error: Cannot find module 'webpack-cli/bin/config-yargs’解决方案

[https://github.com/webpack/webpack-dev-server/issues/2759](https://github.com/webpack/webpack-dev-server/issues/2759)



webpack-dev-server  and webpack serve



Yes webpack-cli v4 comes with out of box support of `@webpack-cli/serve` which means you can use `webpack serve` to invoke the `webpack-dev-server`.









## CopyPlugin的问题





### compilation.getCache

```
 ERROR  TypeError: compilation.getCache is not a function
```



问题：安装的版本过高

https://blog.mounoydev.com/compilation-getcache-is-not-a-function-framwork7/



```
npm uninstall copy-webpack-plugin --save
npm install copy-webpack-plugin@6.2.1 --save
```





## 'isFile' of undefined

最后定位到为题：

```js
 const resolve = (dir) => path.join(__dirname, dir)
 
 
      new CopyPlugin({
        patterns: [
          {
            from: resolve('./static'),
            to: resolve('./dist/static')
          },
          { from: 'robots.txt', to: 'robots.txt' }
        ]
      }),
```

一开始并不存在dist文件夹 导致文件路径的错误





## postcss-loader



### [sourceMap](https://github.com/webpack-contrib/postcss-loader#sourcemap)

https://github.com/webpack-contrib/postcss-loader

sourceMap的默认值是compiler.devtool 所以我们无需手动去设置



## browserslist

你会发现有 `package.json` 文件里的 `browserslist` 字段 (或一个单独的 `.browserslistrc` 文件)，指定了项目的目标浏览器的范围。这个值会被 [@babel/preset-env](https://new.babeljs.io/docs/en/next/babel-preset-env.html) 和 [Autoprefixer](https://github.com/postcss/autoprefixer) 用来确定需要转译的 JavaScript 特性和需要添加的 CSS 浏览器前缀。



Vue CLI 内部使用了 PostCSS。

所以我们无需手动去指定postcss的处理逻辑

vue.config.js  

```js
// css: {
//   loaderOptions: {
//     postcss: {}
//   }
// }
```



## sass



### TypeError: this.getOptions is not a function

https://stackoverflow.com/questions/66082397/typeerror-this-getoptions-is-not-a-function

问题：sass-loader的版本过高



```js
I installed sass-loader@10.1.1 and it worked like a charm again.
```





### SassError: expected selector

```
    ╷
162 │   /deep/ .el-tabs__item.is-disabled{
    │   ^
    ╵
```

[https://github.com/vuejs/vue-loader/issues/913](https://github.com/vuejs/vue-loader/issues/913)

/deep/ selector will be deprecated soon #913





### /deep/ or >>> or ::v-deep

在vue-cli中

[https://vue-loader.vuejs.org/zh/guide/pre-processors.html#sass-vs-scss](https://vue-loader.vuejs.org/zh/guide/pre-processors.html#sass-vs-scss)

```js
// webpack.config.js -> module.rules
{
  test: /\.sass$/,
  use: [
    'vue-style-loader',
    'css-loader',
    {
      loader: 'sass-loader',
      options: {
        indentedSyntax: true,
        // sass-loader version >= 8
        sassOptions: {
          indentedSyntax: true
        }
      }
    }
  ]
}
```

sass 文件 会默认会经过 vue-style-loader 处理

vue-style-loader 估计支持选择器 

```
::v-deep  ::v-global ::v-slotted
```

[https://stackoverflow.com/questions/48032006/how-do-i-use-deep-or-or-v-deep-in-vue-js](https://stackoverflow.com/questions/48032006/how-do-i-use-deep-or-or-v-deep-in-vue-js)



所以 我们将所有 >>> 和 /deep/ 改成 ::v-deep 语法就行了 



还有一个问题 这些新型选择器 在stylelint中会报错 

[https://stylelint.io/user-guide/rules/selector-pseudo-element-no-unknown](https://stylelint.io/user-guide/rules/selector-pseudo-element-no-unknown)

我们查找配置

[https://stackoverflow.com/questions/45492499/how-to-remove-webstorm-sass-lint-error-unknown-pseudo-selector-ng-deep](https://stackoverflow.com/questions/45492499/how-to-remove-webstorm-sass-lint-error-unknown-pseudo-selector-ng-deep)

stylelint忽略v-deep选择器

```js
// .stylelintrc.js


module.exports = {
  extends: ["stylelint-config-standard"],
  rules: {
	// ...
    "selector-pseudo-element-no-unknown": [true, {
      "ignorePseudoElements": ["v-deep"]
    }]
  }
}
```









## eslint 

[https://eslint.org/docs/user-guide/configuring/configuration-files#configuration-file-formats](https://eslint.org/docs/user-guide/configuring/configuration-files#configuration-file-formats)

### root: true

The configuration cascade works based on the location of the file being linted. If there is a `.eslintrc` file in the same directory as the file being linted, then that configuration takes precedence. ESLint then searches up the directory structure, merging any `.eslintrc` files it finds along the way until reaching either a `.eslintrc` file with `root: true` or the root directory.

ESLint搜索目录结构，合并它一路上找到的任何.eslintrc文件，直到到达根目录:true的.eslintrc文件或根目录。





## @vue/cli-plugin-babel

[https://www.npmjs.com/package/@vue/cli-plugin-babel](https://www.npmjs.com/package/@vue/cli-plugin-babel)

Uses Babel 7 + `babel-loader` + [@vue/babel-preset-app](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app) by defaults





## @vue/babel-preset-app

[https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app#usebuiltins](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app#usebuiltins)



这个已经包括了[@babel/preset-env](https://new.babeljs.io/docs/en/next/babel-preset-env.html)

- modules: false
  - auto set to `'commonjs'` in Jest tests
- [`useBuiltIns: 'usage'`](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app#usebuiltins)
- targets
  - by default `@babel/preset-env` will use [`browserslist config sources`](https://github.com/browserslist/browserslist#queries) (browserslist key in package.json file is recommend) unless either the [`targets`](https://babeljs.io/docs/en/babel-preset-env#targets) or [`ignoreBrowserslistConfig`](https://babeljs.io/docs/en/babel-preset-env#ignorebrowserslistconfig) options are set.
  - set to `{ node: 'current' }` when running unit tests in Node.js
- Includes `Promise` polyfill by default so that they are usable even in non-transpiled dependencies (only for environments that need it)



- [Dynamic Import Syntax](https://github.com/tc39/proposal-dynamic-import) 动态import
- [Proposal Class Properties](https://babeljs.io/docs/en/next/babel-plugin-proposal-class-properties.html) 类属性
- [Proposal Decorators (legacy)](https://babeljs.io/docs/en/next/babel-plugin-proposal-decorators.html)   装饰器



Vue JSX support

- [@babel/plugin-syntax-jsx](https://github.com/babel/babel/tree/master/packages/babel-plugin-syntax-jsx)
- [@vue/babel-preset-jsx](https://github.com/vuejs/jsx)

[@babel/plugin-transform-runtime](https://github.com/babel/babel/tree/master/packages/babel-plugin-transform-runtime)

`transform-runtime` avoids inlining helpers in every file. This is enabled for helpers only, since polyfills are handled by `babel-preset-env`.







# 补充 





## yorkie 和 husky 的区别

[husky](https://github.com/typicode/husky)



