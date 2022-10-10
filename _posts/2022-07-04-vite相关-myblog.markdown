---
layout:     post
title:      "vite相关"
date:       2022-07-04 19:49:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



# 基础



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

