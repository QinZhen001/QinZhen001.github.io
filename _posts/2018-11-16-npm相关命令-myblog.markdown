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




### npm-access


[https://www.npmjs.cn/cli/access/](https://www.npmjs.cn/cli/access/s)

Set access level on published packages

设置已发布包的访问级别


For all of the subcommands, npm access will perform actions on the packages in the current working directory if no package name is passed to the subcommand.


对于所有子命令，如果没有将包名传递给子命令，npm访问将对当前工作目录中的包执行操作。



public / restricted: Set a package to be either publicly accessible or restricted.


默认为:restricted

还可以在package.json里面配置


```

...
"publishConfig": {
		"access": "public"
	}
```



## 补充

### NPM发包


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




#### 以@开头的包

[https://www.npmjs.cn/misc/scope/](https://www.npmjs.cn/misc/scope/)



Publishing public scoped packages to the primary npm registry


To publish a public scoped package, you must specify --access public with the initial publication. This will publish the package and set access to public as if you had run npm access public after publishing.


要发布公共作用域的包，必须指定——使用初始发布访问public。这将发布包并设置对public的访问，就像您在发布后运行npm访问public一样。



#### require子路径

 [ https://runkit.com/qinzhen001/5eb232f5cf366c00149f7b33 ]( https://runkit.com/qinzhen001/5eb232f5cf366c00149f7b33 )



```js
var test = require("qz-asdasd/ccc")
test()
```



有的时候，我们想访问npm包子路径下的文件，那该如何设计这种包的结构尼？



例子：

![](https://s1.ax1x.com/2020/05/06/YAGGX8.png)



> 子文件夹 ccc中的index.js默认导出一个文件





### npm彩蛋

圣诞节彩蛋
```javascript
npm xmas
```





