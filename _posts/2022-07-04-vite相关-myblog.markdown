---
layout:     post
title:      "英语学习相关"
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







# plugin 插件





# 问题



## import *.vue file without .vue will not work

[https://github.com/vitejs/vite/issues/178](https://github.com/vitejs/vite/issues/178)

This is by design. We will also stop supporting extension-less Vue imports in vue-cli in the next major.
