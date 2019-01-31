---
layout:     post
title:      "npm相关命令"
date:       2018-11-16 20:19:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - npm
---

> “Yeah It's on. ”


## 正文

[https://www.cnblogs.com/penghuwan/p/6973702.html#_label4](https://www.cnblogs.com/penghuwan/p/6973702.html#_label4)

### 撤销已经发布的包

npm unpublish 包名 --force

### npm link
[https://docs.npmjs.com/cli/link](https://docs.npmjs.com/cli/link)

First, npm link in a package folder will create a symlink in the global folder `{prefix}/lib/node_modules/<package>`  that links to the package where the npm link command was executed. 



这个命令的作用就是在全局环境下，生成一个符号链接文件，该文件的名字就是package.json文件中指定的模块名。同时我们对此模块的修改会实时反馈在全局目录下。


### 给npm init命令设置自动执行的默认值

我们通常在一个项目中使用npm init来初始化package.json文件。如果你想把经常用到的一些值搞成默认配置，可以使用config set命令。


```javascript
npm config set init.author.name $name
npm config set init.author.email $email
```



## 补充

### NPM发包文件


**NPM默认不会把node_modules发上去**

默认忽略的有


```
.git
CVS
.svn
.hg
.lock-wscript
.wafpickle-N
.*.swp
.DS_Store
._*
npm-debug.log
.npmrc
node_modules
config.gypi
*.orig
package-lock.json (use shrinkwrap instead)

```

想设置发布文件的黑名单 用.npmignore来设置忽略的文件或文件夹。



如果你的项目中没有使用.npmignore文件，那么它默认匹配的是.gitignore中的规则以及一些额外的默认配置。


**可是如果你在项目中添加了.npmignore文件，.gitignore中的规则就会被忽略，而且这时候你还需要维护两份儿规则文件。**