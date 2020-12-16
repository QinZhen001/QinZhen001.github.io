---
layout:     post
title:      "npm和yarn"
date:       2018-11-16 20:19:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”


## npm

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







## yarn 



### yarn的优势

* **并行执行**

npm会等一个包完全安装完才跳到下一个包，但yarn会并行执行包，因此速度会快很多

* **离线模式**

离线的原理比较简单，安装过的包会被保存进缓存目录，以后安装就直接从缓存中复制过来，这样做的本质还是会提高安装下载的速度，避免不必要的网络请求。

* **版本控制**

npm用下来比较强的一个痛点就是：当包的依赖层次比较深时，版本控制不够精确。会出现相同package.json，但不同人的电脑上安装出不同版本的依赖包，出现类似 “我电脑上是好的，没问题呀”的bug很难查找。你可以使用[npm-shrinkwrap](https://link.jianshu.com/?t=https://docs.npmjs.com/cli/shrinkwrap)来实现版本固化，版本信息会写入npm-shrinkwrap.json文件中，但它毕竟不是npm的标准配置。

而yarn天生就能实现版本固化。会生成一个类似npm-shrinkwrap.json的yarn.lock文件，文件内会描述包自身的版本号，还会锁定所有它依赖的包的版本号：yarn.lock存储这你的每个包的确切依赖版本，能确保从本地开发到生产环境，所有机器上都有精确相同的依赖版本。







### workspace

**workspace是除缓存外yarn区别于npm最大的优势**

 

* 能帮助你更好地管理多个子project的repo，这样你可以在每个子project里使用独立的package.json管理你的依赖，又不用分别进到每一个子project里去yarn
* install/upfrade安装/升级依赖，而是使用一条yarn命令去处理所有依赖就像只有一个package.json一样
* yarn会根据就依赖关系帮助你分析所有子project的共用依赖，保证所有的project公用的依赖只会被下载和安装一次。



使用：

yarn workspace并不需要安装什么其他的包，只需要简单的更改package.json便可以工作。 首先我们需要确定workspace root，一般来说workspace root都会是repo的根目录



yarn workspace目录结构树:

![](https://upload-images.jianshu.io/upload_images/12564775-6eb7783e32e44b13.png?imageMogr2/auto-orient/strip|imageView2/2/w/921/format/webp)

package.json:

```js
{
    //当private为true时workspace才会被启用
    "private": true，
    "workspace": ["workspace-a","workspace-b"]
}

// workspaces属性的值为一个字符串数组，每一项指代一个workspace路径，支持全局匹配，这里的路径指向指的是package.json所在文件夹文件夹名。
```

你会发现整个repo只生成了一份yarn.lock，绝大多数的依赖包都被提升到了根目录下的node_modules之内。各个子project的node_modules里面不会重复存在依赖，只会有针对根目录下依赖的引用



#### workspace的不足

* yarn workspace并没有像lerna那样封装大量的高层API，整个workspace整体上还是依赖于整个yarn命令体系。
* workspace不能嵌套（只能有一个根workspace）
* workspace采用的是向上遍历，所以workspace并不能识别根workspace之外的依赖。

