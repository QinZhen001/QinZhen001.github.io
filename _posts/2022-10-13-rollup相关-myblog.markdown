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







# plugin 





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
