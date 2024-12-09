---
layout:     post
title:      "原子化css"
date:       2022-10-13 18:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”



[重新构想原子化 CSS](https://antfu.me/posts/reimagine-atomic-css-zh)



# tailwindcss



## tailwind-merge

[https://www.npmjs.com/package/tailwind-merge](https://www.npmjs.com/package/tailwind-merge)

Utility function to efficiently merge [Tailwind CSS](https://tailwindcss.com/) classes in JS without style conflicts.



```tsx
import clsx from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]): string {
  return twMerge(clsx(inputs));
}

// test
className={cn(
  "absolute top-[50%] z-10 h-3 w-3 translate-y-[-50%] rounded-full bg-background",
  left ? "-left-[4px] -ml-0.5" : "-right-[4px] -mr-0.5",
)}
```





## dark-mode

[https://www.tailwindcss.cn/docs/dark-mode](https://www.tailwindcss.cn/docs/dark-mode)







## Just-in-Time Mode

[https://v2.tailwindcss.com/docs/just-in-time-mode](https://v2.tailwindcss.com/docs/just-in-time-mode)

Tailwind CSS v2.1 introduces a new just-in-time compiler for Tailwind CSS that generates your styles on-demand as you author your templates instead of generating everything in advance at initial build time.



## postcss-preset-env

[https://www.npmjs.com/package/postcss-preset-env](https://www.npmjs.com/package/postcss-preset-env)

**在使用 Tailwind CSS 时，通常不需要单独使用 `postcss-preset-env`。**

因为 Tailwind CSS 已经包含了许多现代 CSS 特性，并且它的构建工具已经处理了浏览器兼容性问题。然而，如果你在 Tailwind CSS 之外还使用了许多现代 CSS 特性，或者有特定的浏览器兼容性要求，`postcss-preset-env` 仍然可以帮助你确保所有 CSS 特性在这些浏览器中兼容。

如果一定要使用：

使用 Tailwind CSS 时，通常建议禁用 `postcss-preset-env` 中的 `nesting-rules` 功能

----

https://github.com/csstools/postcss-plugins/tree/main/plugins/postcss-nesting

Given they have no support they will always be enabled if they match by Stage:

鉴于它们没有支持，如果它们按 Stage 匹配，则它们将始终处于启用状态：

nesting-rules

```ts
module.exports = {
  plugins: {
    'postcss-import': {},  // 如果需要处理 @import 语句
    'tailwindcss': {},  // Tailwind CSS
    'autoprefixer': {},  // 自动添加浏览器前缀
    'postcss-preset-env': {
      stage: 1,  // 选择支持的阶段
      features: {
        'nesting-rules': false,  // 禁用 CSS 嵌套规则
      },
    },
  },
};
```



## @tailwind

```css
// tailwind.css

@tailwind base;
@tailwind components;
@tailwind utilities;
```

当你使用 Tailwind CSS 时，通常有一个构建工具（如 PostCSS）。在构建过程中，这些 `@tailwind` 指令会被处理并替换成对应的 CSS 规则。这样，你的最终 CSS 文件就包含了所需的所有样式，而不仅仅是你在 HTML 中显式使用的类。





## 补充



### group-hover

这个类用于控制元素在其父元素为 `group` 类时的样式。

举个例子：

```html
<div class="group">  
    <div class="bg-blue-500 h-16 w-16 group-hover:opacity-10 transition-opacity duration-300"></div>  
    <p>将鼠标悬停在这里</p>  
</div>  
```

`group-hover:opacity-10` 这个类用于控制元素在其父元素为 `group` 类时的透明度表现。具体而言，它的作用是当你将鼠标悬停在包含 `group` 类的父元素上时，对应的子元素的透明度将变为 10%。



# unocss

[https://github.com/unocss/unocss](https://github.com/unocss/unocss)





## **@unocss/inspector**

The inspector UI for UnoCSS. Ships with `unocss` and `@unocss/vite`.



@unocss/vite 应该已经自带这个了  （无需额外安装）

Visit `http://localhost:3000/__unocss` in your Vite dev server to see the inspector.

就可以看到效果了 





# css in js

Ant Design 5.0放弃了跟随 antd 已久的 less 方案，转而拥抱 CSS-in-JS。

- 更小的 BundleSize；
- 不依赖任何插件的样式按需引入能力。

## Emotion

[Emotion](https://emotion.sh/docs/introduction) 

Emotion is a library designed for writing css styles with JavaScript

###  react

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





### vite

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

