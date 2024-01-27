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





# rollup

[https://rollupjs.org/guide/en/](https://rollupjs.org/guide/en/)

[中文文档](https://www.rollupjs.com/)







## 基础 



### -- configPlugin

**Allows specifying Rollup plugins to transpile or otherwise control the parsing of your configuration file.** The main benefit is that it allows you to use non-JavaScript configuration files. For instance the following will allow you to write your configuration in TypeScript, provided you have `@rollup/plugin-typescript` installed:

使用Rollup plugin 处理 配置文件

例子：

```bash
rollup --config rollup.config.ts --configPlugin @rollup/plugin-typescript
```





 

## repl

[https://rollupjs.org/repl](https://rollupjs.org/repl)

> REPL 是一种编程环境，全称为"Read-Eval-Print Loop"，意为"读取-求值-输出循环"。它是一种交互式的编程环境，可以逐行读取代码，对代码进行求值或执行，并将结果输出到屏幕上。



## multiple input

多入口打包

```tsx
// rollup.config.ts
import { defineConfig } from 'rollup'

const config1 = defineConfig({})
// ...

export default (commandLineArgs: any): RollupOptions[] => {
  const isDev = commandLineArgs.watch

  return defineConfig([
    config1,
    config2,
    config3
  ])
}
```





## support *CommonJS*

Rollup supports *ES modules* out of the box. However, to support *CommonJS*, the following plugins are required:

- [@rollup/plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs)
- [@rollup/plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve)

`@rollup/plugin-commonjs` 应该在其他插件 *之前* 使用——这是为了防止其他插件进行的更改破坏了 CommonJS 检测。





## 集成三方库

[https://www.rollupjs.com/guide/tools](https://www.rollupjs.com/guide/tools)

有时候，您的项目可能需要依赖于一些 NPM 包。与 webpack 和 Browserify 等其他打包器不同，Rollup 并不能“开箱即用”地处理 NPM 包的依赖关系——我们需要添加一些配置。

当出现 Unresolved dependencies

因为 `import` 声明被转换成为 CommonJS 规范的 `require` 语句，但是三方库**并未** 被打包在 bundle 中。为此，我们需要一个插件@rollup/plugin-node-resolve,可以让 Rollup 查找到外部模块



## plugin 



### rollup-plugin-esbuild

[https://www.npmjs.com/package/rollup-plugin-esbuild](https://www.npmjs.com/package/rollup-plugin-esbuild)

[esbuild](https://github.com/evanw/esbuild) is by far one of the fastest TS/ESNext to ES6 compilers and minifier, this plugin replaces `rollup-plugin-typescript2`, `@rollup/plugin-typescript` and `rollup-plugin-terser` for you.



### rollup-plugin-visualizer

[https://www.npmjs.com/package/rollup-plugin-visualizer](https://www.npmjs.com/package/rollup-plugin-visualizer)

分析打包体积



### @rollup/plugin-commonjs

[https://www.npmjs.com/package/@rollup/plugin-commonjs](https://www.npmjs.com/package/@rollup/plugin-commonjs)

🍣 A Rollup plugin to convert CommonJS modules to ES6, so they can be included in a Rollup bundle



### @rollup/plugin-node-resolve

🍣 A Rollup plugin which locates modules using the [Node resolution algorithm](https://nodejs.org/api/modules.html#modules_all_together), for using third party modules in `node_modules`

可以让 Rollup 找到外部模块

加载node_modules里面的三方库

```ts
import { nodeResolve } from "@rollup/plugin-node-resolve";

 nodeResolve({
    browser: true,  // browser module resolutions 解析浏览器的包
    mainFields: ['browser', 'module', 'main'],
    preferBuiltins: false,
    extensions: [".mjs", ".js", ".json", ".ts"]
 }),
```

### @rollup/pluginutils

[https://www.npmjs.com/package/@rollup/pluginutils](https://www.npmjs.com/package/@rollup/pluginutils)

A set of utility functions commonly used by 🍣 Rollup plugins.

为写 rollup plugin 提供的 utils 工具函数

```tsx
export {
  addExtension,   // 增加后缀
  attachScopes,   // 依附作用域 （ast处理）
  createFilter, // 处理 include 和 exclude  (用于 transform 钩子)
  dataToEsm,  // data =>  esmodule
  extractAssignedNames,  // 获取ast的node的names
  makeLegalIdentifier, // 变成下滑线命名
  normalizePath  // 规范化路径
};
```

### Inter-plugin communication

[https://rollupjs.org/guide/en/#inter-plugin-communication](https://rollupjs.org/guide/en/#inter-plugin-communication)

```tsx
function parentPlugin() {
  return {
    name: 'parent',
    api: {
      //...methods and properties exposed for other plugins
      doSomething(...args) {
        // do something interesting
      }
    }
    // ...plugin hooks
  };
}

function dependentPlugin() {
  let parentApi;
  return {
    name: 'dependent',
    buildStart({ plugins }) {
      const parentName = 'parent';
      const parentPlugin = plugins.find(plugin => plugin.name === parentName);
      if (!parentPlugin) {
        // or handle this silently if it is optional
        throw new Error(`This plugin depends on the "${parentName}" plugin.`);
      }
      // now you can access the API methods in subsequent hooks
      parentApi = parentPlugin.api;
    },
    transform(code, id) {
      if (thereIsAReasonToDoSomething(id)) {
        parentApi.doSomething(id);
      }
    }
  };
}
```

通过提供api 一个插件可以调用另外一个插件提供的方法。

## 问题

### embed all dependencies into one fat target bundle

[embed all dependencies into one fat target bundle](https://stackoverflow.com/questions/52125190/how-to-embed-all-dependencies-into-one-fat-target-bundle-with-rollup-js)

Use [rollup-plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve) (and [rollup-plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs) if you have CommonJS dependencies).





# unbuild

[https://github.com/unjs/unbuild](https://github.com/unjs/unbuild)

> A unified javascript build system

Robust [rollup](https://rollupjs.org/) based bundler that supports typescript and generates commonjs and module formats + type declarations.



```tsx
// build.config.ts
import { defineBuildConfig } from 'unbuild'

export default defineBuildConfig({
  entries: [
    './src/index'
  ],
  clean: true,
  declaration: true, // generate .d.ts files
  rollup: {
    emitCJS: true,  // generate .cjs files
    inlineDependencies: true,
  },
})
```











# esbuild



## 基础

[https://esbuild.github.io/](https://esbuild.github.io/)

```tsx
esbuild src/main.js --minify --format=esm --target=es6  --bundle --outfile=build/agora-miniapp-sdk.js
```

minify:  代码压缩

format：模块化 （iife cjs esm）

target： 目标  

bundle：打包

outfile： 输出地址

### serve

[https://esbuild.github.io/api/#serve](https://esbuild.github.io/api/#serve)



### Inject

[https://esbuild.github.io/api/#inject](https://esbuild.github.io/api/#inject)

注入变量 （效果相当于webpack插件之ProvidePlugin）



### tree-shaking

[https://esbuild.github.io/api/#tree-shaking](https://esbuild.github.io/api/#tree-shaking)

默认会带tree-shaking功能



### analyze

[https://esbuild.github.io/api/#analyze](https://esbuild.github.io/api/#analyze)



## 问题





### target es5

[Lowering for ES5?](https://github.com/evanw/esbuild/issues/297)

**结论：esbuild 还不支持将代码转为es5**

If you use a syntax feature that esbuild doesn't yet have support for transforming to your current language target, esbuild will generate an error where the unsupported syntax is used. This is often the case when targeting the es5 language version, for example, since esbuild only supports transforming most newer JavaScript syntax features to es6.

如果您使用的语法特性esbuild还不支持转换到当前语言目标，则esbuild将在使用不支持的语法时生成一个错误。例如，当目标是es5语言版本时，通常会出现这种情况，因为esbuild只支持将大多数较新的JavaScript语法特性转换为es6。





# swc

[https://swc.nodejs.cn/](https://swc.nodejs.cn/)

SWC 是一个基于 Rust 的可扩展平台，适用于下一代快速开发工具。 它被 Next.js、Parcel 和 Deno 等工具以及 Vercel、字节跳动、腾讯、Shopify 等公司使用。





# tsup

