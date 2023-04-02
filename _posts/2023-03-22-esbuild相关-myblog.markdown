---
layout:     post
title:      "esbuild相关"
date:       2023-03-22 16:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”





# 基础

[https://esbuild.github.io/](https://esbuild.github.io/)

```tsx
esbuild src/main.js --minify --format=esm --target=es6  --bundle --outfile=build/agora-miniapp-sdk.js
```

minify:  代码压缩

format：模块化 （iife cjs esm）

target： 目标  

bundle：打包

outfile： 输出地址



## Inject 

[https://esbuild.github.io/api/#inject](https://esbuild.github.io/api/#inject)

注入变量 （效果相当于webpack插件之ProvidePlugin）



## tree-shaking

[https://esbuild.github.io/api/#tree-shaking](https://esbuild.github.io/api/#tree-shaking)

默认会带tree-shaking功能



## analyze

[https://esbuild.github.io/api/#analyze](https://esbuild.github.io/api/#analyze)



# 问题





## target es5

[Lowering for ES5?](https://github.com/evanw/esbuild/issues/297)

**结论：esbuild 还不支持将代码转为es5**







If you use a syntax feature that esbuild doesn't yet have support for transforming to your current language target, esbuild will generate an error where the unsupported syntax is used. This is often the case when targeting the es5 language version, for example, since esbuild only supports transforming most newer JavaScript syntax features to es6.

如果您使用的语法特性esbuild还不支持转换到当前语言目标，则esbuild将在使用不支持的语法时生成一个错误。例如，当目标是es5语言版本时，通常会出现这种情况，因为esbuild只支持将大多数较新的JavaScript语法特性转换为es6。

