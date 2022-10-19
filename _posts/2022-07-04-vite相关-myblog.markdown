---
layout:     post
title:      "vite相关"
date:       2022-07-04 19:49:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”
>



# 基础

[深入理解Vite核心原理](https://juejin.cn/post/7064853960636989454)

`Vite`其核心原理是利用浏览器现在已经支持`ES6`的`import`,碰见`import`就会发送一个`HTTP`请求去加载文件，`Vite`启动一个 `express` 服务器拦截这些请求，并在后端进行相应的处理将项目中使用的文件通过简单的分解与整合，然后再以`ESM`格式返回返回给浏览器。`Vite`整个过程中没有对文件进行打包编译，做到了真正的按需加载，所以其运行速度比原始的`webpack`开发编译速度快出许多！

#### 

目前所有的打包工具实现热更新的思路都大同小异：主要是通过`WebSocket`创建浏览器和服务器的通信监听文件的改变，当文件被修改时，服务端发送消息通知客户端修改相应的代码，客户端对应不同的文件进行不同的操作的更新。



## alias

```tsx
export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});

```



## base

```tsx
export default defineConfig({
  base: "./",
});
```



## HMR 热更新

目前所有的打包工具实现热更新的思路都大同小异：主要是通过`WebSocket`创建浏览器和服务器的通信监听文件的改变，当文件被修改时，服务端发送消息通知客户端修改相应的代码，客户端对应不同的文件进行不同的操作的更新。



* Webpack:  重新编译，请求变更后模块的代码，客户端重新加载
* Vite:  请求变更的模块，再重新加载



`Vite` 通过 `chokidar` 来监听文件系统的变更，只用对发生变更的模块重新加载， 只需要精确的使相关模块与其临近的 `HMR`边界连接失效即可，这样`HMR` 更新速度就不会因为应用体积的增加而变慢而 `Webpack` 还要经历一次打包构建。所以 `HMR` 场景下，`Vite` 表现也要好于 `Webpack`。



## 预构建

为什么需要预构建？

1. 支持`commonJS`依赖。上面提到`Vite`是基于浏览器原生支持`ESM`的能力实现的，但要求用户的代码模块必须是`ESM`模块，因此必须将`commonJs`的文件提前处理，转化成 `ESM` 模块并缓存入 `node_modules/.vite`
2. 减少模块和请求数量



常用的`lodash`工具库，里面有很多包通过单独的文件相互导入，而 `lodash-es`这种包会有几百个子模块，当代码中出现 `import { debounce } from 'lodash-es'` 会发出几百个 `HTTP` 请求，这些请求会造成网络堵塞，影响页面的加载。

 `Vite` 将有许多内部模块的 `ESM` 依赖关系转换为单个模块，以提高后续页面加载性能。

通过预构建 `lodash-es` 成为一个模块，也就只需要一个 `HTTP` 请求了！





# plugin

[https://cn.vitejs.dev/guide/api-plugin.html](https://cn.vitejs.dev/guide/api-plugin.html)



## basic



### transformIndexHtml

可以选择前置 or 后置 处理

[https://cn.vitejs.dev/guide/api-plugin.html#vite-specific-hooks](https://cn.vitejs.dev/guide/api-plugin.html#vite-specific-hooks)

例子：

```tsx
export default function () {
  return {
    name: 'my-plugin',
    transformIndexHtml: {
      enforce: 'post',   // 'pre'
      transform(html, ctx) {
        console.log('html', html);
        console.log('ctx', ctx);
        return html
      }
    }
  }
}
```











## vite-plugin-inspect

[https://github.com/antfu/vite-plugin-inspect](https://github.com/antfu/vite-plugin-inspect)

Inspect the intermediate state of Vite plugins. Useful for debugging and authoring plugins.



## **esbuildScanPlugin**

[https://www.modb.pro/db/96623](https://www.modb.pro/db/96623)

源码： packages/vite/src/node/optimizer/scan.ts



内置插件

它是从入口文件开始，扫描所有文件，然后找到所有 **bare imports** 即官方称为裸导入的路径，然后记录在 **deps** 对象上。

>  ★ 所谓的 **bare imports** 就是需要从 **node_modules** 中导入的模块。





# 补充





## 多入口

示例：

```tsx
  build: {
          manifest: true,
          outDir,
          rollupOptions: {
            input: Object.fromEntries(entrypoints)
          }
   },
```

entrypoints：

```tsx
 {
  'global.css': '/Users/qz/Downloads/vite-main/playground/backend-integration/frontend/entrypoints/global.css',
  'index.html': '/Users/qz/Downloads/vite-main/playground/backend-integration/frontend/entrypoints/index.html',
  'main.ts': '/Users/qz/Downloads/vite-main/playground/backend-integration/frontend/entrypoints/main.ts',
  'nested/blue.scss': '/Users/qz/Downloads/vite-main/playground/backend-integration/frontend/entrypoints/nested/blue.scss',
  'tailwindcss-colors': 'tailwindcss/colors.js',
  'foo.css': '/Users/qz/Downloads/vite-main/playground/backend-integration/dir/foo.css'
}
```

```tsx
      const entrypoints = glob
        .sync(`${normalizePath(root)}/**/*`, { onlyFiles: true })
        .map((filename) => [path.relative(root, filename), filename])
```







## normalizePath

[https://cn.vitejs.dev/guide/api-plugin.html#path-normalization](https://cn.vitejs.dev/guide/api-plugin.html#path-normalization)

Vite 对路径进行了规范化处理，在解析路径时使用 POSIX 分隔符（ / ），同时保留了 Windows 中的卷名。而另一方面，Rollup 在默认情况下保持解析的路径不变，因此解析的路径在 Windows 中会使用 win32 分隔符（ \ ）。

```tsx
import { normalizePath } from 'vite'

normalizePath('foo\\bar') // 'foo/bar'
normalizePath('foo/bar') // 'foo/bar'
```



## css、less、postcss

[https://juejin.cn/post/7060351422885265415](https://juejin.cn/post/7060351422885265415)

**css:**

`Vite` 默认就支持对 `css` 代码的处理（而不需要再像使用 `Webpack` 时，还需要使用 `css-loader` 和 `style-loader`）。它也是通过在 `<head>` 元素中插入包含了有关样式代码的 `<style>` 元素实现的：



**less:**

预处理器需要依赖 `less` 这个工具，但 `Vite` 没有找到它。确实，我们还没有安装它，所以我们需要先安装 `less` 这个工具（前面在讲 `Webpack` 时说过，在使用 `less-loader` 去加载 `less` 文件时，也需要依赖 `less` 这个工具，所以必须先安装 `less`。只不过在 `Vite` 中，我们不再需要使用 `less-loader` 了，但 `less` 工具还得用）：

```tsx
npm install less -D
```



## 输出 cjs 和 es

https://rollupjs.org/guide/en/#input

```tsx
  build: {
    outDir: './dist',
    manifest: true,
    rollupOptions: {
      output: {
        format: 'cjs',
        freeze: false,
        externalLiveBindings: false
      }
    }
  }
```



## build.lib

[https://cn.vitejs.dev/guide/build.html#library-mode](https://cn.vitejs.dev/guide/build.html#library-mode)

构建为库。`entry` 是必须的因为库不能使用 HTML 作为入口。`name` 则是暴露的全局变量，在 `formats` 包含 `'umd'` 或 `'iife'` 时是必须的。默认 `formats` 是 `['es', 'umd']` 。`fileName` 是输出的包文件名，默认 `fileName` 是 `package.json` 的 `name` 选项，同时，它还可以被定义为参数为 `format` 的函数。





## external

[https://www.rollupjs.com/guide/big-list-of-options](https://www.rollupjs.com/guide/big-list-of-options)

外部依赖

```tsx
  build: {
    rollupOptions: {
      external: ['vue']
    },
    commonjsOptions: {
      esmExternals: ['vue']
    }
  }
```

这样在打包后的js文件 还会保留 import vue   并不会吧vue的源码打包进来



commonjsOptions：

https://cn.vitejs.dev/config/build-options.html#build-commonjsoptions

传递给 [@rollup/plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs) 插件的选项。



esmExternals：

Controls how to render imports from external dependencies. By default, this plugin assumes that all external dependencies are CommonJS. This means they are rendered as default imports to be compatible with e.g. NodeJS where ES modules can only import a default export from a CommonJS dependency:



**If you set `esmExternals` to `true`, this plugins assumes that all external dependencies are ES modules and will adhere to the `requireReturnsDefault` option. If that option is not set, they will be rendered as namespace imports.**



## css.preprocessorOptions

[https://cn.vitejs.dev/config/shared-options.html#css-postcss](https://cn.vitejs.dev/config/shared-options.html#css-postcss)

指定传递给 CSS 预处理器的选项。文件扩展名用作选项的键

```tsx
export default defineConfig({
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `$injectedColor: orange;`
      },
      styl: {
        additionalData: `$injectedColor ?= orange`
      }
    }
  }
})
```





## glob

[https://cn.vitejs.dev/guide/features.html#glob-import](https://cn.vitejs.dev/guide/features.html#glob-import)

Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```
const modules = import.meta.glob('./dir/*.js')
```





## with babel

[https://www.npmjs.com/package/vite-plugin-babel](https://www.npmjs.com/package/vite-plugin-babel)

使用 vite-plugin-babel

Vite team didn't enabled and included Babel by default, because they wanted to keep expirience as fast as possible and esbuild can already do a lot of things, you would probably do with Babel.

```tsx
import { defineConfig } from 'vite';
import babel from 'vite-plugin-babel';

export default defineConfig({
    plugins: [
        // Babel will try to pick up Babel config files (.babelrc or .babelrc.json)
        babel(),
        // ...
    ],

    // ...
})
```







## 打包分析

```tsx
// vite.config.ts

import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [visualizer()],
  build: {
		// ...
  }
})
```

使用rollup 的 plugin

默认会在root目录生成 stats.html  （分析结果页面）





# 问题



## import *.vue file without .vue will not work

[https://github.com/vitejs/vite/issues/178](https://github.com/vitejs/vite/issues/178)

This is by design. We will also stop supporting extension-less Vue imports in vue-cli in the next major.





## vue runtime compilation

当我们使用 template 代替 render 函数的时候： 

或者 在template 中使用 字符串 时：

option but runtime compilation is not supported in this build of Vue. Configure your bundler to alias "vue" to "vue/dist/vue.esm-bundler.js". 

要选择带有编译器的vue版本

```tsx
// vite.config.js
 resolve:{
    alias:{
      'vue': 'vue/dist/vue.esm-bundler.js'
    }
 },
```





## Can css be styleInjected in library mode?

[https://github.com/vitejs/vite/issues/1579](https://github.com/vitejs/vite/issues/1579)

The problem is injecting the style assumes a DOM environment which will make the library SSR-incompatible.

If you only have minimal css, the easiest way is to simply use inline styles.



我们可以使用一个插件

**[vite-plugin-css-injected-by-js](https://github.com/Marco-Prontera/vite-plugin-css-injected-by-js)**

A Vite plugin that takes the CSS and adds it to the page through the JS. For those who want a single JS file.





## Multiple entry points/output in library mode?

[https://github.com/vitejs/vite/discussions/1736](https://github.com/vitejs/vite/discussions/1736)

多入口 库模式打包

vite 的 lib 库模式打包 lib.entry 只支持单入口

解决：

```tsx
import { build } from 'vite'
```

多次调用这个build



## ship in CJS format when my package has pure ESM package dependencies?

[https://lightrun.com/answers/unjs-unbuild-how-to-ship-in-cjs-format-when-my-package-has-pure-esm-package-dependencies](https://lightrun.com/answers/unjs-unbuild-how-to-ship-in-cjs-format-when-my-package-has-pure-esm-package-dependencies)

use `rollup.inlineDependencies` and make your ESM-only dependency a development dependency
