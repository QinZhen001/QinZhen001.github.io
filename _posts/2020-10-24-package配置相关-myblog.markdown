---

layout:     post
title:      "package配置相关"
date:       2020-10-24 11:33:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>




# 正文

##  [main](https://docs.npmjs.com/files/package.json#main)

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

## **type**

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







