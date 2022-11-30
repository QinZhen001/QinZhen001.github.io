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
