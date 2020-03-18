---
layout:     post
title:      "npm几种依赖"
date:       2019-03-06 16:34:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Node
---

> “Yeah It's on. ”


## 正文
[https://zhuanlan.zhihu.com/p/29855253](https://zhuanlan.zhihu.com/p/29855253)

在一个Node.js项目中，package.json几乎是一个必须的文件，它的主要作用就是管理项目中所使用到的外部依赖包，同时它也是npm命令的入口文件。






npm 目前支持以下几类依赖包管理：


* dependencies
* devDependencies
* peerDependencies
* optionalDependencies
* bundledDependencies / bundleDependencies



例子：

```javascript
"devDependencies": {
    "fw2": "^0.3.2",
    "grunt": "^1.0.1",
    "webpack": "^3.6.0"
  },
  "dependencies": {
    "gulp": "^3.9.1",
    "hello-else": "^1.0.0"
  },
  "peerDependencies": { },
  "optionalDependencies": { },
  "bundledDependencies": []  
```




### dependencies


应用依赖，或者叫做业务依赖，这是我们最常用的依赖包管理对象！它用于指定应用依赖的外部包，这些依赖是应用发布后正常执行时所需要的，但不包含测试时或者本地打包时所使用的包。可使用下面的命令来安装


```javascript
npm install packageName --save
```



dependencies是一个简单的JSON对象，包含包名与包版本，其中包版本可以是版本号或者URL地址。比如：


```
{ 
  "dependencies" :{ 
    "foo" : "1.0.0 - 2.9999.9999", // 指定版本范围
    "bar" : ">=1.0.2 <2.1.2", 
    "baz" : ">1.0.2 <=2.3.4", 
    "boo" : "2.0.1", // 指定版本
    "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0", 
    "asd" : "http://asdf.com/asdf.tar.gz", // 指定包地址
    "til" : "~1.2",  // 最近可用版本
    "elf" : "~1.2.3", 
    "elf" : "^1.2.3", // 兼容版本
    "two" : "2.x", // 2.1、2.2、...、2.9皆可用
    "thr" : "*",  // 任意版本
    "thr2": "", // 任意版本
    "lat" : "latest", // 当前最新
    "dyl" : "file:../dyl", // 本地地址
    "xyz" : "git+ssh://git@github.com:npm/npm.git#v1.0.27", // git 地址
    "fir" : "git+ssh://git@github.com:npm/npm#semver:^5.0",
    "wdy" : "git+https://isaacs@github.com/npm/npm.git",
    "xxy" : "git://github.com/npm/npm.git#v1.0.27",
  }
}
```





### devDependencies


开发环境依赖，仅次于dependencies的使用频率！它的对象定义和dependencies一样，只不过它里面的包只用于开发环境，不用于生产环境，这些包通常是单元测试或者打包工具等，例如gulp, grunt, webpack, moca, coffee等，可使用以下命令来安装：

```javascript
npm install packageName --save-dev
```




举个栗子：

```javascript
{ "name": "ethopia-waza",
  "description": "a delightfully fruity coffee varietal",
  "version": "1.2.3",
  "devDependencies": {
    "coffee-script": "~1.6.3"
  },
  "scripts": {
    "prepare": "coffee -o lib/ -c src/waza.coffee"
  },
  "main": "lib/waza.js"
}
```

prepare脚本会在发布前运行，因此使用者在编译项目时不用依赖它。在开发模式下，运行npm install, 同时也会执行prepare脚本，开发时可以很容易的测试。



### **peerDependencies**

同等依赖，或者叫同伴依赖，用于指定当前包（也就是你写的包）兼容的宿主版本。如何理解呢？ 试想一下，我们编写一个gulp的插件，而gulp却有多个主版本，我们只想兼容最新的版本，此时就可以用同等依赖（peerDependencies）来指定：

```javascript
{
  "name": "gulp-my-plugin",
  "version": "0.0.1",
  "peerDependencies": {
    "gulp": "3.x"
  }
}
```

当别人使用我们的插件时，peerDependencies就会告诉明确告诉使用方，你需要安装该插件哪个宿主版本。

通常情况下，我们会在一个项目里使用一个宿主（比如gulp）的很多插件，如果相互之间存在宿主不兼容，在执行npm install时，cli会抛出错误信息来告诉我们，比如


```javascript
npm ERR! peerinvalid The package gulp does not satisfy its siblings' peerDependencies requirements!
npm ERR! peerinvalid Peer gulp-cli-config@0.1.3 wants gulp@~3.1.9
npm ERR! peerinvalid Peer gulp-cli-users@0.1.4 wants gulp@~2.3.0
```


运行命令npm install gulp-my-plugin --save-dev来安装我们插件，我们来看下依赖图谱：

```
├── gulp-my-plugin@0.0.1
└── gulp@3.9.1
```

**重点注意gulp-my-plugin和gulp是在同一层级别的，peerDependencies也常用第三方库的开发**




>npm 1 与 npm 2 会自动安装同等依赖，npm 3 不再自动安装，会产生警告！手动在package.json文件中添加依赖项可以解决。




### optionalDependencies

可选依赖，如果有一些依赖包即使安装失败，项目仍然能够运行或者希望npm继续运行，就可以使用optionalDependencies。另外optionalDependencies会覆盖dependencies中的同名依赖包，所以不要在两个地方都写。

举个栗子，可选依赖包就像程序的插件一样，如果存在就执行存在的逻辑，不存在就执行另一个逻辑。



```javascript
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```



### bundledDependencies / bundleDependencies

打包依赖，bundledDependencies是一个包含依赖包名的数组对象，在发布时会将这个对象中的包打包到最终的发布包里。如：


```javascript
{
  "name": "fe-weekly",
  "description": "ELSE 周刊",
  "version": "1.0.0",
  "main": "index.js",
  "devDependencies": {
    "fw2": "^0.3.2",
    "grunt": "^1.0.1",
    "webpack": "^3.6.0"
  },
  "dependencies": {
    "gulp": "^3.9.1",
    "hello-else": "^1.0.0"
  },
  "bundledDependencies": [
    "fw2",
    "hello-else"
  ]
}
```


执行打包命令npm pack, 在生成的fe-weekly-1.0.0.tgz包中，将包含fw2和hello-else。 但是值得注意的是，这两个包必须先在devDependencies或dependencies声明过，否则打包会报错。







## 补充





### 解决Error: Cannot find module 'node-sass'问题





 https://blog.csdn.net/sliver1836/article/details/95950284 





1.在项目目录cmd下运行 npm install -g cnpm --registry=https://registry.npm.taobao.org

2.下载成功后再运行 cnpm install node-sass





