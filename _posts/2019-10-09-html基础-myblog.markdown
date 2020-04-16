---
layout:     post
title:      "html基础"
date:       2019-10-09 17:33:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Html
---

> “Yeah It's on. ”


# 正文



## MutationObserver

[https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)


MutationObserver接口提供了监视对DOM树所做更改的能力。它被设计为旧的Mutation Events功能的替代品，该功能是DOM3 Events规范的一部分。







## document.currentScript



>在学习vue-cli3时发现了currentScript这个知识点


>在库模式中，项目的 publicPath 是根据主文件的加载路径动态设置的（用以支持动态的资源加载能力）。但是这个功能用到了 document.currentScript，而 IE 浏览器并不支持这一特性。所以如果网站需要支持 IE 的话，建议使用库之前先在页面上引入 current-script-polyfill。



>https://cli.vuejs.org/zh/guide/build-targets.html#%E5%BA%94%E7%94%A8







----------------


[https://developer.mozilla.org/zh-CN/docs/Web/API/Document/currentScript](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/currentScript)



返回其所包含的脚本中正在被执行的 `<script>` 元素.

```javascript
var curScriptElement = document.currentScript;
```


下例演示了如何检测当前正在执行脚本的 <script> 元素是否是以异步模式执行的.

```javascript
if (document.currentScript.async) {
  console.log("Executing asynchronously");
} else {
  console.log("Executing synchronously");
}
```







### Selection

[ https://developer.mozilla.org/zh-CN/docs/Web/API/Selection ]( https://developer.mozilla.org/zh-CN/docs/Web/API/Selection )





 表示用户选择的文本范围或光标的当前位置。 



```js
const selection = window.getSelection() ;
```

- `selection` 是一个 [`Selection`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection) 对象。 如果想要将 `selection` 转换为字符串，可通过连接一个空字符串（""）或使用 [`String.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/toString) 方法。





 一般来说，插入光标的位置可通过 Selection 获取，这时它被标记为 `Collapsed`，这表示选区被压缩至一点，即光标位置。但要注意它与 `focus` 事件或 [`Document.activeElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/activeElement) 等的值没有必然联系。 







###  **Range** 



[ https://developer.mozilla.org/zh-CN/docs/Web/API/Range ]( https://developer.mozilla.org/zh-CN/docs/Web/API/Range )







**`Range`** 接口表示一个包含节点与文本节点的一部分的文档片段。





可以用 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 对象的 [`Document.createRange`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createRange) 方法创建 Range，也可以用 [`Selection`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection) 对象的 [`getRangeAt`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection/getRangeAt) 方法获取 Range。另外，还可以通过 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 对象的构造函数 [`Range()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/Range) 来得到 Range。





---



属性：



[`Range.collapsed`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/collapsed) 只读

返回一个表示 `Range` 的起始位置和终止位置是否相同的[`布尔值`](https://developer.mozilla.org/zh-CN/docs/Web/API/Boolean)。

[`Range.commonAncestorContainer`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/commonAncestorContainer) 只读

返回完整包含 `startContainer` 和 `endContainer` 的、最深一级的[`节点`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)。

[`Range.endContainer`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/endContainer) 只读

返回包含 `Range` 终点的[`节点`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)。

[`Range.endOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/endOffset) 只读

返回一个表示 `Range` 终点在 `endContainer` 中的位置的数字。

[`Range.startContainer`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/startContainer) 只读

返回包含 `Range` 开始的[`节点`](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)。

[`Range.startOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/startOffset) 只读

返回一个表示 `Range` 起点在 `startContainer` 中的位置的数字。













