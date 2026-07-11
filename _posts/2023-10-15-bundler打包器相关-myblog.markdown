---
layout:     post
title:      "Bundler 打包器与 esbuild 实践总结"
description: "系统整理「Bundler 打包器与 esbuild 实践总结」的配置方法、工作机制、工程实践和常见问题。"
date:       2023-10-15 16:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”

## 前言

本文整理常见 JavaScript / TypeScript 构建与打包工具，包括 Rollup、tsdown、tsup、esbuild、SWC、Rolldown、Turbopack、unbuild、father、Gulp 等。

不同工具的定位不完全相同：

| 工具 | 主要定位 | 底层实现 / 生态 | 适合场景 |
| --- | --- | --- | --- |
| Rollup | 库打包器 | JavaScript / 插件生态成熟 | 组件库、工具库、需要精细控制产物 |
| tsdown | TypeScript 库打包器 | Rolldown + Oxc | 现代 TS 库、从 tsup 迁移、追求构建速度 |
| tsup | TypeScript 库打包器 | esbuild + Rollup dts | 快速打包 TS 库、配置简单 |
| esbuild | 编译器 / 打包器 | Go | 极速转译、压缩、简单打包 |
| SWC | 编译器平台 | Rust | 替代 Babel、快速转译 |
| Rolldown | 新一代打包器 | Rust / Rollup 兼容方向 | Vite 未来底层、Rollup 生态迁移 |
| Turbopack | 应用构建工具 | Rust | Next.js 等大型应用开发构建 |
| unbuild | 库构建工具 | Rollup / mkdist | Nuxt / unjs 生态库打包 |
| father | NPM 包研发工具 | Umi 生态 | 组件库、工具库、React 相关包 |
| Gulp | 任务流工具 | Node Stream | 文件处理流水线、自动化任务 |

## Rollup

- 官网：[https://rollupjs.org/guide/en/](https://rollupjs.org/guide/en/)
- 中文文档：[https://www.rollupjs.com/](https://www.rollupjs.com/)
- REPL：[https://rollupjs.org/repl](https://rollupjs.org/repl)

Rollup 是一个以 ES Module 为核心的 JavaScript 打包器，尤其适合库打包。它的优势是产物干净、Tree Shaking 能力强、插件生态成熟。

### 基础能力

#### 使用 TypeScript 配置文件

`--configPlugin` 允许使用 Rollup 插件处理配置文件。例如使用 `@rollup/plugin-typescript` 解析 `rollup.config.ts`：

```bash
rollup --config rollup.config.ts --configPlugin @rollup/plugin-typescript
```

#### 多入口 / 多配置打包

Rollup 支持导出配置数组，适合同时构建多个入口或多个产物：

```ts
// rollup.config.ts
import { defineConfig } from 'rollup'

const config1 = defineConfig({})
const config2 = defineConfig({})
const config3 = defineConfig({})

export default (commandLineArgs: any) => {
  const isDev = commandLineArgs.watch

  return defineConfig([
    config1,
    config2,
    config3,
  ])
}
```

#### 支持 CommonJS

Rollup 默认支持 ES Module。如果需要处理 CommonJS 包，一般需要配合：

- [`@rollup/plugin-commonjs`](https://github.com/rollup/plugins/tree/master/packages/commonjs)
- [`@rollup/plugin-node-resolve`](https://github.com/rollup/plugins/tree/master/packages/node-resolve)

`@rollup/plugin-commonjs` 通常应该放在其他会转换代码的插件之前，避免 CommonJS 检测被破坏。

#### 处理第三方依赖

Rollup 不会像 Webpack 那样默认处理所有 `node_modules` 依赖。如果遇到 `Unresolved dependencies`，通常需要添加 `@rollup/plugin-node-resolve`：

```ts
import { nodeResolve } from '@rollup/plugin-node-resolve'

export default {
  plugins: [
    nodeResolve({
      browser: true,
      mainFields: ['browser', 'module', 'main'],
      preferBuiltins: false,
      extensions: ['.mjs', '.js', '.json', '.ts'],
    }),
  ],
}
```

如果依赖是 CommonJS，还需要配合 `@rollup/plugin-commonjs`。

### 常用插件

#### rollup-plugin-esbuild

- [https://www.npmjs.com/package/rollup-plugin-esbuild](https://www.npmjs.com/package/rollup-plugin-esbuild)

用于在 Rollup 中接入 esbuild，可替代部分 TypeScript 转译、ESNext 降级和压缩能力。

#### rollup-plugin-visualizer

- [https://www.npmjs.com/package/rollup-plugin-visualizer](https://www.npmjs.com/package/rollup-plugin-visualizer)

用于分析打包体积，排查大依赖和重复依赖。

#### @rollup/plugin-commonjs

- [https://www.npmjs.com/package/@rollup/plugin-commonjs](https://www.npmjs.com/package/@rollup/plugin-commonjs)

将 CommonJS 模块转换为 ES Module，便于 Rollup 纳入打包流程。

#### @rollup/plugin-node-resolve

用于按照 Node.js 解析规则查找外部模块，让 Rollup 能解析 `node_modules` 中的第三方包。

#### @rollup/pluginutils

- [https://www.npmjs.com/package/@rollup/pluginutils](https://www.npmjs.com/package/@rollup/pluginutils)

Rollup 插件开发工具集，常用能力包括：

```ts
export {
  addExtension,        // 增加扩展名
  attachScopes,        // AST 作用域处理
  createFilter,        // 处理 include / exclude
  dataToEsm,           // 将 data 转成 ES Module
  extractAssignedNames, // 获取 AST 节点名称
  makeLegalIdentifier, // 转成合法变量名
  normalizePath,       // 规范化路径
}
```

### 插件间通信

Rollup 插件可以通过暴露 `api` 给其他插件调用：

```ts
function parentPlugin() {
  return {
    name: 'parent',
    api: {
      doSomething(...args) {
        // do something
      },
    },
  }
}

function dependentPlugin() {
  let parentApi

  return {
    name: 'dependent',
    buildStart({ plugins }) {
      const parentPlugin = plugins.find(plugin => plugin.name === 'parent')

      if (!parentPlugin) {
        throw new Error('This plugin depends on the "parent" plugin.')
      }

      parentApi = parentPlugin.api
    },
    transform(code, id) {
      parentApi.doSomething(id)
    },
  }
}
```

### 常见问题

#### 如何把依赖全部打入一个 bundle？

参考：[How to embed all dependencies into one fat target bundle with Rollup.js](https://stackoverflow.com/questions/52125190/how-to-embed-all-dependencies-into-one-fat-target-bundle-with-rollup-js)

一般需要：

- 不把依赖配置为 `external`
- 使用 `@rollup/plugin-node-resolve` 解析依赖
- 如有 CommonJS 依赖，使用 `@rollup/plugin-commonjs`

## tsdown

- 官网：[https://tsdown.dev/zh-CN/](https://tsdown.dev/zh-CN/)
- GitHub：[https://github.com/rolldown/tsdown](https://github.com/rolldown/tsdown)

`tsdown` 是一个由 Rolldown 驱动的现代 TypeScript / JavaScript 库打包器。它强调开箱即用、构建速度快、类型声明生成快，并兼容 tsup 的主要选项和功能，适合从 tsup 平滑迁移。

### 核心特点

1. **速度快**：基于 Oxc 和 Rolldown 构建，并支持快速生成 `.d.ts`。
2. **适合库打包**：默认入口、输出目录、类型声明等都围绕 NPM 包研发设计。
3. **生态兼容**：支持 Rollup 插件、Rolldown 插件、unplugin 插件，以及部分 Vite 插件。
4. **迁移友好**：兼容 tsup 的主要配置选项和功能。
5. **产物格式丰富**：支持 `esm`、`cjs`、`iife`、`umd`。

> 注意：运行 `tsdown` 的构建环境要求 Node.js `22.18.0` 或更高版本，但构建产物可以通过 `target` 指定更低运行环境。

### 安装

```bash
npm install -D tsdown
# or
pnpm add -D tsdown
# or
yarn add -D tsdown
# or
bun add -D tsdown
```

如果项目需要生成声明文件，且没有启用 `isolatedDeclarations`，建议安装 TypeScript：

```bash
npm install -D typescript
```

也可以使用官方模板创建项目：

```bash
npm create tsdown@latest
```

### package.json

```json
{
  "name": "my-lib",
  "type": "module",
  "scripts": {
    "build": "tsdown",
    "dev": "tsdown --watch"
  },
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "import": "./dist/index.mjs",
      "require": "./dist/index.cjs"
    }
  },
  "main": "./dist/index.cjs",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts",
  "files": [
    "dist"
  ],
  "devDependencies": {
    "tsdown": "^0.9.0",
    "typescript": "^5.0.0"
  }
}
```

### 基础配置

```ts
// tsdown.config.ts
import { defineConfig } from 'tsdown'

export default defineConfig({
  entry: ['./src/index.ts'],
  format: ['esm', 'cjs'],
  dts: true,
  clean: true,
  sourcemap: true,
  minify: false,
})
```

常用命令：

```bash
# 构建
npx tsdown

# 监听模式
npx tsdown --watch
# or
npx tsdown -w

# 查看帮助
npx tsdown --help
```

### 常用配置项

#### entry

入口文件。默认会尝试使用 `src/index.ts`。

```ts
export default defineConfig({
  entry: ['./src/index.ts'],
})
```

也支持多入口和 glob：

```ts
export default defineConfig({
  entry: {
    index: './src/index.ts',
    'hooks/use-count': './src/hooks/use-count.ts',
  },
})
```

```ts
export default defineConfig({
  entry: {
    'hooks/*': ['./src/hooks/*.ts', '!./src/hooks/index.ts'],
  },
})
```

#### format

输出格式，默认是 `esm`。

```ts
export default defineConfig({
  format: ['esm', 'cjs'],
})
```

可选值：

- `esm` / `es` / `module`
- `cjs` / `commonjs`
- `iife`
- `umd`

如果需要 `iife` / `umd`，通常还需要配置 `globalName`。

#### dts

控制是否生成 TypeScript 声明文件：

```ts
export default defineConfig({
  dts: true,
})
```

`dts` 默认会根据 `package.json` 自动判断：

- `package.json` 有 `types` 字段时，默认启用；
- `exports` 中包含 `types` 时，默认启用；
- 开启 `exe` 时默认关闭；
- 其他情况默认关闭。

#### target

指定编译目标：

```ts
export default defineConfig({
  target: 'node18',
})
```

也可以指定多个目标：

```ts
export default defineConfig({
  target: ['node18', 'es2020'],
})
```

如果没有配置 `target`，tsdown 会优先读取 `package.json` 中的 `engines.node`；如果没有该字段，则不做语法降级。

#### platform

指定目标平台，默认是 `node`：

```ts
export default defineConfig({
  platform: 'browser',
})
```

可选值：

- `node`：Node.js、Bun、Deno 等服务端运行时；
- `browser`：浏览器环境；
- `neutral`：不假设具体运行环境。

#### clean / outDir

```ts
export default defineConfig({
  outDir: 'dist',
  clean: true,
})
```

`clean` 默认会在构建前清理输出目录。

#### minify / sourcemap / treeshake

```ts
export default defineConfig({
  minify: true,
  sourcemap: true,
  treeshake: true,
})
```

- `minify`：是否压缩产物，默认 `false`；
- `sourcemap`：是否生成 Source Map，默认 `false`；
- `treeshake`：Tree Shaking 配置，默认 `true`。

#### plugins

tsdown 支持 Rollup / Rolldown / unplugin 插件，以及部分 Vite 插件：

```ts
import SomePlugin from 'some-plugin'
import { defineConfig } from 'tsdown'

export default defineConfig({
  plugins: [SomePlugin()],
})
```

#### deps

新版 tsdown 推荐使用 `deps` 管理依赖是否打包。旧的 `external`、`noExternal`、`inlineOnly` 等配置仍可见，但已逐步被 `deps` 取代。

常见思路：

- 库开发一般把 `dependencies` / `peerDependencies` 外置，避免重复打包；
- 如果某些小依赖必须内联，可配置为总是打包；
- 如果某些依赖绝不能打包，可配置为永远外置。

#### publint / attw

tsdown 可以在打包后运行包检查：

```ts
export default defineConfig({
  publint: true,
  attw: true,
})
```

- `publint`：检查 NPM 包发布配置，需要安装 `publint`；
- `attw`：运行 `arethetypeswrong` 检查类型导出，需要安装 `@arethetypeswrong/core`。

### 与 tsup 的关系

`tsdown` 可以理解为面向库开发的新一代 tsup 替代方案：

- `tsup` 基于 esbuild，生态成熟，配置简单；
- `tsdown` 基于 Rolldown / Oxc，速度更快，并尽量兼容 tsup 的主要选项；
- 如果已有 tsup 项目，可以优先尝试直接迁移配置，再逐步替换不兼容项。

适合优先选择 tsdown 的场景：

- 新建 TypeScript 工具库 / 组件库；
- 希望同时输出 ESM + CJS + d.ts；
- 需要更快的构建和类型声明生成；
- 想跟进 Rolldown / Oxc 生态。

## tsup

- 官网：[https://tsup.egoist.dev/](https://tsup.egoist.dev/)
- 文章：[必知必会 tsup 打包库原理分析](https://juejin.cn/post/7148442413420265485)

`tsup` 是一个用于打包 TypeScript 库的工具，开箱即用，底层主要基于 esbuild。它可以快速生成 ESM / CJS 产物，也支持生成类型声明。

### 基础特点

- 基于 esbuild，构建速度快；
- 零配置或少配置即可打包 TS 库；
- 支持 CLI 和配置文件；
- 支持生成 `.d.ts`；
- 可输出多种模块格式。

### ES5 支持

- [https://tsup.egoist.dev/#es5-support](https://tsup.egoist.dev/#es5-support)

当需要输出 ES5 时，tsup 会先通过 esbuild 转成 ES2020，再通过 SWC 转成 ES5。

### Tree Shaking

- [https://tsup.egoist.dev/#tree-shaking](https://tsup.egoist.dev/#tree-shaking)

esbuild 默认开启 Tree Shaking，但部分场景效果不如 Rollup。tsup 提供了额外选项，可使用 Rollup 做 Tree Shaking。

## esbuild

- 官网：[https://esbuild.github.io/](https://esbuild.github.io/)

`esbuild` 是一个用 Go 编写的 JavaScript / TypeScript 构建工具，核心优势是速度极快。它可以完成转译、打包、压缩、格式转换等工作。

### 核心特点

1. **构建速度极快**：Go 实现，并发能力强。
2. **支持现代语法**：支持 ES6+、TypeScript、JSX。
3. **配置简单**：命令行和 JS API 都很直接。
4. **支持 Tree Shaking**：默认对 ESM 进行 Tree Shaking。
5. **支持开发和生产构建**：可用于快速开发构建和生产压缩。

### CLI 示例

```bash
esbuild src/main.js --bundle --minify --format=esm --target=es6 --outfile=build/agora-miniapp-sdk.js
```

参数说明：

- `--bundle`：打包依赖；
- `--minify`：压缩代码；
- `--format`：输出模块格式，支持 `iife`、`cjs`、`esm`；
- `--target`：目标运行环境；
- `--outfile`：输出文件。

### 常用能力

#### serve

- [https://esbuild.github.io/api/#serve](https://esbuild.github.io/api/#serve)

用于启动简单开发服务。

#### inject

- [https://esbuild.github.io/api/#inject](https://esbuild.github.io/api/#inject)

用于注入变量，效果类似 Webpack 的 `ProvidePlugin`。

#### tree-shaking

- [https://esbuild.github.io/api/#tree-shaking](https://esbuild.github.io/api/#tree-shaking)

默认支持 Tree Shaking，但主要依赖 ESM 静态结构。

#### analyze

- [https://esbuild.github.io/api/#analyze](https://esbuild.github.io/api/#analyze)

用于分析打包结果。

#### target

- [https://esbuild.github.io/api/#target](https://esbuild.github.io/api/#target)

用于指定语法降级目标。

### 局限

#### Tree Shaking 不如 Rollup 精细

参考：`esbuild doesn't remove unused imports from external modules`

esbuild 的 Tree Shaking 速度很快，但在复杂库打包场景中不一定能达到 Rollup 的精细程度。

#### 不支持完整降级到 ES5

- [Lowering for ES5?](https://github.com/evanw/esbuild/issues/297)

esbuild 不支持将所有现代语法完整转换为 ES5。例如当 `target` 设置为 `es5` 且代码中存在 `async function` 时，会报错：

```text
Transforming async functions to the configured target environment ("es5") is not supported yet
```

如果必须支持 ES5，可以考虑 tsup + SWC、Babel 或其他降级方案。

## SWC

- 官网：[https://swc.rs/](https://swc.rs/)
- 中文文档：[https://swc.nodejs.cn/](https://swc.nodejs.cn/)

SWC 是一个基于 Rust 的快速编译器平台，常用于替代 Babel。它被 Next.js、Parcel、Deno 等工具采用，也被多家公司用于大型项目构建。

### 优点

- 转译速度快；
- 支持 TypeScript、JSX、现代 JavaScript 语法；
- 适合作为 Babel 替代方案；
- 可集成到构建工具中。

### 缺点

- 插件开发门槛较高，需要了解 Rust / WebAssembly；
- Babel 插件生态更成熟，复杂插件迁移成本较高。

### @swc/wasm-web

- [https://swc.rs/docs/usage/wasm](https://swc.rs/docs/usage/wasm)

`@swc/wasm-web` 用于在 Web 环境中通过 WebAssembly 使用 SWC 能力，适合浏览器侧代码转换、在线编辑器、Playground 等场景。

## Rolldown

- 官网：[https://rolldown.rs/](https://rolldown.rs/)

Rolldown 是一个基于 Rust 的快速 JavaScript 打包器，目标是兼容 Rollup 生态，并服务于 Vite 等现代工具链。

### 特点

- Rust 实现，构建性能更强；
- API 和插件生态向 Rollup 靠拢；
- 适合承接大型项目的构建性能优化；
- 是 tsdown 的底层核心能力之一。

### 与 Rollup 的关系

可以把 Rolldown 理解为“性能更强、面向未来工具链”的 Rollup 兼容方向实现。对于已有 Rollup 插件生态的项目，Rolldown 的目标是尽可能降低迁移成本。

## unbuild

- [https://github.com/unjs/unbuild](https://github.com/unjs/unbuild)

`unbuild` 是 unjs 生态中的统一 JavaScript 构建系统，底层基于 Rollup，可生成 CommonJS、ES Module 和类型声明。

### 示例

```ts
// build.config.ts
import { defineBuildConfig } from 'unbuild'

export default defineBuildConfig({
  entries: [
    './src/index',
  ],
  clean: true,
  declaration: true,
  rollup: {
    emitCJS: true,
    commonjs: true,
    resolve: true,
    inlineDependencies: true,
  },
})
```

### 使用注意

如果已经配置了 `resolve` 等选项，仍然遇到 `Could not resolve`，需要重点检查：

- 依赖是否应该 external；
- 依赖是否为 CJS / ESM 混合产物；
- `package.json` 的 `exports` / `main` / `module` 是否正确；
- 是否需要切换到 Rollup、tsup 或 tsdown 获得更强控制。

## Turbopack

- [https://turbo.build/pack/docs/why-turbopack](https://turbo.build/pack/docs/why-turbopack)

Turbopack 是一个面向现代 Web 应用的构建工具，由 Rust 编写，可以看作 Webpack 的继任方向之一，尤其常见于 Next.js 生态。

### 特点

1. **速度快**：Rust 实现，支持增量构建；
2. **面向现代框架**：重点服务 React / Next.js 等现代应用；
3. **配置简化**：相比 Webpack 更关注开箱即用；
4. **渐进迁移**：目标是尽量兼容现有生态。

## father

- [https://github.com/umijs/father](https://github.com/umijs/father)

`father` 是 Umi 生态中的 NPM 包研发工具，适合组件库、工具库等包的构建和发布。

### 适合场景

- React 组件库；
- Umi / Ant Design 生态项目；
- 需要同时管理构建、类型、发布规范的 NPM 包。

## Gulp

Gulp 更偏向任务流自动化工具，而不是现代意义上的模块打包器。它适合处理文件流，例如复制、压缩、上传、替换内容等。

### through2

- [https://www.npmjs.com/package/through2](https://www.npmjs.com/package/through2)
- 示例源码：[gulp-aliyun-oss](https://github.com/yangblink/gulp-aliyun-oss/blob/master/index.js)

`through2` 常用于封装 Node.js Transform Stream，在 Gulp 插件中非常常见。

### Gulp 插件示例

```js
var stream = through2.obj(function (file, enc, cb) {
  var filename = file.relative
  var self = this

  var getFileKey = function () {
    return option.prefix
      + ((!option.prefix || option.prefix[option.prefix.length - 1] === '/') ? '' : '/')
      + path.posix.relative(file.base, file.path)
  }

  if (file.isBuffer()) {
    co(function* () {
      var result = yield client.put(getFileKey(), file.contents, ossOpt)
      log('OK:', colors.green(filename))
      cb(null, file)
    }).catch(function (err) {
      log('ERR:', colors.red(filename + '\t' + err.code))
      cb(err, null)
    })
  } else {
    cb()
  }
})
```

### 修改文件内容示例

```js
gulp.task('rewrite', () => {
  return gulp.src('./through/enter.txt')
    .pipe(through2.obj(function (chunk, enc, callback) {
      const { contents } = chunk

      for (var i = 0; i < contents.length; i++) {
        if (contents[i] === 97) {
          contents[i] = 122
        }
      }

      chunk.contents = contents
      this.push(chunk)
      callback()
    }))
    .pipe(gulp.dest('./dist'))
})
```

这个例子会把文件中的字符 `a` 转成字符 `z`。

### Transform Stream

`through2` 本质上是对 Node.js 原生 `Transform` 流的封装。`Transform` 是一种双工流，既可读也可写，但它的输入和输出有关联：写入的数据会经过 `_transform` 处理后再输出。

### through2 源码核心

`through2` 的源码很小，核心是一个工厂函数，用于统一处理参数并创建 Transform：

```js
function through2(construct) {
  return function (options, transform, flush) {
    if (typeof options === 'function') {
      flush = transform
      transform = options
      options = {}
    }

    if (typeof transform !== 'function') {
      transform = noop
    }

    if (typeof flush !== 'function') {
      flush = null
    }

    return construct(options, transform, flush)
  }
}
```

`DestroyableTransform` 则补充了 `destroy` 行为：

```js
DestroyableTransform.prototype.destroy = function (err) {
  if (this._destroyed) return
  this._destroyed = true

  var self = this

  process.nextTick(function () {
    if (err) {
      self.emit('error', err)
    }

    self.emit('close')
  })
}
```

### through2 / through2.obj / through2.ctor

- `through2`：创建普通 Transform 流；
- `through2.obj`：创建 object mode 的 Transform 流，写入内容不限于字符串或 Buffer；
- `through2.ctor`：返回可复用的 Transform 构造函数。

```js
const Tran = through.ctor(function (chunk, enc, callback) {
  console.log('transform', chunk.toString())
  callback(null, chunk)
})

const transform = new Tran()
```

## 选型建议

### 打包 NPM 工具库 / 组件库

优先考虑：

1. `tsdown`：新项目、追求速度、需要 ESM + CJS + d.ts；
2. `tsup`：成熟稳定、配置简单、已有项目；
3. `Rollup`：需要高度定制和最精细产物控制。

### 只做极速转译 / 压缩

优先考虑：

- `esbuild`：命令简单，速度极快；
- `SWC`：更偏编译器替代 Babel。

### 大型 Web 应用

优先考虑：

- `Vite` / `Rolldown` 生态；
- `Turbopack`：Next.js 场景。

### 文件流自动化

优先考虑：

- `Gulp`：处理文件复制、替换、上传、压缩等流水线任务。

## 总结

- 如果是现代 TypeScript 库，优先关注 `tsdown`；
- 如果已有 tsup 项目，短期继续使用 `tsup`，新项目可尝试迁移到 `tsdown`；
- 如果需要最强的库产物控制，使用 `Rollup`；
- 如果追求极致转译速度，使用 `esbuild` 或 `SWC`；
- 如果是 Next.js 应用构建，关注 `Turbopack`；
- 如果是任务流处理，`Gulp + through2` 仍然有价值。

## 相关阅读

构建工具演进：[Webpack]({% post_url 2017-12-12-webpack相关-myblog %}) → [Vite]({% post_url 2022-07-04-vite相关-myblog %}) → Bundler 打包器
