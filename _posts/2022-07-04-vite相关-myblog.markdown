---
layout:     post
title:      "Vite 原理、配置与工程实践总结"
description: "系统整理「Vite 原理、配置与工程实践总结」的配置方法、工作机制、工程实践和常见问题。"
date:       2022-07-04 19:49:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”
>

## 基础

[深入理解Vite核心原理](https://juejin.cn/post/7064853960636989454)

Vite 是一种现代前端构建工具，目标是为现代 Web 项目提供更快、更轻量的开发体验。它主要由两部分组成：

- 一个开发服务器：基于浏览器原生 [ES Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 提供快速启动、按需转换和模块热更新（HMR）。
- 一套生产构建命令：根据当前官方文档，生产构建由 [Rolldown](https://rolldown.rs/) 驱动，预配置输出高度优化的静态资源。（早期 Vite 版本主要基于 Rollup，很多配置仍然沿用 Rollup/Rolldown 兼容的插件模型。）

`Vite` 的核心原理是利用浏览器原生支持 `import` 的能力。开发阶段不再像传统打包器一样先把整个应用 bundle 完再启动，而是启动一个基于 `connect` 中间件体系的 dev server，浏览器请求哪个模块，Vite 就按需对该模块做解析、转换、依赖重写，然后以 ESM 形式返回给浏览器。

所以开发阶段可以理解为：

```text
浏览器请求 /src/main.ts
  ↓
Vite dev server 拦截请求
  ↓
resolveId / load / transform
  ↓
重写 bare import、注入 HMR、生成 sourcemap
  ↓
返回浏览器可执行的 ESM
```

这就是 Vite 冷启动和 HMR 快的核心原因：**源码按需转换，依赖预构建，更新时只处理受影响模块，而不是全量重新打包。**

### alias

```tsx
export default defineConfig({
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});

```

### base

```tsx
export default defineConfig({
  base: "./",
});
```

## plugin

[https://cn.vitejs.dev/guide/api-plugin.html](https://cn.vitejs.dev/guide/api-plugin.html)

### enforce: pre

If you want your plugin to handle resolveId before the internal resolver, try enforce: pre

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

### configureServer

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

### load 和  resolveId

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

## 常用 plugin

常用的一些plugin

### vite-plugin-inspect

[https://github.com/antfu/vite-plugin-inspect](https://github.com/antfu/vite-plugin-inspect)

Inspect the intermediate state of Vite plugins. Useful for debugging and authoring plugins.

### **esbuildScanPlugin**

> 内置插件

[https://www.modb.pro/db/96623](https://www.modb.pro/db/96623)

源码： packages/vite/src/node/optimizer/scan.ts

它是从入口文件开始，扫描所有文件，然后找到所有 **bare imports** 即官方称为裸导入的路径，然后记录在 **deps** 对象上。

>  ★ 所谓的 **bare imports** 就是需要从 **node_modules** 中导入的模块。

### vite-plugin-react

[https://www.npmjs.com/package/@vitejs/plugin-react](https://www.npmjs.com/package/@vitejs/plugin-react)

[学不动系列之vite-plugin-react](https://zhuanlan.zhihu.com/p/146273954)

内部 dependencies  => react-refresh

react-refresh主要有两个文件，一个是babel的插件ReactFreshBabelPlugin.js， 一个是ReactFreshRuntime.js。

### vite-dev-rpc

[https://www.npmjs.com/package/vite-dev-rpc](https://www.npmjs.com/package/vite-dev-rpc)

Remote procedure call for client-server communication in Vite plugins.

### vite-plugin-commonjs-externals

Provides commonjs externals support for Vite.

为Vite提供commonjs外部支持。

Prevent bundling of certain *esm* `import`ed packages and instead retrieve these external dependencies at runtime by *commonjs* `require`.

防止绑定某些esm导入的包，而是在运行时通过commonjs require检索这些外部依赖项。

### @vitejs/plugin-legacy

vitejs/plugin-legacy 插件用于在旧版浏览器中支持 ES2015+ 特性

#### targets

targets 是一个对象，可指定要支持的最低浏览

```css
{
  targets: {
    ie: '11'
  }
}
```

上面的配置使用了 IE11 最小支持版本。当使用该插件对代码进行转换时，Vite 会尝试根据这个目标将代码转换为 ES5 代码。

The query is also [Browserslist compatible](https://github.com/browserslist/browserslist)

也就说不是必要填targets 也可用browserslist 的形式表达

#### globalThis is not defined 问题

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

#### Polyfills 和 additionalLegacyPolyfills

* Polyfills：这是一组默认的 polyfills，包括一些常见的 JavaScript API、ES2015+ 特性和浏览器支持的功能。这些 polyfills 能够在旧版浏览器中模拟现代浏览器的功能，使得你的代码可以跨浏览器运行。这些 polyfills 不需要手动配置，在启用插件时会默认加载。
* additionalLegacyPolyfills：这是一个用户定义的选项，用于添加额外的 polyfills。如果你使用了某些 ES2015+ 特性或浏览器 API，而默认的 Polyfills 无法支持这些特性，你可以在这里添加相应的 polyfills。这些 polyfills 需要手动配置，并且会在启用插件时一起加载。

#### 结合 regenerator-runtime

regenerator-runtime 是一个将 generator 和 async/await 等语法转换为 ES5 版本的库，用于在不支持这些语法的浏览器中运行。

这两者结合使用，可以让我们在 Vite 中使用 generator 和 async/await 等语法，并且能够在不支持这些语法的浏览器中运行。

```bash
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

## Vite 官方核心概念补充

官方 Guide 里有几个很重要的设计点。

### index.html 是入口

在 Vite 项目中，`index.html` 不是放在 `public` 里的静态模板，而是项目的一等入口文件。

原因是：开发阶段 Vite 本质上是一个 dev server，`index.html` 是浏览器访问应用的入口。Vite 会解析其中的：

```html
<script type="module" src="/src/main.ts"></script>
<link rel="stylesheet" href="/src/style.css" />
```

并把这些资源纳入模块图处理。

这意味着：

- `index.html` 会被 Vite 插件处理；
- `<script type="module">` 会进入模块转换管线；
- HTML 中引用的 CSS、图片等资源会被自动处理；
- 多页面应用可以有多个 `.html` 入口。

### 开发阶段为什么不打包

传统 bundle-based dev server：

```text
启动 dev server
  ↓
先打包整个应用
  ↓
浏览器才能访问页面
```

应用越大，启动越慢。

Vite dev server：

```text
启动 dev server
  ↓
立即返回 index.html
  ↓
浏览器按 import 请求模块
  ↓
Vite 按需转换模块
```

开发启动时间不再和整个应用体积强绑定。

### 生产环境为什么仍然需要打包

开发阶段不打包很好，因为可以按需请求模块。但生产环境如果也直接发送大量未打包 ESM，会有很多网络请求和嵌套 import round trips。

所以 Vite 的策略是：

```text
开发阶段：原生 ESM + 按需转换 + 快速 HMR
生产阶段：Rolldown/Rollup 风格打包 + chunk 优化 + 静态资源优化
```

### 浏览器兼容

根据当前官方文档：

- 开发阶段默认面向现代浏览器，尽量少做语法降级；
- 生产构建默认面向 Baseline Widely Available 浏览器；
- 旧浏览器可以使用 `@vitejs/plugin-legacy` 支持；
- Vite 默认主要做语法转换，不自动覆盖所有 polyfill。

### TypeScript：只转译，不类型检查

Vite 支持直接导入 `.ts` 文件，但默认只做 transpile，不做 type checking。

原因是：

- 转译可以按文件处理，适合 Vite 的按需编译模型；
- 类型检查需要理解完整模块图，会拖慢 dev server；
- 静态检查应该交给 IDE、`tsc --noEmit` 或 `vite-plugin-checker`。

推荐：

```bash
tsc --noEmit
```

开发时也可以单独开 watch：

```bash
tsc --noEmit --watch
```

### import.meta.glob

Vite 支持 `import.meta.glob` 批量导入文件：

```js
const modules = import.meta.glob('./dir/*.js')
```

会被转换成：

```js
const modules = {
  './dir/bar.js': () => import('./dir/bar.js'),
  './dir/foo.js': () => import('./dir/foo.js'),
}
```

默认是懒加载。如果想立即加载：

```js
const modules = import.meta.glob('./dir/*.js', { eager: true })
```

注意：

- 这是 Vite 专属能力，不是 ECMAScript 标准；
- glob 参数必须是字面量，不能是变量；
- 路径必须是相对路径、绝对路径或 alias 路径。

### 静态资源

Vite 支持直接导入静态资源：

```js
import imgUrl from './img.png'
```

也支持 query：

```js
import rawText from './shader.glsl?raw'
import assetUrl from './asset.svg?url'
import Worker from './worker.ts?worker'
```

### CSS 能力

Vite 默认支持：

- CSS import；
- CSS HMR；
- CSS Modules；
- PostCSS 自动加载；
- Sass / Less / Stylus 预处理器；
- `url()` 自动 rebasing；
- 生产构建 CSS code splitting。

预处理器需要安装对应依赖：

```bash
npm add -D sass-embedded less stylus
```

## 源码分析

下面以当前 Vite 主线源码结构为参考。

### createServer：dev server 入口

关键文件：

```text
packages/vite/src/node/server/index.ts
```

核心入口：

```ts
export function createServer(inlineConfig = {}) {
  return _createServer(inlineConfig, { listen: true })
}
```

`_createServer` 内部主要做这些事：

1. `resolveConfig(inlineConfig, 'serve')` 解析配置；
2. 创建 `connect()` 中间件容器；
3. 创建 HTTP server；
4. 创建 WebSocket server，用于 HMR；
5. 创建 `chokidar` watcher，监听文件变更；
6. 创建 client / ssr environments；
7. 创建 `moduleGraph`；
8. 创建 `pluginContainer`；
9. 注册内部中间件；
10. 调用用户插件的 `configureServer`。

简化结构：

```ts
const middlewares = connect()
const httpServer = await resolveHttpServer(middlewares, httpsOptions)
const ws = createWebSocketServer(httpServer, config, httpsOptions)
const watcher = chokidar.watch([...], resolvedWatchOptions)

const moduleGraph = new ModuleGraph(...)
const pluginContainer = createPluginContainer(environments)

middlewares.use(transformMiddleware(server))
middlewares.use(serveStaticMiddleware(server))
middlewares.use(indexHtmlMiddleware(root, server))
```

可以看出 Vite dev server 的核心对象是：

| 对象 | 作用 |
| --- | --- |
| `middlewares` | Connect 中间件栈 |
| `httpServer` | Node HTTP server |
| `ws` | HMR WebSocket 通道 |
| `watcher` | 文件监听器 |
| `moduleGraph` | 模块依赖图和 HMR 状态 |
| `pluginContainer` | 插件钩子执行容器 |

### transformRequest：请求到转换

关键文件：

```text
packages/vite/src/node/server/transformRequest.ts
```

当浏览器请求一个模块时，大致流程是：

```text
HTTP request
  ↓
transformMiddleware
  ↓
server.transformRequest(url)
  ↓
pluginContainer.resolveId
  ↓
pluginContainer.load
  ↓
pluginContainer.transform
  ↓
缓存 transformResult
  ↓
返回 ESM code
```

源码中的关键函数：

```ts
export function transformRequest(environment, url, options) {
  const pending = environment._pendingRequests.get(url)
  if (pending) return pending.request

  const request = doTransform(environment, url, options, timestamp)
  environment._pendingRequests.set(url, { request, timestamp, abort })
  return request.finally(clearCache)
}
```

这里有两个重要优化：

1. **pending request 复用**
   - 同一个模块同时被多个请求命中时，不重复转换。

2. **transformResult 缓存**
   - 如果模块没有失效，直接复用上次转换结果。

`loadAndTransform` 的核心逻辑：

```ts
const loadResult = await pluginContainer.load(id)

if (loadResult == null) {
  code = await fs.readFile(file, 'utf-8')
}

const transformResult = await pluginContainer.transform(code, id)
moduleGraph.updateModuleTransformResult(mod, result)
```

也就是：先让插件 `load`，插件没处理再读文件，然后进入 `transform` 管线。

### pluginContainer：插件执行容器

关键文件：

```text
packages/vite/src/node/server/pluginContainer.ts
```

Vite 在 dev 阶段并不会真正执行完整打包，但它仍然模拟了 Rollup/Rolldown 的插件钩子：

- `options`；
- `buildStart`；
- `resolveId`；
- `load`；
- `transform`；
- `watchChange`；
- `buildEnd`；
- `closeBundle`。

开发阶段每个模块请求会触发：

```text
resolveId → load → transform
```

`resolveId` 是 hook-first：

```ts
for (const plugin of this.getSortedPlugins('resolveId')) {
  const result = await plugin.resolveId?.(rawId, importer, options)
  if (result) return result
}
```

`load` 也是找到第一个非空结果就返回：

```ts
for (const plugin of this.getSortedPlugins('load')) {
  const result = await plugin.load?.(id)
  if (result != null) return result
}
```

`transform` 是流水线：

```ts
for (const plugin of this.getSortedPlugins('transform')) {
  const result = await plugin.transform?.(code, id)
  if (result) code = result.code || result
}
```

所以插件机制可以理解为：

```text
resolveId：确定模块 id
load：获取模块内容
transform：依次转换代码
```

### HMR 源码流程

关键文件：

```text
packages/vite/src/node/server/hmr.ts
```

文件变化后，server 中的 watcher 会触发：

```ts
watcher.on('change', file => {
  onFileChange(file)
})
```

然后进入：

```ts
handleHMRUpdate(type, file, server)
```

核心流程：

```text
文件变化
  ↓
moduleGraph.getModulesByFile(file)
  ↓
执行插件 hotUpdate / handleHotUpdate
  ↓
updateModules
  ↓
propagateUpdate 找 HMR 边界
  ↓
能热更新：ws.send({ type: 'update' })
  ↓
不能热更新：ws.send({ type: 'full-reload' })
```

`updateModules` 中会递归向 importer 传播更新：

```text
changed module
  ↓
如果 self accepting，作为 HMR boundary
  ↓
否则向 importers 继续传播
  ↓
找到边界则局部更新
  ↓
找不到边界则 full reload
```

所以 Vite HMR 快的原因不是“魔法”，而是：

- dev 阶段模块天然是 ESM；
- Vite 维护了 `moduleGraph`；
- 文件变更后只让相关模块失效；
- 能找到 HMR 边界就只推送局部更新。

### 依赖扫描和预构建

关键文件：

```text
packages/vite/src/node/optimizer/scan.ts
```

官方文档说，浏览器原生 ESM 不支持这种 bare import：

```js
import { debounce } from 'lodash-es'
```

浏览器不知道 `lodash-es` 应该从哪里加载。

Vite 会做两件事：

1. 扫描源码里的 bare imports；
2. 把依赖预构建到 `node_modules/.vite/deps`，并重写 import URL。

当前源码中，依赖扫描大致流程：

```ts
export function scanImports(environment) {
  const entries = await computeEntries(environment)
  const deps = {}
  const missing = {}
  const context = await prepareRolldownScanner(environment, entries, deps, missing)
  await context.build()
  return { deps, missing }
}
```

`computeEntries` 会优先使用：

1. `optimizeDeps.entries`；
2. `build.rolldownOptions.input`；
3. 默认扫描 `**/*.html`。

扫描插件中会识别 bare import：

```ts
if (/^[\w@][^:]/.test(id)) {
  const resolved = await resolve(id, importer)
  if (isInNodeModules(resolved) || include?.includes(id)) {
    depImports[id] = resolved
  }
}
```

因此预构建的本质是：

```text
HTML / JS / TS 入口
  ↓
扫描 import
  ↓
发现 bare imports
  ↓
解析到 node_modules
  ↓
记录 deps
  ↓
预构建为浏览器可直接 import 的 ESM 文件
```

## Vite+ 介绍

官网：https://viteplus.dev/

Vite+ 的定位是：

> The Unified Toolchain for the Web.

也就是一个统一的 Web 开发工具链入口。它不是简单的 Vite 新名字，而是在 Vite 之上，把运行时、包管理器、开发、检查、测试、构建、打包、任务执行等流程整合到一个 `vp` 命令里。

官方 Guide 中说，Vite+ 由两部分组成：

| 部分 | 说明 |
| --- | --- |
| `vp` | 全局命令行工具 |
| `vite-plus` | 安装在项目本地的包 |

它整合的工具包括：

- `Vite`：dev server 和应用构建；
- `Vitest`：测试；
- `Oxlint`：lint；
- `Oxfmt`：format；
- `Rolldown`：打包；
- `tsdown`：库构建；
- `Vite Task`：monorepo 任务执行和缓存；
- `tsgo`：TypeScript 类型检查相关能力。

### 安装

macOS / Linux：

```bash
curl -fsSL https://vite.plus | bash
```

Windows PowerShell：

```powershell
irm https://vite.plus/ps1 | iex
```

安装后：

```bash
vp help
```

官方提示：Vite+ 会管理全局 Node.js runtime 和 package manager。如果不想让它管理，可以：

```bash
vp env off
```

### 常用命令

```bash
vp create   # 创建项目
vp install  # 安装依赖
vp dev      # 启动开发服务器
vp check    # format + lint + type-check
vp test     # 运行测试
vp build    # 生产构建
vp preview  # 本地预览生产构建
vp run      # 执行 workspace/package scripts，带缓存能力
vp pack     # 构建库或 standalone artifacts
```

### vp check

`vp check` 是 Vite+ 很重要的能力。

它把这些事情合到一个命令：

```text
format + lint + type-check
```

背后主要由：

- `Oxfmt`；
- `Oxlint`；
- `tsgo`；

提供能力。

可以自动修复的场景：

```bash
vp check --fix
```

### vp run

`vp run` 面向 monorepo 和脚本任务：

- 自动输入追踪；
- 可缓存任务；
- 按 workspace 依赖关系执行；
- 执行 `package.json` scripts。

这类能力类似把包管理器脚本、任务编排和缓存系统统一起来。

### vp pack

`vp pack` 面向库构建：

- DTS 生成和打包；
- 自动 package exports；
- standalone app binaries；
- transform-only unbundled mode。

底层主要由 `Rolldown` / `tsdown` 提供能力。

### Vite 与 Vite+ 的关系

可以这样理解：

```text
Vite = dev server + build tool
Vite+ = 统一工具链入口
```

对比：

| 能力 | Vite | Vite+ |
| --- | --- | --- |
| dev server | 支持 | 支持，`vp dev` |
| build | 支持 | 支持，`vp build` |
| test | 需要单独配置 Vitest | 内置 `vp test` 流程 |
| lint | 需要 ESLint/Oxlint 配置 | `vp check` 集成 Oxlint |
| format | 需要 Prettier/Oxfmt 配置 | `vp check` 集成 Oxfmt |
| type check | 需要 `tsc` 或插件 | `vp check` 集成 type-check |
| package manager | 不管理 | `vp install/add/remove` |
| Node runtime | 不管理 | `vp env` 管理 |
| monorepo task | 需要 Turborepo/Nx 等 | `vp run` 提供任务缓存 |
| library pack | Vite library mode 或 tsup/tsdown | `vp pack` |

Vite+ 更像是把现代前端常用工具链标准化，减少每个项目重复拼装工具的成本。

## 框架对比

### Vite vs Webpack

| 维度 | Vite | Webpack |
| --- | --- | --- |
| 开发启动 | 原生 ESM，按需转换 | 通常先构建依赖图并打包 |
| HMR | 基于 ESM 和 moduleGraph，更新粒度更小 | 依赖 bundle runtime 和重新编译 |
| 生产构建 | Rolldown/Rollup 风格 | Webpack 自身打包体系 |
| 配置复杂度 | 默认配置友好 | 高度灵活但复杂 |
| 生态 | 现代框架默认支持 | 老牌生态极丰富 |
| 适合场景 | 现代 Web App、库、SSR 框架 | 复杂遗留项目、高度定制历史工程 |

简单说：

- 新项目优先 Vite；
- 旧项目大量 webpack loader/plugin 深度绑定时，迁移要评估；
- Webpack 仍然适合特别复杂的历史工程和高度定制场景。

### Vite vs Rspack

`Rspack` 是 Rust 写的高性能 Webpack 兼容构建工具。

| 维度 | Vite | Rspack |
| --- | --- | --- |
| 核心思路 | 开发期 ESM 按需服务 | Webpack-compatible bundler |
| 兼容目标 | Vite 插件生态 | Webpack 生态兼容 |
| 开发体验 | 冷启动和 HMR 强 | 大型 Webpack 项目迁移友好 |
| 生产构建 | Rolldown/Rollup 风格 | Rust bundler |
| 适合场景 | 新项目、现代框架 | Webpack 项目提速、渐进迁移 |

如果项目已经深度依赖 Webpack 配置，`Rspack` 迁移成本可能低于 Vite。

### Vite vs Turbopack

`Turbopack` 是 Vercel 推出的 Rust 增量打包器，主要服务 Next.js 生态。

| 维度 | Vite | Turbopack |
| --- | --- | --- |
| 生态 | 多框架通用 | Next.js 强绑定 |
| 开发方式 | 原生 ESM + 插件生态 | 增量 bundling |
| 适合框架 | Vue、React、Svelte、Solid 等 | Next.js |
| 成熟度 | 前端通用构建事实标准之一 | 在 Next.js 场景持续演进 |

如果你做的是 Next.js，优先跟随 Next 官方工具链；如果是通用 SPA / SSR / 库项目，Vite 更通用。

### Vite vs Parcel

`Parcel` 主打零配置和自动化。

| 维度 | Vite | Parcel |
| --- | --- | --- |
| 配置 | 简洁但可配置 | 更强调零配置 |
| 插件生态 | Vite/Rollup/Rolldown 插件生态 | Parcel 插件生态 |
| 框架集成 | 很强 | 支持广泛 |
| 社区主流度 | 当前现代前端主流选择 | 相对小众 |

### Vite vs Esbuild

`esbuild` 是 Go 写的极速 bundler/transpiler。

Vite 和 esbuild 不是完全同类：

- esbuild 更像底层构建和转译工具；
- Vite 是完整前端开发工具，包含 dev server、HMR、插件、HTML 处理、静态资源、生产构建等。

早期 Vite 大量使用 esbuild 做依赖预构建和 TS/JS 转译；当前官方文档中，Vite 越来越多能力转向 Oxc / Rolldown 体系。

## 补充

### webpack 和 vite 的区别

* 本地开发环境webpack也是需要先打包，然后服务器运行的是打包后的文件，码量很大的项目就会有启服务很慢的现象
* **Webpack 的热更新会以当前修改的文件为入口重新 build 打包**，所有涉及到的依赖也都会被重新加载一次。虽然webpack 也采用的是局部热更新并且是有缓存机制的，但是还是需要重新打包所以很大的代码项目是真的有卡顿的现象
* vite 可以做到**快速冷启动**。只启动一台静态页面的服务器，对文件代码不打包，服务器会根据客户端的请求加载不同的模块处理（利用的是浏览器对esMoudle的原生支持）

### 预构建

为什么需要预构建？

1. **支持`commonJS`依赖。**上面提到`Vite`是基于浏览器原生支持`ESM`的能力实现的，但要求用户的代码模块必须是`ESM`模块，因此必须将`commonJs`的文件提前处理，转化成 `ESM` 模块并缓存入 `node_modules/.vite`
2. **减少模块和请求数量**

常用的`lodash`工具库，里面有很多包通过单独的文件相互导入，而 `lodash-es`这种包会有几百个子模块，当代码中出现 `import { debounce } from 'lodash-es'` 会发出几百个 `HTTP` 请求，这些请求会造成网络堵塞，影响页面的加载。

 `Vite` 将有许多内部模块的 `ESM` 依赖关系转换为单个模块，以提高后续页面加载性能。

通过预构建 `lodash-es` 成为一个模块，也就只需要一个 `HTTP` 请求了！

### **快速冷启动**

只启动一台静态页面的服务器，对文件代码不打包，服务器会根据客户端的请求加载不同的模块处理（利用的是浏览器对esMoudle的原生支持），所以节省了webpack 那一套打包转化封装的逻辑。所以大型项目不会再出现热更新卡顿，起服务慢的情况

### 缓存

文件缓存：

Vite 会将预构建的依赖缓存到`node_modules/.vite`。它根据几个源来决定是否需要重新运行预构建步骤:`package.json` 中的 `dependencies` 列表， package-lock等

浏览器缓存：

解析后的依赖请求会以 HTTP 头 `max-age=31536000,immutable` 强缓存，以提高在开发时的页面重载性能。一旦被缓存，这些请求将永远不会再到达开发服务器

### HMR 热更新

[https://zhuanlan.zhihu.com/p/424842555](https://zhuanlan.zhihu.com/p/424842555)

目前所有的打包工具实现热更新的思路都大同小异：主要是通过`WebSocket`创建浏览器和服务器的通信监听文件的改变，当文件被修改时，服务端发送消息通知客户端修改相应的代码，客户端对应不同的文件进行不同的操作的更新。

* Webpack:  重新编译，请求变更后模块的代码，客户端重新加载
* Vite:  请求变更的模块，再重新加载

`Vite` 通过 `chokidar` 来监听文件系统的变更，只用对发生变更的模块重新加载， 只需要精确的使相关模块与其临近的 `HMR`边界连接失效即可，**这样`HMR` 更新速度就不会因为应用体积的增加而变慢而 `Webpack` 还要经历一次打包构建。所以 `HMR` 场景下，`Vite` 表现也要好于 `Webpack`。**

### 多入口

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

### normalizePath

[https://cn.vitejs.dev/guide/api-plugin.html#path-normalization](https://cn.vitejs.dev/guide/api-plugin.html#path-normalization)

Vite 对路径进行了规范化处理，在解析路径时使用 POSIX 分隔符（ / ），同时保留了 Windows 中的卷名。而另一方面，Rollup 在默认情况下保持解析的路径不变，因此解析的路径在 Windows 中会使用 win32 分隔符（ \ ）。

```tsx
import { normalizePath } from 'vite'

normalizePath('foo\\bar') // 'foo/bar'
normalizePath('foo/bar') // 'foo/bar'
```

### css、less、postcss

[https://juejin.cn/post/7060351422885265415](https://juejin.cn/post/7060351422885265415)

**css:**

`Vite` 默认就支持对 `css` 代码的处理（而不需要再像使用 `Webpack` 时，还需要使用 `css-loader` 和 `style-loader`）。它也是通过在 `<head>` 元素中插入包含了有关样式代码的 `<style>` 元素实现的：

**less:**

预处理器需要依赖 `less` 这个工具，但 `Vite` 没有找到它。确实，我们还没有安装它，所以我们需要先安装 `less` 这个工具（前面在讲 `Webpack` 时说过，在使用 `less-loader` 去加载 `less` 文件时，也需要依赖 `less` 这个工具，所以必须先安装 `less`。只不过在 `Vite` 中，我们不再需要使用 `less-loader` 了，但 `less` 工具还得用）：

```tsx
npm install less -D
```

### 输出 cjs 和 es

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

### build.lib

[https://cn.vitejs.dev/guide/build.html#library-mode](https://cn.vitejs.dev/guide/build.html#library-mode)

构建为库。`entry` 是必须的因为库不能使用 HTML 作为入口。`name` 则是暴露的全局变量，在 `formats` 包含 `'umd'` 或 `'iife'` 时是必须的。默认 `formats` 是 `['es', 'umd']` 。`fileName` 是输出的包文件名，默认 `fileName` 是 `package.json` 的 `name` 选项，同时，它还可以被定义为参数为 `format` 的函数。

### external

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

### css.preprocessorOptions

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

### glob

[https://cn.vitejs.dev/guide/features.html#glob-import](https://cn.vitejs.dev/guide/features.html#glob-import)

Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```js
const modules = import.meta.glob('./dir/*.js')
```

### with babel

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

### 打包分析

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

## 问题

### import *.vue file without .vue will not work

[https://github.com/vitejs/vite/issues/178](https://github.com/vitejs/vite/issues/178)

This is by design. We will also stop supporting extension-less Vue imports in vue-cli in the next major.

### vue runtime compilation

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

### Can css be styleInjected in library mode?

[https://github.com/vitejs/vite/issues/1579](https://github.com/vitejs/vite/issues/1579)

The problem is injecting the style assumes a DOM environment which will make the library SSR-incompatible.

If you only have minimal css, the easiest way is to simply use inline styles.

我们可以使用一个插件

**[vite-plugin-css-injected-by-js](https://github.com/Marco-Prontera/vite-plugin-css-injected-by-js)**

A Vite plugin that takes the CSS and adds it to the page through the JS. For those who want a single JS file.

### Multiple entry points/output in library mode?

[https://github.com/vitejs/vite/discussions/1736](https://github.com/vitejs/vite/discussions/1736)

多入口 库模式打包

vite 的 lib 库模式打包 lib.entry 只支持单入口

解决：

```tsx
import { build } from 'vite'
```

多次调用这个build

### ship in CJS format when my package has pure ESM package dependencies?

[https://lightrun.com/answers/unjs-unbuild-how-to-ship-in-cjs-format-when-my-package-has-pure-esm-package-dependencies](https://lightrun.com/answers/unjs-unbuild-how-to-ship-in-cjs-format-when-my-package-has-pure-esm-package-dependencies)

use `rollup.inlineDependencies` and make your ESM-only dependency a development dependency
