---
layout:     post
title:      "SSR相关"
date:       2018-04-10 23:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 后端
---

> “Yeah It's on. ”

[https://github.com/yacan8/blog/issues/30](https://github.com/yacan8/blog/issues/30)

与传统 SPA (单页应用程序 (Single-Page Application)) 相比，服务器端渲染 (SSR) 的优势主要在于：

- 更好的 SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。请注意，截至目前，Google 和 Bing 可以很好对同步 JavaScript 应用程序进行索引。在这里，同步是关键。如果你的应用程序初始展示 loading 菊花图，然后通过 Ajax 获取内容，抓取工具并不会等待异步完成后再行抓取页面内容。也就是说，如果 SEO 对你的站点至关重要，而你的页面又是异步获取内容，则你可能需要服务器端渲染(SSR)解决此问题。
- 更快的内容到达时间 (time-to-content)，特别是对于缓慢的网络情况或运行缓慢的设备。无需等待所有的 JavaScript 都完成下载并执行，才显示服务器渲染的标记，所以你的用户将会更快速地看到完整渲染的页面。通常可以产生更好的用户体验，并且对于那些「内容到达时间(time-to-content) 与转化率直接相关」的应用程序而言，服务器端渲染 (SSR) 至关重要。

##  React

### Suspense in ssr

[New Suspense SSR Architecture in React 18](https://github.com/reactwg/react-18/discussions/37)

支持在ssr下的组件的懒加载

### next.js

### React.lazy

- `React.lazy`：官方、简洁，但 **早期不支持 SSR**（React 18 才通过 Streaming SSR 支持）
- `react-loadable`：**SSR 友好**，有 `preload`、`Map`、超时处理等更丰富的 API，但作者已在 README 中标注推荐用 `React.lazy`，项目更新不活跃

React.lazy 为什么在 React 18 之后支持 SSR？

React 17 及之前：为什么不支持？

```jsx
const LazyComp = React.lazy(() => import('./Comp'));
```

`React.lazy` 依赖 **Suspense** 机制：当组件未加载完成时，`lazy` 会 **throw 一个 Promise**，最近的 `<Suspense>` 捕获并渲染 `fallback`，等 Promise resolve 后再重新渲染真实组件。

React 17 SSR 的限制：

React 17 的 SSR API 是 `renderToString` / `renderToNodeStream`，它们是 **同步字符串渲染**：

1. **不能"暂停"再"继续"**：遇到 throw 的 Promise，服务端没法等它 resolve 再接着往下渲染

2. **Suspense 在服务端等同于直接渲染 fallback**：遇到 `lazy` 只能要么报错，要么永远停在 fallback

3. **结果**：`React.lazy` + `Suspense` 在 SSR 下直接 throw：

   > `ReactDOMServer does not yet support Suspense.`

这就是为什么大家转而用 `react-loadable` —— 它通过 **同步加载已注册模块 + webpack 插件收集 chunk** 的方式绕过了 Suspense 机制。

----

React 18 如何支持的？

核心：Streaming SSR + Selective Hydration

React 18 在 SSR 架构上做了两件大事，彻底解决了这个问题。

1. 服务端：`renderToPipeableStream` / `renderToReadableStream`（Streaming SSR）

替代 `renderToString`，核心变化：

| 特性     | React 17 `renderToString` | React 18 `renderToPipeableStream`              |
| -------- | ------------------------- | ---------------------------------------------- |
| 输出形式 | 一次性字符串              | **分块流式输出** (chunked transfer)            |
| 异步处理 | 不支持                    | **能"等待"Suspense 内部的 Promise**            |
| Suspense | 直接渲染 fallback 或报错  | **先发 fallback，组件 ready 后再补发真实内容** |

Streaming 的工作流程（关键）

当 SSR 渲染过程中遇到 `React.lazy` 或任何 Suspense 边界：

```jsx
时间轴：

T0   Server 开始渲染
     ├── 外层 HTML <html><body><App>...
     ├── 遇到 <Suspense fallback={<Spinner/>}>
     │       <LazyComp />   ← throw Promise
     │   </Suspense>
     │
T1   Server 先把 fallback 写进流：
     flush → <div id="B:0"><Spinner/></div>   （带占位 ID）
     ├── 继续渲染 Suspense 之外的内容，也一起 flush
     │
T2   LazyComp 的 Promise resolve（chunk 加载 / 数据就绪）
     Server 把真实 HTML 渲染好，再 flush：
     <template id="S:0">...真实内容...</template>
     <script>
       $RC("B:0","S:0")   // React 注入的运行时函数：
                          // 把 template 里的内容替换到 B:0 占位
     </script>
     │
T3   Client 收到流，原生 HTML 解析 → 占位替换 → Hydration

```

关键点：

- **`$RC` 是 React 运行时注入的极小 JS 函数**，在流中就绪后直接在 DOM 层替换，不用等整个页面下载完
- **无需框架层缓冲整个 HTML**，首字节（TTFB）显著变快

3. 客户端：`hydrateRoot` + Selective Hydration

与之配套的客户端 API 从 `ReactDOM.hydrate` 换成 `ReactDOM.hydrateRoot`，带来：

- **Selective Hydration（选择性水合）**：Suspense 边界内的组件可以 **独立水合**，不必等整棵树 JS 全部到齐
- **优先级调度**：用户交互的那一块 Suspense 会被优先水合（基于 Concurrent Rendering）
- 于是 `React.lazy` 分出来的 chunk 可以在客户端按需加载 + 独立水合，不阻塞其他部分

#### Concurrent Rendering

React 18 底层把渲染改成 **可中断、可恢复** 的 Concurrent Rendering：

- Fiber 树可以"挂起"在某个节点（遇到未 resolve 的 Promise）
- 服务端 renderer 也复用了这套机制：渲染是**分片进行**的，遇到 Suspense 就 park 住这棵子树，继续渲染其他部分
- 等 Promise resolve，再 resume 这棵子树，flush 到流里

没有 Concurrent Rendering 就没有"挂起/恢复"能力，也就没法实现 Streaming SSR 里的 Suspense。

#### 完整demo

```ts
// 用法几乎不变
const LazyComp = React.lazy(() => import('./Comp'));

function Page() {
  return (
    <Suspense fallback={<Spinner />}>
      <LazyComp />
    </Suspense>
  );
}

// 服务端
import { renderToPipeableStream } from 'react-dom/server';
const { pipe } = renderToPipeableStream(<Page />, {
  bootstrapScripts: ['/main.js'],
  onShellReady() { pipe(res); },  // 外壳 ready 就先吐给浏览器
});

// 客户端
import { hydrateRoot } from 'react-dom/client';
hydrateRoot(document, <Page />);

```

完整流程：

1. Server 渲染 → 遇到 `LazyComp` 的 import Promise → 写入 `<Spinner>` 占位 + 继续渲染
2. Server 端 `import()` resolve → 渲染真实内容 → flush `<template>` + `$RC` 脚本替换占位
3. Client 同时在下载 `Comp` 的 chunk → 选择性水合这块 Suspense

### react-loadable

核心一句话：**`react-loadable` 把"异步加载"限定在进程启动或客户端下载阶段，渲染阶段永远是同步的**。这与 `renderToString` 的同步模型完美契合。而 `React.lazy` 则把异步性下沉到了渲染阶段，必须要有一个支持异步/暂停恢复的 renderer（Streaming SSR）才能在服务端工作。

| 维度         | react-loadable                                         | React 18 `React.lazy` + Suspense          |
| ------------ | ------------------------------------------------------ | ----------------------------------------- |
| SSR 支持     | 需 `preloadAll` + webpack 插件手动收集 chunk 注入 HTML | **原生支持**（Streaming）                 |
| 首字节 TTFB  | 等所有 chunk 预加载完才能渲染                          | **先吐 shell，流式补内容**                |
| 代码复杂度   | 需要 babel 插件 + 手动 `preloadAll`                    | 纯 React API，零配置                      |
| 水合粒度     | 整页一次性                                             | **Selective Hydration**，按 Suspense 粒度 |
| 维护状态     | 官方停滞，README 自荐用 lazy                           | React 官方持续演进                        |
| Next.js 对应 | `next/dynamic`（底层也在迁移到 lazy）                  | App Router 原生用 lazy/Suspense           |

`react-loadable` 是不是强依赖 webpack？

**结论先说：运行时（render 层面）不依赖 webpack；但要完整发挥"SSR 友好 + 精确 chunk 注入"能力，强依赖 webpack 生态**。

完整 SSR 方案：**强依赖 webpack**

这是 `react-loadable` 真正和 webpack 深度耦合的地方。完整 SSR 方案的三件套：

1. `react-loadable/babel`（Babel 插件）

```js
// 原始代码
Loadable({ loader: () => import('./Foo') })

// 插件转换后
Loadable({
  loader: () => import('./Foo'),
  webpack: () => [require.resolveWeak('./Foo')],  // ← webpack API
  modules: ['./Foo'],
})
```

**关键依赖**：`require.resolveWeak` 是 **webpack 独有**的 API，含义是"给我这个模块的 id，但不要把它当依赖打进来"。

- Rollup、Vite、esbuild 都没有等价物
- 一旦走这条路径，**产物必须由 webpack 打包**

2. `react-loadable/webpack`（webpack 插件）

```js
const { ReactLoadablePlugin } = require('react-loadable/webpack')

// webpack.config.js
plugins: [
  new ReactLoadablePlugin({ filename: './build/react-loadable.json' }),
]
```

**关键依赖**：直接作为 webpack plugin 运行，hook 进 webpack compiler，遍历 `chunks` / `modules` 生成"模块路径 → chunk 文件"映射。

- Plugin API 是 webpack 专属（Rollup/Vite 有自己的 plugin 体系，API 完全不同）
- 这个插件只在 webpack 生态里能用

## Vue

### 组件生命周期钩子函数

由于没有动态更新，所有的生命周期钩子函数中，只有 `beforeCreate` 和 `created` 会在服务器端渲染 (SSR) 过程中被调用。这就是说任何其他生命周期钩子函数中的代码（例如 `beforeMount` 或 `mounted`），只会在客户端执行。

此外还需要注意的是，你应该避免在 `beforeCreate` 和 `created` 生命周期时产生全局副作用的代码，例如在其中使用 `setInterval` 设置 timer。在纯客户端 (client-side only) 的代码中，我们可以设置一个 timer，然后在 `beforeDestroy` 或 `destroyed` 生命周期时将其销毁。但是，由于在 SSR 期间并不会调用销毁钩子函数，所以 timer 将永远保留下来。为了避免这种情况，请将副作用代码移动到 `beforeMount` 或 `mounted` 生命周期中。

### 避免状态单例

当编写纯客户端 (client-only) 代码时，我们习惯于每次在新的上下文中对代码进行取值。但是，Node.js 服务器是一个长期运行的进程。当我们的代码进入该进程时，它将进行一次取值并留存在内存中。这意味着如果创建一个单例对象，它将在每个传入的请求之间共享。

如基本示例所示，我们**为每个请求创建一个新的根 Vue 实例**。这与每个用户在自己的浏览器中使用新应用程序的实例类似。如果我们在多个请求之间使用一个共享的实例，很容易导致交叉请求状态污染 (cross-request state pollution)。

因此，我们不应该直接创建一个应用程序实例，而是应该暴露一个可以重复执行的工厂函数，为每个请求创建新的应用程序实例：

```js
// app.js
const Vue = require('vue')

module.exports = function createApp (context) {
  return new Vue({
    data: {
      url: context.url
    },
    template: `<div>访问的 URL 是： {{ url }}</div>`
  })
}
```

>同样的规则也适用于 router、store 和 event bus 实例。你不应该直接从模块导出并将其导入到应用程序中，而是需要在 `createApp` 中创建一个新的实例，并从根 Vue 实例注入。

### 数据预取存储容器 (Data Store)

[数据预取存储容器](https://ssr.vuejs.org/zh/guide/data.html#数据预取存储容器-data-store)

## 补充

### asset-require-hook

[网页链接](https://www.npmjs.com/package/asset-require-hook)

解决SSR中图片路径的问题

### css-modules-require-hook

[css-modules-require-hook](https://www.npmjs.com/package/css-modules-require-hook)

[react中SSR](https://www.jianshu.com/p/47c8e364d0bc?appinstall=1&mType=Group)

解决后端不支持CSS的问题

The require hook compiles CSS Modules in runtime. This is similar to Babel's babel/register.

[See the example](https://github.com/css-modules/css-modules-require-hook/tree/a432b76e1eb46a7bf9ef729c16a96b6ef2295410/demo)

The second one allows you to move options to the separate file cmrh.conf.js. Config file should be located in the same directory where executor is or in its ancestor directories. In that case hook will be attached right after the css-modules-require-hook/preset module will be required. Example:

```js
// cmrh.conf.js
module.exports = {
  generateScopedName: '[name]__[local]___[hash:base64:5]',
};

require('css-modules-require-hook/preset');
 
// const styles = require('./icon.css');
```

### Using with babel-node / ES6 Imports

You will need to create a cmrh.conf.js file within the directory as you are importing css-modules-require-hook.

```js
// server.js
import csshook from 'css-modules-require-hook/preset' // import hook before routes
import routes from '/shared/views/routes'
 
// create server, etc

// cmrh.conf.js
module.exports = {
  // Same scope name as in webpack build
  generateScopedName: '[name]__[local]___[hash:base64:5]',
}
```

**需要注意的此处有一小坑，csshook必须放所有组件引入之前**

```js
// 处理css
import csshook from 'css-modules-require-hook/preset';

const express = require("express"); const bodyParser = require("body-parser"); const cookieParser = require("cookie-parser"); const userRoute = require("./userRoute"); const app = express(); const path = require('path'); app.use(cookieParser()); app.use(bodyParser.json());
```

### SSR 要用 Node.js

**不是"Node 适合 SSR"，而是"SSR 不得不用 Node"**。

| 方案                                      | 可行性     | 问题                        |
| ----------------------------------------- | ---------- | --------------------------- |
| **用 Node.js 跑 JS**                      | ✅ 原生支持 | CPU 密集有瓶颈              |
| **用 Java/Go 重写 React**                 | ❌ 不现实   | React 官方只维护 JS 版      |
| **用 Java 嵌 JS 引擎（Nashorn/GraalJS）** | 技术上可行 | 性能不如 V8、生态脱节       |
| **用 Rust 跑 V8（Deno）**                 | 可行       | 本质还是跑 JS，CPU 瓶颈一样 |
| **不做 SSR，改 CSR**                      | 可行       | 首屏性能 / SEO 问题         |

Node.js 做 SSR 的劣势

```text
JS 单线程 + renderToString 同步执行
    ↓
单个 Pod 同时只能渲染 1 个页面
    ↓
CPU 密集场景下 QPS 上限低
```
