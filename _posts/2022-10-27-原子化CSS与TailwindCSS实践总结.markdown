---
layout:     post
title:      "原子化 CSS 与 Tailwind CSS 实践总结"
description: "围绕「原子化 CSS 与 Tailwind CSS 实践总结」梳理基础概念、关键属性、实践技巧和常见问题。"
date:       2022-10-27 18:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - CSS
---

> “Yeah It's on. ”

参考资料：

- [重新构想原子化 CSS](https://antfu.me/posts/reimagine-atomic-css-zh)
- [Tailwind CSS 官方文档](https://tailwindcss.com/docs)
- [Tailwind CSS v3 文档](https://v3.tailwindcss.com/docs)
- [Tailwind CSS v4 升级指南](https://tailwindcss.com/docs/upgrade-guide)

## 原子化 CSS

原子化 CSS（Atomic CSS，也常称 Utility-first CSS）的核心思想，是把样式拆成职责单一、可复用的工具类，再通过组合工具类完成页面样式。例如：

```html
<button class="rounded-lg bg-blue-600 px-4 py-2 font-medium text-white hover:bg-blue-700">
  保存
</button>
```

它并不是“完全不写 CSS”，而是把常用样式映射成一套受设计约束的声明式 API。Tailwind CSS、UnoCSS 都属于这一类方案。

### 适用场景

- 组件化应用、设计系统、后台管理系统等需要统一视觉规范的项目。
- 活动页、原型和中后台页面等迭代速度快、样式组合频繁的场景。
- 多人协作项目：通过统一的颜色、间距、字号和断点减少随意取值。
- 需要暗色模式、响应式布局和状态变体，又不想反复手写媒体查询及选择器的项目。

### 优势

- **样式就近维护**：减少在模板和样式文件之间来回跳转，也避免为简单样式反复命名。
- **约束设计系统**：颜色、间距、字号等由 design token 统一管理，页面更一致。
- **改动影响可控**：工具类作用于当前元素，减少全局 CSS 级联和选择器冲突。
- **产物体积稳定**：构建工具只生成源码中实际使用的工具类，CSS 不会随着页面数量线性膨胀。
- **状态表达直观**：`hover:`、`focus-visible:`、`dark:`、`md:` 等变体与样式写在一起。

### 取舍与边界

- class 较长时会影响可读性。重复出现的业务结构应封装为组件，而不是简单复制整串 class。
- 不要过早使用 `@apply` 把工具类重新包装成大量语义 class，否则容易退化回传统 CSS 的维护方式。
- 复杂动画、富文本排版、第三方组件覆盖以及复杂伪元素，仍适合使用普通 CSS。
- 工具类只能约束可选值，不能替代设计规范；颜色、间距和层级仍需统一维护。
- Tailwind 会以纯文本方式扫描类名，因此运行时拼接 `bg-${color}-500` 通常无法被识别，应使用完整类名映射。

```tsx
// 不推荐：扫描器看不到完整类名
const className = `bg-${color}-500`

// 推荐：静态列出完整类名
const colorClasses = {
  blue: "bg-blue-500 hover:bg-blue-600",
  red: "bg-red-500 hover:bg-red-600",
}
```

## Tailwind CSS

Tailwind CSS 是 utility-first CSS 框架。它提供预设工具类、响应式及状态变体，并在构建阶段根据源码生成实际使用的 CSS。Tailwind v3 与 v4 的使用体验相似，但底层引擎、安装方式、配置入口和浏览器要求存在明显差异。

### `tailwind-merge`

[`tailwind-merge`](https://www.npmjs.com/package/tailwind-merge) 用于合并 Tailwind class，并按 Tailwind 规则处理冲突，例如 `px-2 px-4` 最终保留 `px-4`。它通常与 `clsx` 搭配：

```tsx
import clsx, { type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]): string {
  return twMerge(clsx(inputs))
}

const className = cn(
  "absolute top-1/2 z-10 h-3 w-3 -translate-y-1/2 rounded-full bg-white",
  left ? "-left-1 -ml-0.5" : "-right-1 -mr-0.5",
)
```

### 暗色模式

参考 [Tailwind CSS Dark Mode](https://tailwindcss.com/docs/dark-mode)。具体的系统跟随和手动切换方式见后文“常用实践”。

### Just-in-Time Mode

[https://v2.tailwindcss.com/docs/just-in-time-mode](https://v2.tailwindcss.com/docs/just-in-time-mode)

Tailwind CSS v2.1 引入 JIT 编译器，按需生成工具类，避免提前生成全部 CSS。后续版本中 JIT 已经成为默认能力，新项目通常不需要再单独开启 `mode: 'jit'`。

### Tailwind CSS 3.x 与 4.x 的区别

Tailwind CSS v3.0 发布于 2021 年，v3.4 是 v3 系列的稳定版本；v4.0 于 2025 年 1 月发布，是一次底层重写。二者的工具类理念没有变化，但 v4 从“以 JavaScript 配置为中心”转向“以 CSS 和 Web 平台能力为中心”。

| 对比项 | Tailwind CSS 3.x | Tailwind CSS 4.x |
| --- | --- | --- |
| 构建引擎 | v3 内置 JIT 引擎 | 全新高性能引擎，增量构建明显更快 |
| 配置入口 | 默认使用 `tailwind.config.js` / `tailwind.config.ts` | CSS-first，主要使用 `@theme`、`@utility`、`@custom-variant` 等指令 |
| 引入方式 | `@tailwind base/components/utilities` | 一行 `@import "tailwindcss"` |
| 源文件扫描 | 在 `content` 中显式声明路径 | 默认自动检测源码；额外路径用 `@source` 声明 |
| PostCSS | `tailwindcss` 直接作为 PostCSS 插件，通常搭配 `autoprefixer` | 使用独立的 `@tailwindcss/postcss`；导入和前缀通常由框架处理 |
| Vite 集成 | 通过 PostCSS 使用 | 提供第一方 `@tailwindcss/vite` 插件 |
| CLI | CLI 位于 `tailwindcss` 包 | CLI 移至 `@tailwindcss/cli` 包 |
| 设计令牌 | `theme.extend` 中的 JavaScript 对象 | `@theme` 中的 CSS 变量，可直接被运行时和普通 CSS 使用 |
| 自定义工具类 | 常用插件 API 或 `@layer utilities` | 使用 `@utility` 注册，自动支持已有变体 |
| 插件与旧配置 | JavaScript 配置和插件是主要扩展方式 | 可用 `@config` / `@plugin` 兼容，但部分旧配置项不再支持 |
| CSS 能力 | 容器查询等能力曾需要插件 | 容器查询、3D transform、径向/锥形渐变等进入核心 |
| 浏览器要求 | 更适合需要兼容旧浏览器的项目 | Safari 16.4+、Chrome 111+、Firefox 128+ |
| 预处理器 | 可放入传统 Sass/Less/PostCSS 链路 | 官方不建议与 Sass、Less、Stylus 一起使用 |

#### 1. 安装和入口文件

v3 的典型写法：

```js
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,js,ts,jsx,tsx}"],
  theme: {
    extend: {
      colors: {
        brand: "#1677ff",
      },
    },
  },
  plugins: [],
}
```

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

v4 默认不再需要配置 `content`，入口 CSS 可以同时承担引入和主题配置：

```css
@import "tailwindcss";

@theme {
  --color-brand: #1677ff;
  --spacing-page: 1.5rem;
  --font-sans: Inter, system-ui, sans-serif;
}
```

定义后可直接使用 `bg-brand`、`p-page`、`font-sans`，也能在普通 CSS 或 JavaScript 中读取 `var(--color-brand)`。这让 Tailwind 的 token 更容易和组件库、图表及动画库共享。

如果 monorepo、外部组件库或被 `.gitignore` 忽略的位置没有被自动扫描，可以显式注册：

```css
@import "tailwindcss";
@source "../node_modules/@company/ui";
```

#### 2. 构建工具集成

v4 将不同构建入口拆成独立包：

```js
// postcss.config.mjs
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
}
```

Vite 项目则优先使用第一方插件：

```ts
import { defineConfig } from "vite"
import tailwindcss from "@tailwindcss/vite"

export default defineConfig({
  plugins: [tailwindcss()],
})
```

v4 已负责处理 CSS 导入和浏览器前缀，多数项目不再需要额外配置 `postcss-import` 与 `autoprefixer`。如果还有 Tailwind 之外的 CSS 兼容需求，再按项目目标浏览器决定是否加入其他 PostCSS 插件。

#### 3. CSS-first 扩展能力

v4 可以直接在 CSS 中注册工具类和变体：

```css
@utility content-auto {
  content-visibility: auto;
}

@custom-variant theme-midnight (&:where([data-theme="midnight"] *));
```

随后可以使用：

```html
<div class="content-auto hover:content-auto theme-midnight:bg-black"></div>
```

JavaScript 配置并非完全不能用，但不会再自动检测，需要显式引入：

```css
@config "../../tailwind.config.js";
@plugin "@tailwindcss/typography";
```

需要注意，v4 不再支持旧 JavaScript 配置中的 `corePlugins`、`safelist`、`separator`；静态安全列表可改用 `@source inline()`。

#### 4. 默认值和行为变化

升级时不能只改依赖版本，还要检查视觉差异：

- `border-*` 的默认颜色从 `gray-200` 改为 `currentColor`，建议显式写 `border-gray-200`。
- `ring` 从默认 3px、蓝色改为 1px、`currentColor`；保留旧效果可写 `ring-3 ring-blue-500`。
- `shadow`、`rounded`、`blur` 等命名尺度发生调整，例如 v3 的 `shadow-sm` 对应 v4 的 `shadow-xs`。
- `outline-none` 的含义发生变化；需要保留强制颜色模式下轮廓时应使用 `outline-hidden`。
- `space-*` 与 `divide-*` 的子元素选择器变化，复杂布局更推荐使用 Flex/Grid 的 `gap-*`。
- 堆叠变体由从右向左改为从左向右，`first:*:pt-0` 一类写法要重点检查。
- `hover:` 仅在主要输入设备支持悬停时生效，不应把关键交互只放在 hover 状态。
- `transform-none` 不再重置独立的 `scale`、`rotate`、`translate`，应使用对应的 `scale-none` 等工具类。
- Preflight 中按钮默认光标、placeholder 颜色、`dialog` 外边距和 `hidden` 属性优先级均有变化。

一些旧工具类也被移除或改名：

| v3 写法 | v4 写法 |
| --- | --- |
| `bg-opacity-50` | `bg-black/50`（颜色与透明度合并表达） |
| `text-opacity-50` | `text-black/50` |
| `flex-shrink-0` | `shrink-0` |
| `flex-grow` | `grow` |
| `overflow-ellipsis` | `text-ellipsis` |
| `bg-gradient-to-r` | `bg-linear-to-r` |
| `outline-none`（旧行为） | `outline-hidden` |
| `ring`（旧默认效果） | `ring-3 ring-blue-500` |

#### 5. 浏览器兼容性

v4 使用 `@property`、`color-mix()`、现代颜色空间等能力，官方最低目标为 Safari 16.4、Chrome 111 和 Firefox 128。如果需要覆盖更旧的浏览器或旧 WebView，应继续使用 v3.4，而不是依赖降级插件强行升级。

#### 6. 升级建议

1. 先确认目标浏览器满足 v4 要求，并在独立分支执行迁移。
2. Node.js 20+ 环境可使用官方升级工具 `npx @tailwindcss/upgrade` 处理多数机械改动。
3. 将入口从三个 `@tailwind` 指令改为 `@import "tailwindcss"`，同步调整 PostCSS、Vite 或 CLI 包。
4. 梳理 `theme.extend`、插件、自定义 utility、safelist 和动态类名，再逐步迁移到 CSS-first 配置。
5. 检查边框、ring、阴影、圆角、`space-*`、`divide-*`、hover 和 transform 等视觉变化。
6. 对核心页面做截图或视觉回归测试，不要把“构建成功”等同于“升级完成”。

选型上，新项目且只需支持现代浏览器时优先考虑 v4；需要兼容旧浏览器、依赖 Sass/Less 或大量旧插件的项目可以继续使用 v3.4。没有明确收益时，不必为了版本号立即重构成熟项目。

### PostCSS 与 `postcss-preset-env`

[postcss-preset-env](https://www.npmjs.com/package/postcss-preset-env) 用于把部分现代 CSS 语法转换为目标浏览器能够理解的形式，它和 Tailwind 解决的问题不同。

- **Tailwind v3**：常见链路是 `tailwindcss` + `autoprefixer`；只有项目在 Tailwind 之外使用了目标浏览器尚不支持的现代 CSS 时，才需要 `postcss-preset-env`。
- **Tailwind v4**：`@tailwindcss/postcss` 已处理 CSS 导入和前缀，通常不需要再加入 `postcss-import`、`autoprefixer`。v4 本身要求现代浏览器，`postcss-preset-env` 也无法完整降级 `@property`、`color-mix()` 等核心依赖。

v3 项目确实需要 `postcss-preset-env` 时，应避免和其他 nesting 插件重复转换嵌套规则：

```js
// postcss.config.js（Tailwind CSS v3）
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
    "postcss-preset-env": {
      stage: 1,
      features: {
        "nesting-rules": false,
      },
    },
  },
}
```

是否需要转换以及支持到什么程度，应以项目的 Browserslist 和真实运行环境为准。

### `@tailwind` 指令

下面是 Tailwind CSS v3 的入口写法：

```css
/* tailwind.css（v3） */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

构建时，这些指令会被替换为基础样式、组件样式和源码中实际使用的工具类。Tailwind CSS v4 已移除这三个入口指令，改用：

```css
/* app.css（v4） */
@import "tailwindcss";
```

### 常用实践

#### 条件类名与冲突合并

`clsx` 负责条件拼接，`tailwind-merge` 负责识别同组工具类冲突。它适合组件接收外部 `className` 的场景，但不是所有重复 class 都应该被删除；升级 Tailwind 大版本时，也要同步确认 `tailwind-merge` 版本是否支持对应语法。

```tsx
import clsx, { type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

#### `group-hover`

`group-hover` 用于根据父元素的 hover 状态改变后代样式。父元素标记为 `group`，后代使用 `group-hover:*`：

```html
<a class="group flex items-center gap-3 rounded-lg p-3 hover:bg-gray-100" href="#">
  <span class="h-3 w-3 rounded-full bg-blue-500 transition-opacity group-hover:opacity-50"></span>
  <span class="font-medium group-hover:text-blue-600">查看详情</span>
</a>
```

存在多层 group 时，可使用命名 group 避免状态串扰，例如父元素使用 `group/card`，后代使用 `group-hover/card:text-blue-600`。

#### 暗色模式

默认情况下，`dark:` 响应系统的 `prefers-color-scheme`：

```html
<div class="bg-white text-gray-900 dark:bg-gray-900 dark:text-white"></div>
```

v4 若需要通过 class 手动切换，可以自定义 `dark` 变体：

```css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
```

之后在 `<html>` 上添加或移除 `dark` class。为避免页面首次渲染闪烁，应在应用渲染前同步系统偏好和持久化的用户设置。

#### 何时抽取组件

不要仅因为 class 很长就立即使用 `@apply`。如果一组样式对应可复用的业务结构，优先抽取 React/Vue 组件；如果只是跨技术栈复用的简单 CSS 模式，才考虑 `@utility`、`@apply` 或普通 CSS。

```tsx
function Button({ className, ...props }: React.ComponentProps<"button">) {
  return (
    <button
      className={cn(
        "rounded-md bg-blue-600 px-4 py-2 font-medium text-white",
        "hover:bg-blue-700 focus-visible:outline-2 focus-visible:outline-offset-2",
        "disabled:cursor-not-allowed disabled:opacity-50",
        className,
      )}
      {...props}
    />
  )
}
```

## unocss

[https://github.com/unocss/unocss](https://github.com/unocss/unocss)

### **@unocss/inspector**

The inspector UI for UnoCSS. Ships with `unocss` and `@unocss/vite`.

@unocss/vite 应该已经自带这个了  （无需额外安装）

Visit `http://localhost:3000/__unocss` in your Vite dev server to see the inspector.

就可以看到效果了

## css in js

Ant Design 5.0放弃了跟随 antd 已久的 less 方案，转而拥抱 CSS-in-JS。

- 更小的 BundleSize；
- 不依赖任何插件的样式按需引入能力。

### Emotion

[Emotion](https://emotion.sh/docs/introduction)

Emotion is a library designed for writing css styles with JavaScript

####  react

```tsx
// .babelrc

{
  "presets": [
    [
      "@babel/preset-react",
      { "runtime": "automatic", "importSource": "@emotion/react" }
    ]
  ],
  "plugins": ["@emotion/babel-plugin"]
}
```

然后组件就可以使用css props了

```tsx
const AnotherComponent = () => 
<div css={{ textDecoration: 'underline' }}>Some text with an underline.</div>;
```

#### vite

[https://github.com/vitejs/vite-plugin-react/tree/main/packages/plugin-react](https://github.com/vitejs/vite-plugin-react/tree/main/packages/plugin-react)

```tsx
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react({
    jsxImportSource: '@emotion/react',
  })],
})
```
