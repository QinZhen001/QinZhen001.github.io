---
layout:     post
title:      "gulp基础相关"
date:       2017-11-27 22:56:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Gulp
---

> “Yeah It's on. ”


## 正文


[网页链接](http://www.gulpjs.com.cn/docs/getting-started/)

gulp是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，而且大大提高我们的工作效率。

gulp是基于Nodejs的自动任务运行器， 她能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。在实现上，她借鉴了Unix操作系统的管道（pipe）思想，前一级的输出，直接变成后一级的输入，使得在操作上非常简单。通过本文，我们将学习如何使用Gulp来改变开发流程，从而使开发更加快速高效。


gulp 和 grunt 非常类似，但相比于 grunt 的频繁 IO 操作，gulp 的流操作，能更快地更便捷地完成构建工作。

### 起步
1. 全局安装 gulp：
$ npm install --global gulp

2. 作为项目的开发依赖（devDependencies）安装：
$ npm install --save-dev gulp

3. 在项目根目录下创建一个名为 gulpfile.js 的文件：
```
var gulp = require('gulp');
gulp.task('default', function() {
  // 将你的默认的任务代码放在这
});
```

4. 运行 gulp：
$ gulp

### 入门
gulp.src(globs[, options])

输出（Emits）符合所提供的匹配模式（glob）或者匹配模式的数组（array of globs）的文件。 将返回一个 Vinyl files 的 stream 它可以被 piped 到别的插件中。

```javascript
gulp.src('client/templates/*.jade')
  .pipe(jade())
  .pipe(minify())
  .pipe(gulp.dest('build/minified_templates'));
```




## 优化


### 优化gulp watch

不要一次watch太多文件，分开成几个watch任务

```javascript
    /**
     * 构建相关任务
     */
    gulp.task(`${id}-watch`, () => {
      gulp.watch(config.jsFiles, {cwd: srcPath}, gulp.series('js'))
        .on('unlink', (curPath) => {
          let targetPath = path.resolve(distPath, curPath)
          _.delPath(targetPath)
        })

      gulp.watch(config.jsonFiles, {cwd: srcPath}, gulp.series('json'))
        .on('change', (path) => {
          if (/package/.test(path)) {
            install()
          }
        })
        .on('unlink', (curPath) => {
          let targetPath = path.resolve(distPath, curPath)
          _.delPath(targetPath)
        })

      gulp.watch(config.wxmlFiles, {cwd: srcPath}, gulp.series('wxml'))
        .on('unlink', (curPath) => {
          let targetPath = path.resolve(distPath, curPath)
          _.delPath(targetPath)
        })

      gulp.watch(config.lessFiles, {cwd: srcPath}, gulp.series('wxss'))
        .on('unlink', (curPath) => {
          let targetPath = path.resolve(distPath, curPath)
          if (/\.less/.test(targetPath)) {
            targetPath = targetPath.replace('.less', '.wxss')
          }
          _.delPath(targetPath)
        })

      gulp.watch(config.imgFiles, {cwd: srcPath}, gulp.series('img'))
        .on('unlink', (curPath) => {
          let targetPath = path.resolve(distPath, curPath)
          _.delPath(targetPath)
        })
    })
```




### gulp-changed

Only pass through changed files
[https://www.npmjs.com/package/gulp-changed](https://www.npmjs.com/package/gulp-changed)








### gulp-cache



A temp file based caching proxy task for gulp.


[https://www.npmjs.com/package/gulp-cache](h


----------


ttps://www.npmjs.com/package/gulp-cache)




### Vinyl

[https://github.com/gulpjs/vinyl-fs](https://github.com/gulpjs/vinyl-fs)

Vinyl is a very simple metadata object that describes a file. When you think of a file, two attributes come to mind: path and contents. These are the main attributes on a Vinyl object. A file does not necessarily represent something on your computer’s file system. You have files on S3, FTP, Dropbox, Box, CloudThingly.io and other services. Vinyl can be used to describe files from all of these sources.


乙烯基是一个描述文件的非常简单的元数据对象。当您想到一个文件时，会想到两个属性:path和contents。这些是乙烯基物体的主要属性。文件不一定表示计算机文件系统中的内容。你在S3, FTP, Dropbox, Box上都有文件。io和其他服务。乙烯基可以用来描述所有这些来源的文件。


### vinyl-paths

[https://www.npmjs.com/package/vinyl-paths](https://www.npmjs.com/package/vinyl-paths)



Get the file paths in a vinyl stream




Useful when you need to use the file paths from a Gulp pipeline in an async Node.js package.


当您需要在async Node.js包中使用来自Gulp管道的文件路径时，它非常有用。



Simply pass an async function such as del and this package will provide each path in the stream as the first argument.




简单地传递一个异步函数，如del，这个包将提供流中的每个路径作为第一个参数。


```javascript
// gulpfile.js
const gulp = require('gulp');
const stripDebug = require('gulp-strip-debug');
const del = require('del');
const vinylPaths = require('vinyl-paths');
 
// Log file paths in the stream
gulp.task('log', =>
    gulp.src('app/*')
        .pipe(stripDebug())
        .pipe(vinylPaths(async paths => {
            console.log('Paths:', paths);
        })
);
 
// Delete files in the stream
gulp.task('delete', =>
    gulp.src('app/*')
        .pipe(stripDebug())
        .pipe(vinylPaths(del))
);
``` 
 



## 补充


### **crash on error**

最近写babel插件，发现在gulp-babel中很难定位错误。

所以这里引出一个非常重要的知识点，通常情况gulp报错的信息非常模糊，我们很难定位到具体的错误，所以我们要自己监听error事件

**Yep, you need to add an error handler to your gulp task.**


```javascript
var gulp = require("gulp");
var babel = require('gulp-babel');
var sourcemaps = require('gulp-sourcemaps');

gulp.task("default", function(){
  gulp.watch("src/js/**/*.js", ["babel"]);
});

gulp.task("babel", function(){
  gulp.src("src/js/**/*.js")
    .pipe(sourcemaps.init())
    .pipe(babel())
    .on('error', console.error.bind(console));
    .pipe(gulp.dest("www/js/"));
});
```

**非常重要**

**.on('error', console.error.bind(console));**





打印出很清晰的信息

```javascript
{ SyntaxError: F:\前端项目\xhwBase\src\config\baseConfig.js: Unexpected token (15:18)
  13 | switch (baseConfig.env) {
  14 |   case "development":
> 15 |     baseConfig = {...baseConfig, ...devConfig};
     |                   ^
  16 |     break;
  17 |   case "production":
  18 |     baseConfig = {...baseConfig, ...proConfig};
    at Parser.pp$5.raise (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:4454:13)
    at Parser.pp.unexpected (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:1761:8)
    at Parser.pp$3.parseIdentifier (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:4332:10)
    at Parser.pp$3.parsePropertyName (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:4156:96)
    at Parser.pp$3.parseObj (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:4045:12)
    at Parser.pp$3.parseExprAtom (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:3719:19)
    at Parser.pp$3.parseExprSubscripts (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:3494:19)
    at Parser.pp$3.parseMaybeUnary (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:3474:19)
    at Parser.pp$3.parseExprOps (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:3404:19)
    at Parser.pp$3.parseMaybeConditional (F:\前端项目\xhwBase\node_modules\babylon\lib\index.js:3381:19)
  pos: 379,
  loc: Position { line: 15, column: 18 },
  _babel: true,
  codeFrame:
```








### webpack-stream

[https://www.npmjs.com/package/webpack-stream](https://www.npmjs.com/package/webpack-stream)

Run webpack as a stream to conveniently integrate with gulp.

以流的形式运行webpack，方便地与gulp集成。


```javascript
const gulp = require('gulp');
const webpack = require('webpack-stream');

gulp.task('default', function() {
  return gulp.src('src/entry.js')
    .pipe(webpack())
    .pipe(gulp.dest('dist/'));
});
```






