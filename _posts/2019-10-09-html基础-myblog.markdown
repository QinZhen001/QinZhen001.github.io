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







##  **Range** 



[ https://developer.mozilla.org/zh-CN/docs/Web/API/Range ]( https://developer.mozilla.org/zh-CN/docs/Web/API/Range )



 Range对象代表页面上一段连续的区域，通过Range对象可以获取或者修改页面上任何区域的内容。也可以通过Range的方法进行复制和移动页面任何区域的元素。 



**`Range`** 接口表示一个包含节点与文本节点的一部分的文档片段。





可以用 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 对象的 [`Document.createRange`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/createRange) 方法创建 Range，也可以用 [`Selection`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection) 对象的 [`getRangeAt`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection/getRangeAt) 方法获取 Range。另外，还可以通过 [`Document`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document) 对象的构造函数 [`Range()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Range/Range) 来得到 Range。





---



### 属性



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







### 例子





```html
<body>
  <table id="mytable" border="1">
    <tr>
      <td>内容1</td>
      <td>内容2</td>
    </tr>
    <tr>
      <td>内容3</td>
      <td>内容4</td>
    </tr>
  </table>
  <button onclick="delrow()">删除第一行</button>
</body>

```



```html
<script>
  function delrow() {
    var table = document.getElementById("mytable");
    if (table.rows.length > 0) {
      var row = table.rows[0];
      var rangeObj = document.createRange();
      rangeObj.setStartBefore(row);
      rangeObj.setEndAfter(row);
      rangeObj.deleteContents();
    }
  }
</script>
```







### 兼容性



 https://www.caniuse.com/#search=createRange 



 createRange 兼容性非常好，可以使用



## Selection

[ https://developer.mozilla.org/zh-CN/docs/Web/API/Selection ]( https://developer.mozilla.org/zh-CN/docs/Web/API/Selection )





 表示用户选择的文本范围或光标的当前位置。 



```js
const selection = window.getSelection() ;
```

- `selection` 是一个 [`Selection`](https://developer.mozilla.org/zh-CN/docs/Web/API/Selection) 对象。 如果想要将 `selection` 转换为字符串，可通过连接一个空字符串（""）或使用 [`String.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/toString) 方法。





 一般来说，插入光标的位置可通过 Selection 获取，这时它被标记为 `Collapsed`，这表示选区被压缩至一点，即光标位置。但要注意它与 `focus` 事件或 [`Document.activeElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/activeElement) 等的值没有必然联系。 







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





## audio标签



### 切换url播放

```html
  <audio id="Audio" src="https://demo.dj63.com//2016/串烧舞曲/20150926/全中文CLUB音乐情一动心就痛情感慢摇串烧.mp3" controls>11111</audio>
  <button onclick="changeUrl()">切换audio url</button>
```



```js
  const audio = document.getElementById("Audio")
  const url2 = "https://demo.dj63.com//2016/%E4%B8%B2%E7%83%A7%E8%88%9E%E6%9B%B2/20161123/[%E9%A3%8E%E5%90%B9%E9%BA%A6%E6%B5%AA]%E6%89%93%E9%80%A0%E4%B8%AD%E8%8B%B1%E6%96%87%E6%97%8B%E5%BE%8B%E5%BC%B9%E8%B7%B3CLUB%E8%88%9E%E6%9B%B2%E4%B8%B2%E7%83%A7.mp3"

  function changeUrl(){
    audio.src = url2
    // 记得调用play()  
    audio.play()	
  }
```





## Attributes and properties

[https://javascript.info/dom-attributes-and-properties](https://javascript.info/dom-attributes-and-properties)



总结

- **Attributes – is what’s written in HTML.**
- **Properties – is what’s in DOM objects.**



|      | Properties                                                   | Attributes                 |
| :--- | :----------------------------------------------------------- | :------------------------- |
| Type | Any value, standard properties have types described in the spec | A string                   |
| Name | Name is case-sensitive                                       | Name is not case-sensitive |

For most situations using DOM properties is preferable. We should refer to attributes only when DOM properties do not suit us, when we need exactly attributes, for instance:

- We need a non-standard attribute. But if it starts with `data-`, then we should use `dataset`.
- We want to read the value “as written” in HTML. The value of the DOM property may be different, for instance the `href` property is always a full URL, and we may want to get the “original” value.





----



When the browser loads the page, it “reads” (another word: “parses”) the HTML and generates DOM objects from it. For element nodes, most standard HTML attributes automatically become properties of DOM objects.



For instance, if the tag is `<body id="page">`, then the DOM object has `body.id="page"`.



But the attribute-property mapping is not one-to-one









---



[DOM properties](https://javascript.info/dom-attributes-and-properties#dom-properties)

We’ve already seen built-in DOM properties. There are a lot. But technically no one limits us, and if there aren’t enough, we can add our own.

DOM nodes are regular JavaScript objects. We can alter them.



For instance, let’s create a new property in `document.body`:



```js
document.body.myData = {
  name: 'Caesar',
  title: 'Imperator'
};

alert(document.body.myData.title); // Imperator
```

We can add a method as well:

```js
document.body.sayTagName = function() {
  alert(this.tagName);
};

document.body.sayTagName(); // BODY (the value of "this" in the method is document.body)

```

We can also modify built-in prototypes like `Element.prototype` and add new methods to all elements:

```js
Element.prototype.sayHi = function() {
  alert(`Hello, I'm ${this.tagName}`);
};

document.documentElement.sayHi(); // Hello, I'm HTML
document.body.sayHi(); // Hello, I'm BODY
```

So, DOM properties and methods behave just like those of regular JavaScript objects:

- They can have any value.
- They are case-sensitive (write `elem.nodeType`, not `elem.NoDeTyPe`).



----

[HTML attributes](https://javascript.info/dom-attributes-and-properties#html-attributes)



In HTML, tags may have attributes. When the browser parses the HTML to create DOM objects for tags, it recognizes *standard* attributes and creates DOM properties from them.



在HTML中，标签可以有属性。当浏览器解析HTML以创建标记的DOM对象时，它识别标准属性并从中创建DOM属性。



So when an element has `id` or another *standard* attribute, the corresponding property gets created. But that doesn’t happen if the attribute is non-standard.s



因此，当一个元素具有id或另一个标准属性时，相应的属性将被创建。但是如果属性是非标准的，就不会发生这种情况。



For instance:

```html
<body id="test" something="non-standard">
  <script>
    alert(document.body.id); // test
    // non-standard attribute does not yield a property
    alert(document.body.something); // undefined
  </script>
</body>
```

Please note that a standard attribute for one element can be unknown for another one. For instance, `"type"` is standard for `<input>` ([HTMLInputElement](https://html.spec.whatwg.org/#htmlinputelement)), but not for `<body>` ([HTMLBodyElement](https://html.spec.whatwg.org/#htmlbodyelement)). Standard attributes are described in the specification for the corresponding element class.



Here we can see it:

```html
<body id="body" type="...">
  <input id="input" type="text">
  <script>
    alert(input.type); // text
    alert(body.type); // undefined: DOM property not created, because it's non-standard
  </script>
</body>
```

So, if an attribute is non-standard, there won’t be a DOM-property for it. Is there a way to access such attributes?



Sure. All attributes are accessible by using the following methods:

- `elem.hasAttribute(name)` – checks for existence.
- `elem.getAttribute(name)` – gets the value.
- `elem.setAttribute(name, value)` – sets the value.
- `elem.removeAttribute(name)` – removes the attribute.

These methods operate exactly with what’s written in HTML.



Also one can read all attributes using `elem.attributes`: a collection of objects that belong to a built-in [Attr](https://dom.spec.whatwg.org/#attr) class, with `name` and `value` properties.



Here’s a demo of reading a non-standard property:

```html
<body something="non-standard">
  <script>
    alert(document.body.getAttribute('something')); // non-standard
  </script>
</body>

```



HTML attributes have the following features:

- Their name is case-insensitive (`id` is same as `ID`).    // 不区分大小写的
- Their values are always strings.



Here’s an extended demo of working with attributes:

```html
<body>
  <div id="elem" about="Elephant"></div>

  <script>
    alert( elem.getAttribute('About') ); // (1) 'Elephant', reading

    elem.setAttribute('Test', 123); // (2), writing

    alert( elem.outerHTML ); // (3), see if the attribute is in HTML (yes)

    for (let attr of elem.attributes) { // (4) list all
      alert( `${attr.name} = ${attr.value}` );
    }
  </script>
</body>
```

Please note:

1. `getAttribute('About')` – the first letter is uppercase here, and in HTML it’s all lowercase. But that doesn’t matter: attribute names are case-insensitive.
2. We can assign anything to an attribute, but it becomes a string. So here we have `"123"` as the value.
3. All attributes including ones that we set are visible in `outerHTML`.
4. The `attributes` collection is iterable and has all the attributes of the element (standard and non-standard) as objects with `name` and `value` properties.



---

[Property-attribute synchronization](https://javascript.info/dom-attributes-and-properties#property-attribute-synchronization)



When a standard attribute changes, the corresponding property is auto-updated, and (with some exceptions) vice versa.

In the example below `id` is modified as an attribute, and we can see the property changed too. And then the same backwards:



```html
<input>

<script>
  let input = document.querySelector('input');

  // attribute => property
  input.setAttribute('id', 'id');
  alert(input.id); // id (updated)

  // property => attribute
  input.id = 'newId';
  alert(input.getAttribute('id')); // newId (updated)
</script>

```

But there are exclusions, for instance `input.value` synchronizes only from attribute → to property, but not back:

```html
<input>

<script>
  let input = document.querySelector('input');

  // attribute => property
  input.setAttribute('value', 'text');
  alert(input.value); // text

  // NOT property => attribute
  input.value = 'newValue';
  alert(input.getAttribute('value')); // text (not updated!)
</script>
```

In the example above:

- Changing the attribute `value` updates the property.
- But the property change does not affect the attribute.



That “feature” may actually come in handy, because the user actions may lead to `value` changes, and then after them, if we want to recover the “original” value from HTML, it’s in the attribute.

----

DOM properties are not always strings. For instance, the `input.checked` property (for checkboxes) is a boolean:



```html
<input id="input" type="checkbox" checked> checkbox

<script>
  alert(input.getAttribute('checked')); // the attribute value is: empty string
  alert(input.checked); // the property value is: true
</script>

```

There are other examples. The `style` attribute is a string, but the `style` property is an object:



```html
<div id="div" style="color:red;font-size:120%">Hello</div>

<script>
  // string
  alert(div.getAttribute('style')); // color:red;font-size:120%

  // object
  alert(div.style); // [object CSSStyleDeclaration]
  alert(div.style.color); // red
</script>

```

Most properties are strings though.

---



**All attributes starting with “data-” are reserved for programmers’ use. They are available in the `dataset` property.**



Multiword attributes like `data-order-state` become camel-cased: `dataset.orderState`.



Here’s a rewritten “order state” example:



```html
<style>
  .order[data-order-state="new"] {
    color: green;
  }

  .order[data-order-state="pending"] {
    color: blue;
  }

  .order[data-order-state="canceled"] {
    color: red;
  }
</style>

<div id="order" class="order" data-order-state="new">
  A new order.
</div>

<script>
  // read
  alert(order.dataset.orderState); // new

  // modify
  order.dataset.orderState = "pending"; // (*)
</script>
```

Using `data-*` attributes is a valid, safe way to pass custom data.



## activeElement

当前获得焦点的元素:

```js
var x = document.activeElement.tagName;
// BUTTON
```

一个别的例子：

```js
document.activeElement.blur()
```



## attribute中的dir 

**dir**是一个指示元素中文本方向的枚举属性。它的取值如下：

- ltr, 指从左到右，用于那种从左向右书写的语言（比如英语）；

- rtl, 指从右到左，用于那种从右向左书写的语言（比如阿拉伯语）；

- auto, 指由用户代理决定方向。它在解析元素中字符时会运用一个基本算法，直到发现一个具有强方向性的字符，然后将这一方向应用于整个元素。



```js
const isRTL = document.documentElement.getAttribute('dir') == 'rtl'
fakeElem.style[ isRTL ? 'right' : 'left' ] = '-9999px'
```



