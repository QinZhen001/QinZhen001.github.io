---
layout:     post
title:      "rollup相关"
date:       2022-10-13 18:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”

# 正文

[https://rollupjs.org/guide/en/](https://rollupjs.org/guide/en/)

[中文文档](https://www.rollupjs.com/)





##  -- configPlugin

**Allows specifying Rollup plugins to transpile or otherwise control the parsing of your configuration file.** The main benefit is that it allows you to use non-JavaScript configuration files. For instance the following will allow you to write your configuration in TypeScript, provided you have `@rollup/plugin-typescript` installed:

使用Rollup plugin 处理 配置文件

例子：

```bash
rollup --config rollup.config.ts --configPlugin @rollup/plugin-typescript
```







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

因为 `import` 声明被转换成为 CommonJS 规范的 `require` 语句，但是三方库**并未** 被打包在 bundle 中。为此，我们需要一个插件。

@rollup/plugin-node-resolve

可以让 Rollup 查找到外部模块





# plugin 



## base 



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





## @rollup/pluginutils

[https://www.npmjs.com/package/@rollup/pluginutils](https://www.npmjs.com/package/@rollup/pluginutils)

A set of utility functions commonly used by 🍣 Rollup plugins.

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







# 问题



## [embed all dependencies into one fat target bundle](https://stackoverflow.com/questions/52125190/how-to-embed-all-dependencies-into-one-fat-target-bundle-with-rollup-js)

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





