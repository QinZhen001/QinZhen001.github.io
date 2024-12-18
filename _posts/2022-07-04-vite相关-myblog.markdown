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

Vite是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，它基于 [原生 ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供了 [丰富的内建功能](https://vitejs.cn/guide/features.html)，如速度快到惊人的 [模块热更新（HMR）](https://vitejs.cn/guide/features.html#hot-module-replacement)。
- 一套构建指令，它使用 [Rollup](https://rollupjs.org/) 打包你的代码，并且它是预配置的，可输出用于生产环境的高度优化过的静态资源。

`Vite`其核心原理是利用浏览器现在已经支持`ES6`的`import`,碰见`import`就会发送一个`HTTP`请求去加载文件，`Vite`启动一个 `express` 服务器拦截这些请求，并在后端进行相应的处理将项目中使用的文件通过简单的分解与整合，然后再以`ESM`格式返回返回给浏览器。`Vite`整个过程中没有对文件进行打包编译，做到了真正的按需加载，所以其运行速度比原始的`webpack`开发编译速度快出许多！



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









# plugin

[https://cn.vitejs.dev/guide/api-plugin.html](https://cn.vitejs.dev/guide/api-plugin.html)



## enforce: pre

If you want your plugin to handle resolveId before the internal resolver, try enforce: pre



## transformIndexHtml

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



## configureServer

是用于配置开发服务器的钩子。最常见的用例是在内部 [connect](https://github.com/senchalabs/connect) 应用程序中添加自定义中间件:

```tsx
const myPlugin = () => ({
  name: 'configure-server',
  configureServer(server) {
    server.middlewares.use((req, res, next) => {
      // 自定义请求处理...
    })
  }
})
```



在看一个例子：

@unocss/inspector

[https://github.com/unocss/unocss/blob/main/packages/inspector/node/index.ts](https://github.com/unocss/unocss/blob/main/packages/inspector/node/index.ts)



## load 和  resolveId

这两个都是rollup的钩子

[https://rollupjs.org/guide/en/#load](https://rollupjs.org/guide/en/#load)



load:

**Previous Hook**    =>     [`resolveId`](https://rollupjs.org/guide/en/#resolveid)

**Next Hook:**   =>  [`transform`](https://rollupjs.org/guide/en/#transform)



```tsx
    resolveId(id) {
      if (id === virtualModuleId) {
        return resolvedVirtualModuleId
      }
    },
```

tip: resolveId 默认不需要有返回值

加了 enforce: pre  能看到更多处理







# 常用 plugin 

常用的一些plugin



## vite-plugin-inspect

[https://github.com/antfu/vite-plugin-inspect](https://github.com/antfu/vite-plugin-inspect)

Inspect the intermediate state of Vite plugins. Useful for debugging and authoring plugins.



## **esbuildScanPlugin**

> 内置插件

[https://www.modb.pro/db/96623](https://www.modb.pro/db/96623)

源码： packages/vite/src/node/optimizer/scan.ts



它是从入口文件开始，扫描所有文件，然后找到所有 **bare imports** 即官方称为裸导入的路径，然后记录在 **deps** 对象上。

>  ★ 所谓的 **bare imports** 就是需要从 **node_modules** 中导入的模块。



## vite-plugin-react

[https://www.npmjs.com/package/@vitejs/plugin-react](https://www.npmjs.com/package/@vitejs/plugin-react)

[学不动系列之vite-plugin-react](https://zhuanlan.zhihu.com/p/146273954)

内部 dependencies  => react-refresh

react-refresh主要有两个文件，一个是babel的插件ReactFreshBabelPlugin.js， 一个是ReactFreshRuntime.js。



## vite-dev-rpc

[https://www.npmjs.com/package/vite-dev-rpc](https://www.npmjs.com/package/vite-dev-rpc)

Remote procedure call for client-server communication in Vite plugins.



## vite-plugin-commonjs-externals

Provides commonjs externals support for Vite.

为Vite提供commonjs外部支持。

Prevent bundling of certain *esm* `import`ed packages and instead retrieve these external dependencies at runtime by *commonjs* `require`.

防止绑定某些esm导入的包，而是在运行时通过commonjs require检索这些外部依赖项。



## @vitejs/plugin-legacy

vitejs/plugin-legacy 插件用于在旧版浏览器中支持 ES2015+ 特性



### targets

targets 是一个对象，可指定要支持的最低浏览

```
{
  targets: {
    ie: '11'
  }
}
```

上面的配置使用了 IE11 最小支持版本。当使用该插件对代码进行转换时，Vite 会尝试根据这个目标将代码转换为 ES5 代码。

The query is also [Browserslist compatible](https://github.com/browserslist/browserslist)

也就说不是必要填targets 也可用browserslist 的形式表达



### globalThis is not defined 问题

[https://github.com/vitejs/vite/discussions/7915](https://github.com/vitejs/vite/discussions/7915)

```ts
import legacy from '@vitejs/plugin-legacy';

export default defineConfig({
  // For production build environments only
  build: {
    target: 'es2015',
  },
  plugins: [
    // For production build environments only
    legacy({
      targets: ['chrome >= 64', 'edge >= 79', 'safari >= 11.1', 'firefox >= 67'],
      ignoreBrowserslistConfig: true,
      renderLegacyChunks: false,
      /**
       * Polyfills required by modern browsers
       *
       * Since some low-version modern browsers do not support the new syntax
       * You need to load polyfills corresponding to the syntax to be compatible
       * At build, all required polyfills are packaged according to the target browser version range
       * But when the page is accessed, only the required part is loaded depending on the browser version
       *
       * Two configuration methods:
       *
       * 1. true
       *  - Automatically load all required polyfills based on the target browser version range
       *  - Demerit: will introduce polyfills that are not needed by modern browsers in higher versions,
       *    as well as more aggressive polyfills.
       *
       * 2、string[]
       *  - Add low-version browser polyfills as needed
       *  - Demerit: It needs to be added manually, which is inflexible;
       *    it will be discovered after the production is deployed, resulting in production failure! ! !
       */
      modernPolyfills: ['es/global-this'],
      //  or
      // modernPolyfills: true,
    }),
  ],
});

```



### Polyfills 和 additionalLegacyPolyfills 

* Polyfills：这是一组默认的 polyfills，包括一些常见的 JavaScript API、ES2015+ 特性和浏览器支持的功能。这些 polyfills 能够在旧版浏览器中模拟现代浏览器的功能，使得你的代码可以跨浏览器运行。这些 polyfills 不需要手动配置，在启用插件时会默认加载。
* additionalLegacyPolyfills：这是一个用户定义的选项，用于添加额外的 polyfills。如果你使用了某些 ES2015+ 特性或浏览器 API，而默认的 Polyfills 无法支持这些特性，你可以在这里添加相应的 polyfills。这些 polyfills 需要手动配置，并且会在启用插件时一起加载。



### 结合 regenerator-runtime

regenerator-runtime 是一个将 generator 和 async/await 等语法转换为 ES5 版本的库，用于在不支持这些语法的浏览器中运行。

这两者结合使用，可以让我们在 Vite 中使用 generator 和 async/await 等语法，并且能够在不支持这些语法的浏览器中运行。

```
npm i regenerator-runtime -D
```

在项目的入口文件中引入 regenerator-runtime

```tsx
import 'regenerator-runtime/runtime'
```

```tsx
// 在 vite.config.js 中配置
import legacy from '@vitejs/plugin-legacy'

export default {
  plugins: [
    legacy({
      targets: ['ie >= 11'],
      additionalLegacyPolyfills: ['regenerator-runtime/runtime']
    })
  ]
}
```





# 补充



## webpack 和 vite 的区别

* 本地开发环境webpack也是需要先打包，然后服务器运行的是打包后的文件，码量很大的项目就会有启服务很慢的现象
* **Webpack 的热更新会以当前修改的文件为入口重新 build 打包**，所有涉及到的依赖也都会被重新加载一次。虽然webpack 也采用的是局部热更新并且是有缓存机制的，但是还是需要重新打包所以很大的代码项目是真的有卡顿的现象
* vite 可以做到**快速冷启动**。只启动一台静态页面的服务器，对文件代码不打包，服务器会根据客户端的请求加载不同的模块处理（利用的是浏览器对esMoudle的原生支持）





## 预构建

为什么需要预构建？

1. **支持`commonJS`依赖。**上面提到`Vite`是基于浏览器原生支持`ESM`的能力实现的，但要求用户的代码模块必须是`ESM`模块，因此必须将`commonJs`的文件提前处理，转化成 `ESM` 模块并缓存入 `node_modules/.vite`
2. **减少模块和请求数量**

常用的`lodash`工具库，里面有很多包通过单独的文件相互导入，而 `lodash-es`这种包会有几百个子模块，当代码中出现 `import { debounce } from 'lodash-es'` 会发出几百个 `HTTP` 请求，这些请求会造成网络堵塞，影响页面的加载。

 `Vite` 将有许多内部模块的 `ESM` 依赖关系转换为单个模块，以提高后续页面加载性能。

通过预构建 `lodash-es` 成为一个模块，也就只需要一个 `HTTP` 请求了！



## **快速冷启动**

只启动一台静态页面的服务器，对文件代码不打包，服务器会根据客户端的请求加载不同的模块处理（利用的是浏览器对esMoudle的原生支持），所以节省了webpack 那一套打包转化封装的逻辑。所以大型项目不会再出现热更新卡顿，起服务慢的情况



## 缓存

文件缓存：

Vite 会将预构建的依赖缓存到`node_modules/.vite`。它根据几个源来决定是否需要重新运行预构建步骤:`package.json` 中的 `dependencies` 列表， package-lock等

浏览器缓存：

解析后的依赖请求会以 HTTP 头 `max-age=31536000,immutable` 强缓存，以提高在开发时的页面重载性能。一旦被缓存，这些请求将永远不会再到达开发服务器





## HMR 热更新

[https://zhuanlan.zhihu.com/p/424842555](https://zhuanlan.zhihu.com/p/424842555)

目前所有的打包工具实现热更新的思路都大同小异：主要是通过`WebSocket`创建浏览器和服务器的通信监听文件的改变，当文件被修改时，服务端发送消息通知客户端修改相应的代码，客户端对应不同的文件进行不同的操作的更新。

* Webpack:  重新编译，请求变更后模块的代码，客户端重新加载
* Vite:  请求变更的模块，再重新加载

`Vite` 通过 `chokidar` 来监听文件系统的变更，只用对发生变更的模块重新加载， 只需要精确的使相关模块与其临近的 `HMR`边界连接失效即可，**这样`HMR` 更新速度就不会因为应用体积的增加而变慢而 `Webpack` 还要经历一次打包构建。所以 `HMR` 场景下，`Vite` 表现也要好于 `Webpack`。**





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
