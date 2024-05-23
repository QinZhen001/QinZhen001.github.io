---

layout:     post
title:      "package配置相关"
date:       2020-10-24 11:33:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Package
---

> “Yeah It's on. ”
>




# 正文

[package.json 指南](http://nodejs.cn/learn/the-package-json-guide)





## module

定义 `npm` 包的 ESM 规范的入口文件，browser 环境和 node 环境均可使用





## browser

定义 `npm` 包在 browser 浏览器环境下的入口文件





## scripts



### preinstall

在运行npm install命令时，preinstall脚本会在安装依赖包之前触发。这意味着，在安装依赖包之前，你可以在preinstall脚本中执行任何必要的操作，例如检查环境变量、创建目录、运行编译脚本等。

举个例子：

```ts
"preinstall": "npx only-allow pnpm",
```

[https://pnpm.io/only-allow-pnpm](https://pnpm.io/only-allow-pnpm)





###  prepare

"prepare" script in package.json is triggered when the package is prepared for publishing

It is executed automatically after the package is installed or updated

在安装依赖 or 更新依赖之后执行



## [main](https://docs.npmjs.com/files/package.json#main)

定义了 `npm` 包的入口文件，browser 环境和 node 环境均可使用

The main field is a module ID that is the primary entry point to your program. That is, if your package is named `foo`, and a user installs it, and then does `require("foo")`, then your main module’s exports object will be returned.

This should be a module ID relative to the root of your package folder.

For most modules, it makes the most sense to have a main script and often not much else.



## [bin](https://docs.npmjs.com/files/package.json#bin)

A lot of packages have one or more executable files that they’d like to install into the PATH. npm makes this pretty easy (in fact, it uses this feature to install the “npm” executable.)



To use this, supply a `bin` field in your package.json which is a map of command name to local file name. On install, npm will symlink that file into `prefix/bin` for global installs, or `./node_modules/.bin/` for local installs.

For example, myapp could have this:

```
{ "bin" : { "myapp" : "./cli.js" } }
```

## [files](https://docs.npmjs.com/files/package.json#files)

The optional `files` field is an array of file patterns that describes the entries to be included when your package is installed as a dependency. File patterns follow a similar syntax to `.gitignore`, but reversed: including a file, directory, or glob pattern (`*`, `**/*`, and such) will make it so that file is included in the tarball when it’s packed. Omitting the field will make it default to `["*"]`, which means it will include all files.



可选文件字段是文件模式的数组，描述了将软件包作为依赖项安装时要包括的条目。 文件格式遵循与.gitignore类似的语法，但是相反：包括文件，目录或全局格式（*，** / *等）将使文件格式成为可能，以便在打包时将其包含在压缩包中。 省略该字段将使其默认为[“ *”]，这意味着它将包括所有文件。



Certain files are always included, regardless of settings:

- `package.json`
- `README`
- `CHANGES` / `CHANGELOG` / `HISTORY`
- `LICENSE` / `LICENCE`
- `NOTICE`
- **The file in the “main” field**



Conversely, some files are always ignored:

- `.git`
- `CVS`
- `.svn`
- `.hg`
- `.lock-wscript`
- `.wafpickle-N`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules`
- `config.gypi`
- `*.orig`
- `package-lock.json` (use shrinkwrap instead)



## type

在package.json文件中，type字段用于指定模块的类型。它有以下几种取值：

1. "commonjs"：表示模块采用CommonJS规范，可以被Node.js环境直接加载。
2. "module"：表示模块采用ES模块规范，可以被支持ES模块的环境直接加载，如Webpack、Rollup、Babel等。
3. "unambiguous"：表示模块既可以采用CommonJS规范，也可以采用ES模块规范。在这种情况下，具体的模块类型会根据环境的支持情况来决定。如果环境支持ES模块，则按照ES模块规范加载；否则按照CommonJS规范加载。

默认值为"commonjs"，大多数情况下不需要显式地指定type字段，因为Node.js环境默认支持CommonJS规范。但是，在使用一些前端构建工具如Rollup、Babel构建项目时，可能需要将type字段设置为"module"，以指定模块类型为ES模块规范



## **types**

[https://www.tslang.cn/docs/handbook/declaration-files/publishing.html](https://www.tslang.cn/docs/handbook/declaration-files/publishing.html)

如果你的包有一个主`.js`文件，你还是需要在`package.json`里指定主声明文件。 设置 `types`属性指向捆绑在一起的声明文件。 比如：

```
{
    "name": "awesome",
    "author": "Vandelay Industries",
    "version": "1.0.0",
    "main": "./lib/main.js",
    "types": "./lib/main.d.ts"
}
```

注意`"typings"`与`"types"`具有相同的意义，也可以使用它。



### 发布声明文件

有两种主要方式用来发布typescript声明文件到npm

1. 与你的npm包捆绑在一起，或
2. 发布到npm上的[@types organization](https://www.npmjs.com/~types)。

如果你能控制要使用你发布的声明文件的那个npm包的话，推荐第一种方式。 这样的话，你的声明文件与JavaScript总是在一起传递。



举个依赖的例子：

所有的依赖是由npm管理的。 确保所依赖的声明包都在 `package.json`的`"dependencies"`里指明了 比如，假设我们写了一个包它依赖于Browserify和TypeScript。

```
{
    "name": "browserify-typescript-extension",
    "author": "Vandelay Industries",
    "version": "1.0.0",
    "main": "./lib/main.js",
    "types": "./lib/main.d.ts",
    "dependencies": {
        "browserify": "latest",
        "@types/browserify": "latest",
        "typescript": "next"
    }
}
```

这里，我们的包依赖于`browserify`和`typescript`包。 `browserify`没有把它的声明文件捆绑在它的npm包里，所以我们需要依赖于`@types/browserify`得到它的声明文件。 `typescript`相反，它把声明文件放在了npm包里，因此我们不需要依赖额外的包。

### 注意

*不要*在声明文件里使用`/// <reference path="..." />`。

*应该*使用`/// <reference types="..." />`代替





## resolution

开发过程中经常会遇到下面两个场景：

1. 项目依赖一个不常更新的包，但这个包又依赖另一个包，而这个包由于各种原因需要立即升级，例如紧急安全更新等。
2. 项目的直接依赖定义了过于宽泛的子依赖版本范围，恰巧这其中的某个版本有问题，需要把子依赖限制在某些正常工作的版本范围里。



这个时候就很头疼，因为项目 package.json 不能指定依赖的依赖的版本，它是在依赖的 package.json 中指定的，要么坐等作者升级，要么手动编辑 package-lock.json。如果用 yarn 作为包管理工具的话，就好办了，只需要在 package.json 文件里添加 resolutions 字段，就能覆盖版本定义，例如：

```tsx
{
  "name": "project",
  "version": "1.0.0",
  "dependencies": {
    "left-pad": "1.0.0",
    "c": "file:../c-1",
    "d2": "file:../d2-1"
  },
  "resolutions": {
    "d2/left-pad": "1.1.1",
    "c/**/left-pad": "1.1.2"
  }
}
```





## engines

设置此软件包/应用程序要运行的 Node.js 或其他命令的版本。

示例：

```json
"engines": {
  "node": ">= 6.0.0",
  "npm": ">= 3.0.0",
  "yarn": "^0.13.0"
}
```



## unpkg

[https://segmentfault.com/a/1190000016365409](https://segmentfault.com/a/1190000016365409)

```tsx
# jquery
{
  "unpkg": "dist/jquery.js"
}
```

正常情况下，访问 `jquery` 的发布文件通过 `https://unpkg.com/jquery@3.3.1/dist/jquery.js`，当你使用省略的 url `https://unpkg.com/jquery` 时，便会按照如下的方式获取文件：

```
# [latestVersion] 指最新版本号，pkg 指 package.json

# 定义了 unpkg 属性时
https://unpkg.com/jquery@[latestVersion]/[pkg.unpkg]

# 未定义 unpkg 属性时，将回退到 main 属性
https://unpkg.com/jquery@[latestVersion]/[pkg.main] 

```





### unpkgFiles

unpkg 和 unpkgFiles 都是用于指定在 unpkg（一个可靠的、快速的 CDN）上托管的 npm 包的文件。

- `unpkg` 字段是一个字符串，用于指定入口文件（即主要资源），该文件将在访问 unpkg CDN 时加载。可以是一个`.js`、`.json` 或 `.html` 文件的 URL。当未指定`unpkg` 字段时，默认情况下，unpkg 将加载通过`main` 字段指定的主要文件。
- `unpkgFiles` 字段是一个数组，用于指定应该在访问 unpkg CDN 时加载的其他文件。可以指定多个文件的路径，这些文件将作为附加资源加载到入口文件的环境中。

使用 `unpkg` 字段可以指定整个 npm 包的单个入口文件，而使用 `unpkgFiles` 可以指定额外的附加资源。这样，当用户访问 unpkg CDN 时，可以加载指定的文件，而不仅仅是主要文件。这在加载相对较大的 npm 包或某些特定资源时很有用。

举个例子：

```json
{
  "name": "my-package",
  "version": "1.0.0",
  "main": "dist/main.js",
  "unpkg": "dist/main.js",
  "unpkgFiles": [
    "dist/styles.css",
    "dist/images/logo.png"
  ]
}

```







## style

声明当前模块包含 `style` 部分，并指定入口文件。

The `"style"` attribute in package.json is useful for importing CSS packages. Here's a way to utilize it.

支持的工具：

- [parcelify](https://link.segmentfault.com/?enc=0WKMFN82MOhU1CUInBvoJQ%3D%3D.UsAH7%2BU5%2B7udWs3HY7UPrlvAtUQVzeJU%2BSMUdg8X6TBIU4KVXxmJWBiKcbAIbxTP)
- [npm-less](https://link.segmentfault.com/?enc=0RzrvLLJjRK7bMUysKTTDw%3D%3D.M1kxOXwpvdmcPBPWtgyNkDitpknYhdTsqqF7tfQVx2nT04EjTGgMCqP2g%2BrgSsUl)
- [rework-npm](https://link.segmentfault.com/?enc=3YVnlmke5HFVFg7vJT5XYQ%3D%3D.7cb2O0SwMLh9LjwC6%2Bi35Z%2FTtRy1XqQw7Ijb%2F8gF3ROKwSvyDS8bk1CU5XcRwj%2BR)
- [npm-css](https://link.segmentfault.com/?enc=bEn2U12d5TtLF7S%2BF%2FU82g%3D%3D.EQx4nAZuK1ccc%2FGsPXUGFXO91zfp4twadrtw47XKTY8XfXH1ww7FD%2BEWC6oYq6oa)

详细参考 [Package.json "style" Attribute](https://link.segmentfault.com/?enc=%2FIfDOKxRCWfKW1YmFKsljg%3D%3D.lfNf7g6nQm88xvMHJ2vKkxUVqPUZAIvU71f80JT%2Bv7M%2FDg4yFMDEpZiQwbK9f4xvhr2YHw%2FZ61ruZcsngoFL8A%3D%3D), [istf-spec](https://link.segmentfault.com/?enc=Ii0IZ%2FDCODRN%2FYep5reDiQ%3D%3D.nw0k4UxJrKyg74oORMBk1IlkmW%2B1myf1pL91CBoSPw5%2B9JYTnwm0LeLVn3uuIMuQ).





## workspaces

[https://docs.npmjs.com/cli/v10/using-npm/workspaces](https://docs.npmjs.com/cli/v10/using-npm/workspaces)

**Workspaces** is a generic term that refers to the set of features in the npm cli that provides support for managing multiple packages from your local file system from within a singular top-level, root package.

工作区是一个通用术语，指的是npm cli中的一组特性，这些特性支持从一个顶级根包中管理本地文件系统中的多个包。



## exports

[https://www.cnblogs.com/taohuaya/p/15573719.html](https://www.cnblogs.com/taohuaya/p/15573719.html)

提供了一种方法来为不同的环境和 `JavaScript` 风格公开您的包模块，同时限制对其内部部分的访问。

```
// package.json
{
  "name": "my-lib",
  "type": "module",
  "files": ["dist"],
  "main": "./dist/my-lib.umd.cjs",
  "module": "./dist/my-lib.js",
  "exports": {
    ".": {
      "import": "./dist/my-lib.js",
      "require": "./dist/my-lib.umd.cjs"
    }
  }
}
```





## 版本 version

以当前版本为1.0.0为例子，遵循以下规范：

| 更新情况                         | 位置   | version |
| -------------------------------- | ------ | ------- |
| 修复当前版本的一些bug            | 第三位 | 1.0.1   |
| 新增向后兼容的新特性             | 第二位 | 1.1.0   |
| 进行了破坏性地无法向后兼容的更改 | 第一位 | 2.0.0   |

包的版本信息里会出现`^~`符号

例如

```
"eslint": "^3.19.0",
"css-loader": "~3.19.0",
```

代表安装这个包的版本信息是浮动的，而不是指定死版本。

- ^代表固定主版本号的，其余号浮动，如`^1.3.0`，高于等于1.3.0，1.x.x都符合，但是要低于2.0.0
- ~代表固定次版本号的，修订号浮动，如`~1.3.0`，高于等于1.3.0，1.3.x都符合，但是要低于1.4.0



不同版本的npm 

* npm 2 在安装依赖包时，采用简单的递归安装方法。
* npm 3  扁平结构
* npm 5 package-lock 文件



这只是最简单的例子，实际的工程项目中，依赖树不可避免地会有很多层级，很多依赖包，其中会有很多同名但版本不同的包存在于不同的依赖层级，对这些复杂的情况, npm 3 都会在安装时遍历整个依赖树，计算出最合理的文件夹安装方式，使得所有被重复依赖的包都可以去重安装。

package-lock.json 的作用是**锁定**依赖安装结构，如果查看这个 json 的结构，会发现与 node_modules 目录的文件层级结构是一一对应的。

