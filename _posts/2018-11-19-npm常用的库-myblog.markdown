---
layout:     post
title:      "npm常用的库"
date:       2018-11-19 19:52:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Package
---

> “Yeah It's on. ”


## 正文


### chalk 
[http://www.8dou5che.com/2017/10/29/chalk](http://www.8dou5che.com/2017/10/29/chalk)


控制台字符样式。

#### 使用
```javascript
const chalk = require('chalk');  
console.log(chalk.blue('Hello world!'));  
```


### commander

[https://github.com/tj/commander.js](https://github.com/tj/commander.js)

[https://blog.csdn.net/qq_40129176/article/details/80816853](https://blog.csdn.net/qq_40129176/article/details/80816853)

用来自行开发出这样一个命令行工具

#### 安装
```javascript
npm install commander --save
```

#### 使用
```javascript
// 引入依赖
var program = require('commander');
 
// 定义版本和参数选项
program
  .version('0.1.0', '-v, --version')
  .option('-i, --init', 'init something')
  .option('-g, --generate', 'generate something')
  .option('-r, --remove', 'remove something');
 
// 必须在.parse()之前，因为node的emit()是即时的
program.on('--help', function(){
 console.log('  Examples:');
  console.log('');
  console.log('    this is an example');
  console.log('');
});
 
program.parse(process.argv);
 
if(program.init) {
  console.log('init something')
}
 
if(program.generate) {
  console.log('generate something')
}
 
if(program.remove) {
  console.log('remove something')
}

```



#### api

**version**

作用：定义命令程序的版本号
用法示例：.version('0.0.1', '-v, --version')


参数解析：
1. 版本号<必须>
2. 自定义标志<可省略>：默认为 -V 和 --version

----------


**option**

作用：用于定义命令选项


用法示例：.option('-n, --name<path>', 'name description', 'default name')

参数解析：
1. 自定义标志<必须>：分为长短标识，中间用逗号、竖线或者空格分割；标志后面可跟必须参数或可选参数，前者用 <> 包含，后者用 [] 包含
2. 选项描述<省略不报错>：在使用 --help 命令时显示标志描述
3.  默认值<可省略>

----------


**command**

作用：添加命令名称

用法示例：.command('rmdir <dir> [otherDirs...]', 'install description', opts)


参数解析：
1.  命令名称<必须>：命令后面可跟用 <> 或 [] 包含的参数；命令的最后一个参数可以是可变的，像实例中那样在数组后面加入 ... 标志；在命令后面传入的参数会被传入到 action 的回调函数以及 program.args 数组中
2. 命令描述<可省略>：如果存在，且没有显示调用action(fn)，就会启动子命令程序，否则会报错
3. 配置选项<可省略>：可配置noHelp、isDefault等

----------


**description**

作用：定义命令的描述


用法示例：.description('rmdir desc')

----------



**action**


作用：定义命令的回调函数


用法示例：.action(fn)

----------

**parse**


作用：用于解析process.argv，设置options以及触发commands

用法示例：.parse(process.argv)

----------



### promisify
**注意: Node.js 8 中在util中已经集成了promisify**


虽然 Promise 已经普及，但是 Node.js 里仍然有大量的依赖回调的异步函数，如果我们每个函数都封装一次，也是麻烦


所以 Node8 就提供了 util.promisify() 这个方法，方便我们快捷的把原来的异步回调方法改成返回 Promise 实例的方法，接下来，想继续用队列，还是 await 就看需要了。


```javascript
const util = require('util');
const fs = require('fs');
 
const stat = util.promisify(fs.stat);
stat('.')
 .then((stats) => {
  // Do something with `stats`
 })
 .catch((error) => {
  // Handle the error.
 });
```


 只要符合 Node.js 的回调风格，所有函数都可以这样转换。也就是说，满足下面两个条件即可。

 1. 后一个参数是函数
 2. 回调函数的参数为 (err, result)，前面是可能的错误，后面是正常的结果



#### 自定义 Promise 化处理函数

那如果函数不符合这个风格，还能用 util.promisify() 么？答案也是肯定的。我们只要给函数增加一个属性，util.promisify.custom ，指定一个函数作为 Promise 化处理函数，即可。请看下面的代码：

```javascript
const util = require('util');
 
function doSomething(foo, callback) {
 // ...
}
 
doSomething[util.promisify.custom] = function(foo) {
 return getPromiseSomehow();
};
 
const promisified = util.promisify(doSomething);
console.log(promisified === doSomething[util.promisify.custom]);
// prints 'true'
```

如此一来，任何时候我们对目标函数 doSomething 进行 Promise 化处理，都会得到之前定义的函数。运行它，就会按照我们设计的特定逻辑返回 Promise 实例。

我们就可以升级以前所有的异步回调函数了。



### ora 
[https://www.npmjs.com/package/ora](https://www.npmjs.com/package/ora)

Elegant terminal spinner


```javascript
const ora = require('ora');
 
const spinner = ora('Loading unicorns').start();
 
setTimeout(() => {
    spinner.color = 'yellow';
    spinner.text = 'Loading rainbows';
}, 1000);
```



### Inquirer
[https://www.npmjs.com/package/inquirer](https://www.npmjs.com/package/inquirer)

A collection of common interactive command line user interfaces.

```javascript
var inquirer = require('inquirer');
inquirer
  .prompt([
    /* Pass your questions in here */
  ])
  .then(answers => {
    // Use user feedback for... whatever!!
  });
```



### download-git-repo
[https://www.npmjs.com/package/download-git-repo](https://www.npmjs.com/package/download-git-repo)

Download and extract a git repository (GitHub, GitLab, Bitbucket) from node.



### n

[https://www.npmjs.com/package/n](https://www.npmjs.com/package/n)

Node.js version management: no subshells, no profile setup, no convoluted API, just **simple**.

node 版本管理工具





### async

[https://www.npmjs.com/package/sync](https://www.npmjs.com/package/sync)

node-sync is a simple library that allows you to call any asynchronous function in synchronous way

处理异步操作

[https://blog.csdn.net/sxyizhiren/article/details/18240435](https://blog.csdn.net/sxyizhiren/article/details/18240435)

[https://blog.csdn.net/momDIY/article/details/73604678](https://blog.csdn.net/momDIY/article/details/73604678)




#### async.eachSeries

async.eachSeries(coll,iteratee,callback)

**简单地说，是用来异步执行一系列的操作,保证每次遍历都执行完毕后再执行下一次的操作，非常有用。**


* 第一个参数可以是一个数组或一个对象（用来遍历）。 
* 第二个参数是每次遍历执行的函数。 
* 第三个参数是回调函数，当遍历中出错会立刻执行回调函数并返回错误信息，若没有发生错误则会等遍历结束后将正确的结果返回。

如果概念不好理解那就请对比下面的两段代码.

第一段代码，foreach里面嵌套save方法，触发异步陷阱，并且mongoose的数据库锁机制（每次操作数据库时会锁定这个库直到本次操作结束）会，出现逻辑错误。

```javascript
books.foreach(book,function(){
book.price = parseFloat(book.listPrice) || book.price || 0;
    book.listPrice = undefined;
    book.save(function (err, book) {
    console.log(book.name);
    });
});
```

第二段代码，利用async.eachSeries巧妙地完成了异步流程控制，也就是每一个save操作完成后再进行下一次遍历。



```javascript
async.eachSeries(books, function (book, callback) {
    book.price = parseFloat(book.listPrice) || book.price || 0;
    book.listPrice = undefined;
    book.save(function (err, book) {
    console.log(book.name);
    callback(err);
    });
}, function (err) {
    if(err){
      config.error(err);
      done(err);
     }else{
      config.info('update price successful');
      done(null);
       }
   });
}
```



### linebyline
[https://www.npmjs.com/package/linebyline](https://www.npmjs.com/package/linebyline)

Read a file line by line.


一行一行读取文件

### browserify
[https://www.npmjs.com/package/browserify](https://www.npmjs.com/package/browserify)




**可以把nodejs的模块编译成浏览器可用的模块**

Browserify是目前最常用的CommonJS格式转换的工具

----------

```javascript
var math = require('math');
math.add(2, 3);
```

第二行math.add(2, 3)，在第一行require('math')之后运行，因此必须等math.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于"假死"状态

　　而browserify这样的一个工具，可以把nodejs的模块编译成浏览器可用的模块，解决上面提到的问题。

  

```javascript
// a.js
var a = 100;
module.exports.a = a;

// b.js
var result = require('./a');
console.log(result.a);
```

index.html直接引用b.js会报错，提示require没有被定义

```javascript
//index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
<script src="b.js"></script>    
</body>
</html>
```

这时，就要使用Browserify了

```
npm install -g browserify
```

使用下面的命令，就能将b.js转为浏览器可用的格式bb.js

 ```
 browserify b.js > bb.js
 ```

查看bb.js，browserify将a.js和b.js这两个文件打包为bb.js，使其在浏览器端可以运行

#### 原理

Browserify到底做了什么？安装一下browser-unpack，就能清楚原理了

```
npm install browser-unpack -g
```

然后，使用下列命令，将前面生成的bb.js解包

```
browser-unpack < bb.js
```


可以看到，browerify将所有模块放入一个数组，id属性是模块的编号，source属性是模块的源码，deps属性是模块的依赖




### cross-env

[https://www.npmjs.com/package/cross-env](https://www.npmjs.com/package/cross-env)


Run scripts that set and use environment variables across platforms

运行跨平台设置和使用环境变量的脚本



>cross-env makes it so you can have a single command without worrying about setting or using the environment variable properly for the platform. Just set it like you would if it's running on a POSIX system, and cross-env will take care of setting it properly.


在这里，我遇到了一个坑

```json
  "scripts": {
    "build:dev": "cross-env NODE_ENV=devlopment && webapck",
    "build:prod": "cross-env NODE_ENV=production && webpack"
  },
```

上面的写法是错误的，正确的写法是：



```json
  "scripts": {
    "build:dev": "cross-env NODE_ENV=devlopment webapck",
    "build:prod": "cross-env NODE_ENV=production webpack"
  },
```

**记得去掉&&**
**记得去掉&&**
**记得去掉&&**




### commitizen

[https://www.npmjs.com/package/commitizen](https://www.npmjs.com/package/commitizen)

When you commit with Commitizen, you'll be prompted to fill out any required commit fields at commit time. No more waiting until later for a git commit hook to run and reject your commit (though that can still be helpful). No more digging through CONTRIBUTING.md to find what the preferred format is. Get instant feedback on your commit message formatting and be prompted for required fields.

当您使用Commitizen提交时，系统将提示您在提交时填写任何必需的提交字段。不再等待git提交钩子运行并拒绝您的提交(尽管这仍然是有帮助的)。不再通过贡献来挖掘。找到md的首选格式是什么。获得关于提交消息格式的即时反馈，并提示输入所需字段。






### conventional-changelog-cli

[https://www.npmjs.com/package/conventional-changelog-cli](https://www.npmjs.com/package/conventional-changelog-cli)



Generate a changelog from git metadata

生成的git元数据的更新日志



Note You don't have to use the angular commit convention. For the best result of the tool to tokenize you commit and produce flexible output, it's recommended to use a commit convention.



#### cz-conventional-changelog


Part of the commitizen family. Prompts for conventional changelog standard.


package.json


Like commitizen, you specify the configuration of cz-conventional-changelog through the package.json's config.commitizen key.

```
{
// ...  default values
    "config": {
        "commitizen": {      
            "path": "./node_modules/cz-conventional-changelog",
            "maxHeaderWidth": 100,
            "maxLineWidth": 100,
            "defaultType": "",
            "defaultScope": "",        
            "defaultSubject": "",
            "defaultBody": "",
            "defaultIssues": ""
        }
    }
// ...    
}
```




### lint-staged


[https://www.npmjs.com/package/lint-staged](https://www.npmjs.com/package/lint-staged)

Run linters against staged git files and don't let 💩 slip into your code base!



**对暂存的git文件运行**，不要让它滑入您的代码库



```
// package.json

{
  "husky": { "hooks": { "pre-commit": "lint-staged" } },
  "lint-staged": { "src/**/*.js?(x)": ["eslint --fix", "git add"] }
}
```



----

在 CI 流程做 Lint 的缺点显而易见：

- Lint 在整个开发工作流中的反馈链条太长，浪费时间、注意力和资源，最致命的；
- CI 流程搭建成本比较高，即使有各种 CI 服务，步骤也还是比较繁琐；

所以最快速的方式是在提交之前就检查，使用 pre-commit 钩子在commit前进行检查，大量的旧代码问题被检查出来，和我这次提交无关，怎么办？**只 lint 提交的，这也是为什么要用 lint-staged 的原因**








### husky


Husky can prevent bad git commit, git push and more 🐶 woof!


Husky可以防止糟糕的git提交，git推送和更多的woof


```
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "npm test",
      "pre-push": "npm test",
      "...": "..."
    }
  }
}
```



### supervisor

[https://www.npmjs.com/package/supervisor](https://www.npmjs.com/package/supervisor)



A little supervisor script for nodejs. It runs your program, and watches for code changes, so you can have hot-code reloading-ish behavior, without worrying about memory leaks and making sure you clean up all the inter-module references, and without a whole new require system.

一个用于nodejs的小管理脚本。它运行您的程序，并监视代码更改，因此您可以有热代码重载行为，而不必担心内存泄漏并确保清除所有模块间的引用，而且不需要全新的require系统。





### es-checker

[https://www.npmjs.com/package/es-checker](https://www.npmjs.com/package/es-checker)




ES-Checker is a feature detection library for ECMAScript in node.js and browser. Please visit demo.

ES-Checker是一个针对node.js和浏览器中的ECMAScript的特性检测库。请访问演示。

```
$ npm install -g es-checker
$ es-checker
```


### debug

[https://www.npmjs.com/package/debug](https://www.npmjs.com/package/debug)

A tiny JavaScript debugging utility modelled after Node.js core's debugging technique. Works in Node.js and web browsers.




一个小型的JavaScript调试工具，模仿Node.js核心的调试技术。工作在Node.js和web浏览器。



```javascript
var debug = require('debug')('http')
  , http = require('http')
  , name = 'My App';
 
// fake app
 
debug('booting %o', name);
 
http.createServer(function(req, res){
  debug(req.method + ' ' + req.url);
  res.end('hello\n');
}).listen(3000, function(){
  debug('listening');
});
 
// fake worker of some kind
 
require('./worker');
```

**一个非常重要的点 要想这个debug生效**

首先执行

```javascript
set DEBUG=http
```





### mime



[网页链接](https://github.com/broofa/node-mime)

Mime types for JavaScript 

A comprehensive, compact MIME type module.

`npm install mime`


#### Quick Start

```
const mime = require('mime');

mime.getType('txt');                    // ⇨ 'text/plain'
mime.getExtension('text/plain');        // ⇨ 'txt'
```



#### MIME类型

MIME(Multipurpose Internet Mail Extensions)多用途互联网邮件扩展类型。是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件打开方式。


每个MIME类型由两部分组成，前面是数据的大类别，例如声音audio、图象image等，后面定义具体的种类。
常见的MIME类型(通用型)：

* 超文本标记语言文本 .html text/html
* xml文档 .xml text/xml
* XHTML文档 .xhtml application/xhtml+xml
* 普通文本 .txt text/plain
* RTF文本 .rtf application/rtf
* PDF文档 .pdf application/pdf
* Microsoft Word文件 .word application/msword


`<link type="text/css">` type="text/css"就是MIME类型。







### configstore 

常用于node脚本保存用户配置信息。



> Easily load and persist config without having to think about where and how

轻松加载和保持配置，而不必考虑在哪里和如何



he config is stored in a JSON file located in `$XDG_CONFIG_HOME` or `~/.config`.
Example: `~/.config/configstore/some-id.json`

*If you need this for Electron, check out [`electron-store`](https://github.com/sindresorhus/electron-store) instead.*
*And check out [`conf`](https://github.com/sindresorhus/conf) for an updated approach to this concept.*





###   puppeteer 

[ https://www.npmjs.com/package/puppeteer ]( https://www.npmjs.com/package/puppeteer )



Puppeteer 是一个Node库， 它提供高级API，通过DevTools Protocol 来控制Chrome 或 Chromium。 Puppeteer 默认运行为headless ，但是可以配置为运行为non-headless 。

### 

- 生成页面截图或PDF
- 抓取SPA 并生成预渲染内容（SSR）
- 自动化表单提交、UI测试，键盘输入 等
- 创建最新的自动化测试环境。 使用最新的JavaScript和浏览器功能直接在最新版本的Chrome中运行测试。
- 捕获站点的时间线跟踪，以帮助诊断性能问题。
- 测试Chrome扩展程序。



```js
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await page.screenshot({path: 'example.png'});

  await browser.close();
})();
```



Puppeteer初始化页面大小为 800*600px，这也是截图的大小 页面大小可以通过 [`Page.setViewport()`](https://pptr.dev/#?product=Puppeteer&version=v1.8.0&show=api-pagesetviewportviewport).定制。







###  json-templater 

[ https://www.npmjs.com/package/json-templater ]( https://www.npmjs.com/package/json-templater )



JSON template(r) is an opinionated simple way to do mustache style template replacements on your js and json objects (and of course strings too)!.



JSON模板(r)是在js和JSON对象(当然还有字符串)上执行mustache样式模板替换的一种自以为是的简单方法!



----



 mustache style  大括号表达式块

 举个栗子：

```js
{{foo}}
```







#### json-templater/string



The string submodule is a very simple mustache like variable replacement with no special features:



string子模块是一个非常简单的mustache，类似于变量替换，没有任何特殊功能





```js
var render = require('json-templater/string');
render('{{xfoo}} {{say.what}}', { xfoo: 'yep', say: { what: 'yep' } });

// yep yep
```





 





### execa

[https://www.npmjs.com/package/execa](https://www.npmjs.com/package/execa)

> Process execution for humans

This package improves [`child_process`](https://nodejs.org/api/child_process.html) methods with:

- Promise interface.
- [Strips the final newline](https://www.npmjs.com/package/execa#stripfinalnewline) from the output so you don't have to do `stdout.trim()`.
- Supports [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) binaries cross-platform.
- [Improved Windows support.](https://github.com/IndigoUnited/node-cross-spawn#why)
- Higher max buffer. 100 MB instead of 200 KB.
- [Executes locally installed binaries by name.](https://www.npmjs.com/package/execa#preferlocal)
- [Cleans up spawned processes when the parent process dies.](https://www.npmjs.com/package/execa#cleanup)
- [Get interleaved output](https://www.npmjs.com/package/execa#all) from `stdout` and `stderr` similar to what is printed on the terminal. [*(Async only)*](https://www.npmjs.com/package/execa#execasyncfile-arguments-options)
- [Can specify file and arguments as a single string without a shell](https://www.npmjs.com/package/execa#execacommandcommand-options)
- More descriptive errors.





### npm-run-all

[https://www.npmjs.com/package/npm-run-all](https://www.npmjs.com/package/npm-run-all)

A CLI tool to run multiple npm-scripts in parallel or sequential.





### wait-on

[https://www.npmjs.com/package/wait-on](https://www.npmjs.com/package/wait-on)

wait for files, ports, sockets, http(s) resources

wait-on is a cross-platform command line utility which will wait for files, ports, sockets, and http(s) resources to become available (or not available using reverse mode). Functionality is also available via a Node.js API. Cross-platform - runs everywhere Node.js runs (linux, unix, mac OS X, windows)





### concurrently

[https://www.npmjs.com/package/concurrently](https://www.npmjs.com/package/concurrently)

Run multiple commands concurrently  (同时)

---

Remember to surround separate commands with quotes:

```bash
"start": "concurrently \"command1 arg\" \"command2 arg\""
```

记住要加引号



### rimraf

[https://www.npmjs.com/package/rimraf](https://www.npmjs.com/package/rimraf)

```
The UNIX command rm -rf for node.
```





### cp-cli

[https://www.npmjs.com/package/cp-cli](https://www.npmjs.com/package/cp-cli)

The [UNIX command](https://en.wikipedia.org/wiki/Cp_(Unix)) `cp` for Node.js which is useful for cross platform support.



### rvm

>  **非npm库**

[https://rvm.io/rubies/default](https://rvm.io/rubies/default)

Ruby 的版本管理库

```bash
$ rvm --default use 2.1.1

$ ruby -v

ruby 2.1.1p76 (2014-02-24 revision 45161) [x86_64-darwin12.0]
```

tip： 一定要 --default

```
// 查看当前ruby版本
rvm current
```





### only-allow

[https://www.npmjs.com/package/only-allow](https://www.npmjs.com/package/only-allow)

> Force a specific package manager to be used on a project

If you want to force [pnpm](https://pnpm.js.org/), add:

```tsx
{
  "scripts": {
    "preinstall": "npx only-allow pnpm"
  }
}
```



### just-scripts

[https://microsoft.github.io/just/scripts/](https://microsoft.github.io/just/scripts/)

Unlike other build libraries, Just strives to be useful from the beginning. You can choose to write your own tasks that call other Node.js tools like TypeScript, jest, and webpack. However, Just includes some script functions to get you up and running immediately.





### magic-string

[https://www.npmjs.com/package/magic-string](https://www.npmjs.com/package/magic-string)

It's a small, fast utility for manipulating strings and generating sourcemaps.

字符串替换重写



### es-module-lexer

[https://www.npmjs.com/package/es-module-lexer](https://www.npmjs.com/package/es-module-lexer)

A JS module syntax lexer used in [es-module-shims](https://github.com/guybedford/es-module-shims).

Outputs the list of exports and locations of import specifiers, including dynamic import and import meta handling.

虽然js代码的词法分析通常都使用babel, acorn等工具，但是针对ESM文件来说，使用es-module-lexer库在性能上能够有很大的提升，其压缩后的体积只有4kb，而且根据官方给出的例子720kb的Angular1库经过acorn解析要超过100ms，而使用es-module-lexer库只需要5ms, 在性能上提升了将近20倍。







### connect

[https://www.npmjs.com/package/connect](https://www.npmjs.com/package/connect)

Connect is an extensible HTTP server framework for [node](http://nodejs.org/) using "plugins" known as *middleware*.





### chokidar

[https://www.npmjs.com/package/chokidar](https://www.npmjs.com/package/chokidar)

Minimal and efficient cross-platform file watching library





### unbuild

[https://www.npmjs.com/package/unbuild](https://www.npmjs.com/package/unbuild)

> A unified javascript build system.   (js 打包工具)

Robust [rollup](https://rollupjs.org/) based bundler that supports typescript and generates commonjs and module formats + type declarations.

* 支持 typescript 及其生成 

* 支持 cjs 和 mjs

```
    "dev": "unbuild --stub",
    "build": "unbuild",
```

Stub `dist` once using [jiti](https://github.com/unjs/jiti) and you can try and link your project without needing to watch and rebuild during development.



例子：

```tsx
// build.config.ts
import { defineBuildConfig } from 'unbuild'

export default defineBuildConfig({
  entries: ['src/index'],
  externals: ['vite', 'vue/compiler-sfc', '@vue/compiler-sfc'],
  clean: true,
  declaration: true,
  rollup: {
    emitCJS: true,
    inlineDependencies: true
  }
})
```





### esno

[https://www.npmjs.com/package/esno](https://www.npmjs.com/package/esno)

Node.js runtime enhanced with esbuild for loading TypeScript & ESM

可以直接在node环境中运行.ts

```tsx
npm i -g esno

esno index.ts
```







### fs-extra

[https://www.npmjs.com/package/fs-extra](https://www.npmjs.com/package/fs-extra)

`fs-extra` adds file system methods that aren't included in the native `fs` module and adds promise support to the `fs` methods. It also uses [`graceful-fs`](https://github.com/isaacs/node-graceful-fs) to prevent `EMFILE` errors. It should be a drop in replacement for `fs`.





### url

[https://www.npmjs.com/package/url](https://www.npmjs.com/package/url)

This module has utilities for URL resolution and parsing meant to have feature parity with node.js core [url](http://nodejs.org/api/url.html) module.



### ufo

[https://www.npmjs.com/package/ufo](https://www.npmjs.com/package/ufo)

Url utils for umans   (url相关的工具)



### jiti

[https://github.com/unjs/jiti](https://github.com/unjs/jiti)

> Runtime typescript and ESM support for Node.js (CommonJS)

mjs

```tsx
// dist/index.mjs
import jiti from 'jiti'
export default jiti(null, { interopDefault: true })('/Users/antfu/unbuild-test/src/index')
```

cjs

```tsx
// dist/index.cjs
module.exports = require('jiti')(null, { interopDefault: true })('/Users/antfu/unbuild-test/src/index')
```





### sirv

[https://www.npmjs.com/package/sirv](https://www.npmjs.com/package/sirv)

> The optimized and lightweight middleware for serving requests to static assets

为静态资产提供请求的优化的轻量级中间件





### bumpp

[https://www.npmjs.com/package/bumpp](https://www.npmjs.com/package/bumpp)

用于自动版本号更新

git commit  和 git tag 和 git push

```tsx
"release": "bumpp --commit --push --tag && npm publish"
```



### protobufjs

[https://www.npmjs.com/package/protobufjs](https://www.npmjs.com/package/protobufjs)

[http://febeacon.com/protobuf_docs_zh_cn/routes/command_line.html](http://febeacon.com/protobuf_docs_zh_cn/routes/command_line.html)









# 其他



## mpv

[https://command-not-found.com/mpv](https://command-not-found.com/mpv)

[https://mpv.io/](https://mpv.io/)

mpv is a free (as in freedom) media player for the command line. It supports a wide variety of media file formats, audio and video codecs, and subtitle types.
