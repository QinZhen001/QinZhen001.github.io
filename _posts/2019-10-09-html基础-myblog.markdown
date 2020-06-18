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





## crossorigin属性 

[ https://developer.mozilla.org/zh-CN/docs/Web/HTML/CORS_settings_attributes ]( https://developer.mozilla.org/zh-CN/docs/Web/HTML/CORS_settings_attributes )



[ https://www.chrisyue.com/what-the-hell-is-crossorigin-attribute-in-html-script-tag.html ]( https://www.chrisyue.com/what-the-hell-is-crossorigin-attribute-in-html-script-tag.html )



在HTML5中，一些 HTML 元素提供了对 [CORS](https://developer.mozilla.org/en-US/docs/HTTP/Access_control_CORS) 的支持， 例如`<audio> <img> <link> <script> <video>`均有一个跨域属性 (`crossOrigin` property)，它允许你配置元素获取数据的 CORS 请求。 



| 关键字            | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `anonymous`       | 对此元素的 CORS 请求将不设置凭据标志。                       |
| `use-credentials` | 对此元素的CORS请求将设置凭证标志；这意味着请求将提供凭据。   |
| `""`              | 设置一个空的值，如 `crossorigin` 或 `crossorigin=""`，和设置 `anonymous` 的效果一样。 |







从谷歌的结果来看，比较一致的说法是，当引入跨域的脚本（比如用了 apis.google.com 上的库文件）时，如果这个脚本有错误，因为浏览器的限制（根本原因是协议的规定），是拿不到错误信息的。当本地尝试使用 `window.onerror` 去记录脚本的错误时，跨域脚本的错误只会返回 `Script error`。

 



而 HTML5 新的规定，是可以允许本地获取到跨域脚本的错误信息的，但有两个条件：一是跨域脚本的服务器必须通过 `Access-Control-Allow-Origin` 头信息允许当前域名可以获取错误信息，二是网页里的 `script` 标签也必须指明 `src` 属性指定的地址是支持跨域的地址，也就是 crossorigin 属性。有了这两个条件，就可以获取跨域脚本的错误信息：


 但事情还是不够明朗，看起来跨域脚本报个错也没什么啊，为什么浏览器（准确说是 HTTP 协议）这么轴，非要规定默认情况页面是不能获取跨域脚本错误信息的呢？



这其实跟网络安全有关，不妨举一个例子来说明。



我们先假设浏览器默认可以将跨域脚本的错误信息返回。



这个时候我在我的博客里写下如下代码：



```htmls
<script src="http://某个银行/会员信息网址">
<script src="http://某个银行2/会员信息网址">
...
```





注意 src 里面提到地址，都是 HTML 页面的地址，当成 JS 来执行，肯定是会报错的。



因为我们假设浏览器能报具体错误，这个错误可能是类似于：



>  “请登录” is undefined.
>  “您好” is undefined. 





我们通过报错信息的不一致，可能可以推断出当前访问我博客的会员在某某银行是否有账号。虽然不是什么大问题，但隐私的确是泄漏了，如果我是攻击者我可能会通过判断会员在某家银行是否有账号，『精准』推送相关的钓鱼网站给他。





说清楚了来龙去脉，我们就可以更好的判断，我们是否真的需要给 `script` 标签加上 crossorigin 属性了。另外除了 `script`，所有能引入跨域资源的标签包括 `link` 和 `img` 之类，都有一样的属性。





> `script` 的 crossorigin 和其他标签的 `crossorigin` 属性作用不一样，`script` 的跨域属性跟脚本错误有关，上面已经解释过了，但其他标签的跨域属性跟 Canvas 有关 



另外补充一点：





 加载本地静态文件根本就不牵涉跨域的问题，所以本地就不应该用  crossorigin  



> 印象中如果 crossorigin 用在了本地文件上，浏览器报错信息会告诉你 crossorigin 只能支持 http/https/… 等协议上 





##  integrity 



```html
<script src="https://code.jquery.com/jquery-3.2.1.slim.min.js"
    integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN"
    crossorigin="anonymous">
</script>
```





CDN 虽好，但 CDN 有可能被劫持，导致下载的文件是被篡改过的（比如通过 DNS 劫持），有了 integrity 就可以检查文件是否是原版。但因为本地文件用的域名跟网页是同一个域名，不存在劫持的问题（或者劫持就连网站本身一起被劫持了，那就不是 integrity 能解决的问题了），**所以本地静态文件没有太大必要用这个属性**。









