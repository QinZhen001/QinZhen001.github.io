---
layout:     post
title:      "npm常用的库"
date:       2018-11-19 19:52:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - npm
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



### conventional-changelog-cli

[https://www.npmjs.com/package/conventional-changelog-cli](https://www.npmjs.com/package/conventional-changelog-cli)



Generate a changelog from git metadata

生成的git元数据的更新日志



Note You don't have to use the angular commit convention. For the best result of the tool to tokenize you commit and produce flexible output, it's recommended to use a commit convention.


















