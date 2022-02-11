---
layout:     post
title:      "lerna相关"
date:       2020-01-17 14:22:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”


## 正文

[https://github.com/lerna/lerna/](https://github.com/lerna/lerna/)


[https://www.jianshu.com/p/8b7e6025354b](https://www.jianshu.com/p/8b7e6025354b)

[https://blog.realign.pro/posts/tech/notes/automation/lerna-gracefully_manage_multiple_npm_packages.html](https://blog.realign.pro/posts/tech/notes/automation/lerna-gracefully_manage_multiple_npm_packages.html)

[现代前端工程化-基于 Monorepo 的 lerna 详解(从原理到实战)]()

[基于pnpm + lerna + typescript的最佳项目实践](https://mp.weixin.qq.com/s/cG67lITpFKM24p6P3JkJzg)

[指令总览 (Commands)](http://www.febeacon.com/lerna-docs-zh-cn/routes/commands/)




A tool for managing JavaScript projects with multiple packages.



一个用于管理多个包的JavaScript项目的工具。


Splitting up large codebases into separate independently versioned packages is extremely useful for code sharing. However, making changes across many repositories is messy and difficult to track, and testing across repositories gets complicated really fast.

将大型代码库分割成独立的版本控制的包对于代码共享非常有用。然而，在多个存储库之间进行更改是很麻烦的，而且很难跟踪，而且在多个存储库之间进行测试会很快变得非常复杂。


To solve these (and many other) problems, some projects will organize their codebases into multi-package repositories. Projects like Babel, React, Angular, Ember, Meteor, Jest, and many others develop all of their packages within a single repository.


为了解决这些(以及许多其他)问题，一些项目将它们的代码库组织到多包存储库中。像Babel、React、Angular、Ember、Meteor、Jest等项目都在一个存储库中开发所有包。




**Lerna is a tool that optimizes the workflow around managing multi-package repositories with git and npm.**


Lerna是一种工具，用于优化使用git和npm管理多软件包存储库的工作流程


----


lerna不负责构建，测试等任务，它提出了一种集中管理package的目录模式，提供了一套自动化管理程序，让开发者不必再深耕到具体的组件里维护内容，在项目根目录就可以全局掌控，基于npm scripts，可以很好地完成组件构建，代码格式化等操作，并在最后一公里，用lerna变更package版本，将其上传至远端。







### lerna init

初始化lerna库


lerna.json


```json
{
  "packages": [
    "packages/*"
  ],
  "ignoreChanges": [
    "**/__fixtures__/**",
    "**/__tests__/**",
    "**/*.md"
  ],
  "version": "0.0.0"
}
```



### lerna bootstrap


Bootstrap the packages in the current Lerna repo. Installing all their dependencies and linking any cross-dependencies.


在当前的Lerna存储库中引导软件包。 安装它们的所有依赖性并链接任何交叉依赖性。


**This command is crucial, as it allows you to use your package names in require() as if the packages were already existing and available in your node_modules folder.**

**这个命令非常重要，因为它允许您在require()中使用包名，就好像包已经存在并在node modules文件夹中可用一样。**



`lerna` 提供了可以**将子项目的依赖包提升到最顶层**的方式 ，我们可以执行 `lerna clean`先删除每个子项目的 `node_modules` , 然后执行命令  `lerna bootstrop --hoist`。

`lerna bootstrop --hoist` 会将 `packages` 目录下的公共模块包抽离到最顶层，但是这种方式会有一个问题，**不同版本号只会保留使用最多的版本**，这种配置不太好，当项目中有些功能需要依赖老版本时，就会出现问题。

#### yarn workspaces

可以解决前面说的当不同的项目依赖不同的版本号问题， `yarn workspaces`会检查每个子项目里面依赖及其版本，如果版本不一致都会保留到自己的 `node_modules` 中，只有依赖版本号一致的时候才会提升到顶层。注意：这种需要在 `lerna.json` 中增加配置。

```js
  "npmClient": "yarn",  // 指定 npmClent 为 yarn
  "useWorkspaces": true // 将 useWorkspaces 设置为 true
```



增加了这个配置后 不再需要 `lerna bootstrap` 来安装依赖了，可以直接使用 `yarn install` 进行依赖的安装。注意：`yarn install` 无论在顶层运行还是在任意一个子项目运行效果都是可以。



### lerna publish

Create a new release of the packages that have been updated. Prompts for a new version and updates all the packages on git and npm.

创建已更新的包的新版本。提示输入新版本并更新git和npm上的所有包。


Options


--npm-tag [tagname] — Publish to npm with the given npm dist-tag (Defaults to latest).




--canary/-c – Create a canary release.

--skip-git – Don't run any git commands.

--force-publish [packages] — Force publish for the specified packages (comma-separated) or all packages using * (skips the git diff check for changed packages).



#### 固定模式


固定模式，通过lerna.json的版本进行版本管理。当你执行lerna publish命令时， 如果距离上次发布只修改了一个模块，将会更新对应模块的版本到新的版本号，然后你可以只发布修改的库。

这种模式也是Babel使用的方式。如果你希望所有的版本一起变更， 可以更新minor版本号，这样会导致所有的模块都更新版本。

```
// lerna.json

{
    "version": "0.0.1"
}
```

固定模式:模块发布新版本时，都会升级到leran.json里编写的version字段


这样纸并不好

#### 独立模式


独立模式，init的时候需要设置选项 --independent.

模块发布新版本时，会逐个询问需要升级的版本号，基准版本为它自身的package.json，这样就避免了上述问题

独立模式允许管理者对每个库单独改变版本号，每次发布的时候，你需要为每个改动的库指定版本号。这种情况下， lerna.json的版本号不会变化了， 默认为independent。


```
// lerna.json

{
    "version": "independent"
}
```


作者：sovran
链接：https://www.jianshu.com/p/8b7e6025354b
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### lerna link

[http://www.febeacon.com/lerna-docs-zh-cn/routes/commands/link.html](http://www.febeacon.com/lerna-docs-zh-cn/routes/commands/link.html)

将当前 Lerna 仓库中相互依赖的所有 Lerna `packages`符号链接在一起。









## 补充





### Monorepo 的收益

https://juejin.cn/post/6944877410827370504?utm_source=gold_browser_extension

首先是**工作流的一致性**，由于所有的项目放在一个仓库当中，复用起来非常方便，如果有依赖的代码变动，那么用到这个依赖的项目当中会立马感知到。并且所有的项目都是使用最新的代码，不会产生其它项目版本更新不及时的情况。

其次是**项目基建成本的降低**，所有项目复用一套标准的工具和规范，无需切换开发环境，如果有新的项目接入，也可以直接复用已有的基建流程，比如 CI 流程、构建和发布流程。这样只需要很少的人来维护所有项目的基建，维护成本也大大减低。

再者，**团队协作也更加容易**，一方面大家都在一个仓库开发，能够方便地共享和复用代码，方便检索项目源码，另一方面，git commit 的历史记录也支持以功能为单位进行提交，之前对于某个功能的提交，需要改好几个仓库，提交多个 commit，现在只需要提交一次，简化了 commit 记录，方便协作。





### node 如何实现软连接

`lerna` 中也是通过这种方式来实现软链的

```js
fs.symlinkSync(target,path,type)
fs.symlinkSync(target,path,type)
target <string> | <Buffer> | <URL>   // 目标文件
path <string> | <Buffer> | <URL>  // 创建软链对应的地址
type <string>
```



### private

在根项目中

package.json 中 private 必须设置为 true



