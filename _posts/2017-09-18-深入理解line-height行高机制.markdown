---
layout:     post
title:      "深入理解 line-height、vertical-align 与内联布局机制"
description: "系统梳理 line-height 行高机制、vertical-align 对齐机制、幽灵空白节点以及内联元素的垂直居中与间隙问题。"
date:       2017-09-18 12:48:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - CSS
---

> “Yeah It's on. ”

## 正文
[网页链接](http://www.w3school.com.cn/cssref/pr_dim_line-height.asp)

[CSS:line-height:150%与line-height:1.5的真正区别是什么？]( https://www.zhihu.com/question/20394889 )

[http://www.zhangxinxu.com/wordpress/](http://www.zhangxinxu.com/wordpress/2009/11/css%e8%a1%8c%e9%ab%98line-height%e7%9a%84%e4%b8%80%e4%ba%9b%e6%b7%b1%e5%85%a5%e7%90%86%e8%a7%a3%e5%8f%8a%e5%ba%94%e7%94%a8/)

**“行高”顾名思意指一行文字的高度。具体来说是指两行文字间基线之间的距离**

>该属性会影响行框的布局。在应用到一个块级元素时，它定义了该元素中基线之间的最小距离而不是最大距离。

现代布局中，很多垂直居中场景已经可以优先使用 Flex/Grid，例如 `align-items: center`、`place-items: center`。但 `line-height`、`vertical-align`、基线、幽灵空白节点仍然很重要，因为它们决定了文字、图标、inline-block、图片和表单控件在同一行内的对齐效果。

实践建议：

- 正文排版优先使用无单位行高，例如 `line-height: 1.5`，避免子元素继承固定计算值。
- 单行文本垂直居中可以使用 `line-height = height`，但按钮、图标混排更推荐 Flex。
- 图片底部间隙通常来自基线对齐，可用 `display: block` 或 `vertical-align: middle/bottom` 处理。
- 图标字体和文本对齐时，要关注字体度量、`line-height` 和 `vertical-align` 的共同影响。

先说一个大家都熟知的现象，有一个空的div，`<div></div>`，如果没有设置至少大于1像素高度height值时，该div的高度就是个0。如果该div里面打入了一个空格或是文字，则此div就会有一个高度。那么您思考过没有，为什么div里面有文字后就会有高度呢？

这是个看上去很简单的问题，是理解line-height非常重要的一个问题。可能有人会跟认为是：文字撑开的！文字占据空间，自然将div撑开。我一开始也是这样理解的，但是事实上，深入理解inline模型后，我发现，**根本不是文字撑开了div的高度，而是line-height**！

**不是文字撑开了div的高度，而是line-height**

**不是文字撑开了div的高度，而是line-height**

**不是文字撑开了div的高度，而是line-height**

line-height 与 font-size 的计算值之差（在 CSS 中成为“行间距”）分为两半，分别加到一个文本行内容的顶部和底部。可以包含这些内容的最小框就是行框。

原始数字值指定了一个缩放因子，后代元素会继承这个缩放因子而不是计算值。

### 可能的值

| normal  | 默认。设置合理的行间距。                                     |
| ------- | ------------------------------------------------------------ |
| number  | 设置数字，此数字会与当前的字体尺寸相乘来设置行间距。   **（子元素仅继承该系数）** |
| length  | 设置固定的行间距。                                           |
| %       | 基于当前字体尺寸的百分比行间距。  **(子元素继承了父元素计算得出的行距)** |
| inherit |                                                              |

### 行高的垂直居中性

line-height的最终表现是通过line boxes实现的，而无论line boxes所占据的高度是多少（无论比文字大还是比文字小），其占据的空间都是与文字内容公用水平中垂线的。

**这一重要的特性可以用来实现文字或图片的垂直居中对齐。**

#### 单行文字的垂直居中对齐
line-height值设置为height一样大小的值可以实现单行文字的垂直居中

#### 多行文字的垂直居中

line-height 属性设置行间的距离（行高）。
该属性会影响行框的布局。在应用到一个块级元素时，它定义了该元素中基线之间的最小距离而不是最大距离。

line-height 与 font-size 的计算值之差（在 CSS 中成为“行间距”）分为两半，分别加到一个文本行内容的顶部和底部。可以包含这些内容的最小框就是行框。

 **line-height为相对单位，font-size间接控制**

### 深入理解line-height

[http://www.zhangxinxu.com/wordpress/2009/11/css%E8%A1%8C%E9%AB%98line-height%E7%9A%84%E4%B8%80%E4%BA%9B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E5%8F%8A%E5%BA%94%E7%94%A8/](http://www.zhangxinxu.com/wordpress/2009/11/css%E8%A1%8C%E9%AB%98line-height%E7%9A%84%E4%B8%80%E4%BA%9B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E5%8F%8A%E5%BA%94%E7%94%A8/)

行高还有一个特性，叫做垂直居中性。line-height的最终表现是通过line boxes实现的，而无论line boxes所占据的高度是多少（无论比文字大还是比文字小），其占据的空间都是与文字内容公用水平中垂线的。

![enter description here][1]

看test1的结果，此时line boxes的高度为0，但是它是以文字的水平中垂线对称分布的。这一重要的特性可以用来实现文字或图片的垂直居中对齐。

----------

![enter description here][2]

### line-height属性的细节
**有单位（包括百分比）**与无单位之间的区别

**有单位时，子元素继承了父元素计算得出的行距；无单位时继承了系数，子元素会分别计算各自行距（推荐使用）。**

>计算方式:子元素font-size*line-height数字

### line-height设置为数值与百分比
[http://demo.cssworld.cn/5/2-5.php](http://demo.cssworld.cn/5/2-5.php)

line-height:1.5、line-height:150%和line-height:1.5em这3种 用法是一模一样的，终的行高大小都是和font-size计算值，但是，实际上，line-height:1.5 和另外两个有一点儿不同，那就是继承细节有所差别。**如果使用数值作为line-height的属性值， 那么所有的子元素继承的都是这个值**；但是，如果使用百分比值或者长度值作为属性值，那么所有 的子元素继承的是终的计算值。

### 内联元素line-height的大值特性
[http://demo.cssworld.cn/5/2-6.php](http://demo.cssworld.cn/5/2-6.php)

**无论内联元素line-height如何设置，终父级元素的高度都是由数值大的 那个line-height决定的**

## 补充

### boxes

[深入了解css的行高Line Height属性](http://www.cnblogs.com/fengzheng126/archive/2012/05/18/2507632.html)

1. containing boxes
2. inline boxes(不会让内容显示成块形式，而是排成一行)
3. line boxes(inline boxes 在containing box里一个接一个 组成了line boxes)
4. content area(是围绕着文字的看不见的一种box 它的高度取决于font-size)

半行间距会被应用在content area的顶部和底部

content box包裹着inline box，而半行间距位于content box的上部和下部

如果line-height小于font-size,inline box会优先于行高

###  vertical-align和line-height 的关系

[ https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/ ]( https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/ )

这个非常重要，关键联系在于：**百分比值**

 `vertical-align`的百分比值不是相对于字体大小或者其他什么属性计算的，而是相对于`line-height`计算的。

举个例子：

```css
{
  line-height: 30px;
  vertical-align: -10%;
}
```

实际上，等同于：

```css
{
  line-height: 30px;
  vertical-align: -3px;    /* = 30px * -10% */  
}
```

### vertical-align: middle完全居中元素

[ https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/ ]( https://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/ )

vertical-align: middle的效果并不是完全的垂直居中， 只是近似

 为什么只是近似呢？那是因为「幽灵空白节点」高度行高撑开，其垂直中心是字符content area的中心，而对于字符`x`而言，都是比绝对中心位置要下沉的（不同字体下沉幅度不一样），换句更易懂的描述就是`x`的中心位置都是在字符内容区域高度中心点的下方

换句更简单的话说就是：middle中线位置(字符`x`的中心)并不是字符内容的绝对居中位置。两个位置的偏差就是图片近似居中的偏差。

 想完全垂直居中

 最先想到的方法就是让后面的“幽灵字符”也是`vertical-align:middle` 。 既然称之为“幽灵”就表示不会受非继承特性的属性影响，所以，根本没法设置`vertical-align:middle`，除非你自己创建一个显示的内联元素。

我们就没有办法了吗？当然不是，“幽灵字符”可以受具有继承特性的CSS属性影响，于是，我们可以通过其他东西来做调整，让字符的中线和字符内容中心线在一起，或者说在一个位置上就可以了。有人可能要疑问了，这能行吗？啊，是可以的。

怎么搞？很简单，`font-size:0`, 因此此时content area高度是0，各种乱七八糟的线都在高度为0的这条线上，绝对中心线和中线重合。自然全垂直居中：

```css
// div为父元素
div { line-height: 240px; font-size: 0; }
// img是需要垂直居中的元素
img { vertical-align: middle; }
```

**还有一个非常重要的点：  `vertical-align`对块状水平的元素无感**

### height:100%不起作用？

[https://segmentfault.com/a/1190000012707337](https://segmentfault.com/a/1190000012707337)

Web浏览器在计算有效宽度时会考虑浏览器窗口的打开宽度。如果你不给宽度设定任何缺省值，那浏览器会自动将页面内容平铺填满整个横向宽度。即我们不设置宽，会自动填满整个横向宽度

但是高度的计算方式完全不一样。事实上，浏览器根本就不计算内容的高度，除非内容超出了视窗范围(导致滚动条出现)。或者你给整个页面设置一个绝对高度。否则，浏览器就会简单的让内容往下堆砌，页面的高度根本就无需考虑。

**因为页面并没有缺省的高度值，所以，当你让一个元素的高度设定为百分比高度时，无法根据获取父元素的高度，也就无法计算自己的高度。**

即父元素的高度只是一个缺省值：height: auto;我们设置height：100%时，是要求浏览器根据这样一个缺省值来计算百分比高度时，只能得到undefined的结果。也就是一个null值，浏览器不会对这个值有任何的反应。

#### 如何解决

现在你知道了吧，line-height,设置%是一个相对父元素计算得来的高度，要想使他有效，我们需要设置父元素的height;

```jsx
   <style>
        html,body{
            height: 100%;
            margin: 0;
            padding: 0;
        }
        div {
            color: white;
            text-align: center;
            font-size: 30px;
            line-height: 100%;
            background-color: blueviolet;
        }
    </style>
</head>

<body>
    <!-- <div style="width:100%;height:100%;">width:100%;height:100%;</div> -->
    <div style="height:100%;">此文字无法垂直居中</div>
    <!-- <div style="width:100%;height:200px;">width:100%;height:200px;</div> -->
</body>
```

**可以看到设置了line-height为100%没有居中，这是为什么呢，因为这时候的%是相对于字体尺寸的？所以直接作用于没有绝对高度的元素是不行的。**

[1]: http://image.zhangxinxu.com/image/blog/200911/2009-11-28_002310.png
[2]: http://img.mukewang.com/57899eda0001994b12800720.jpg

## vertical-align 对齐机制

vertical-align “垂直的”+“对齐”的意思，可取值：

* 长度：通过距离升高（正值）或降低（负值）元素。`0cm` 等同于 `baseline`
* 百分值 `%`：通过距离（相对于 `line-height` 值的百分比）升高或降低元素。`0%` 等同于 `baseline`
* `baseline`：默认。元素的基线与父元素的基线对齐。
* `sub` / `super`：降低/升高元素基线到父元素合适的下标/上标位置。
* `top`：把对齐的子元素顶端与 line box 顶端对齐。
* `text-top`：把元素顶端与父元素内容区域顶端对齐。
* **`middle`：元素的中垂点与「父元素的基线加 1/2 父元素中字母 x 的高度」对齐。**（一般用作垂直居中）
* `bottom` / `text-bottom`：把子元素底端与 line box / 父元素内容区域底端对齐。

**vertical-align 属性的百分比值是相对于 line-height 的计算值计算的。**

### 为什么 vertical-align 不起作用

vertical-align 称之为“inline-block 依赖型元素”。

**只有一个元素属于 inline 或 inline-block（table-cell 也可理解为 inline-block 水平）水平，其身上的 vertical-align 属性才会起作用。** 所以 `div{vertical-align:middle;}` 这样的代码不会生效。

inline 水平元素受 vertical-align 而位置改变，不是因为它对 vertical-align 敏感，而是受制于整个 line box 的变化而不得不变化。

### vertical-align: middle 完全居中

vertical-align:middle 的效果并不是完全的垂直居中，只是近似。因为「幽灵空白节点」的垂直中心是字符 content area 的中心，而字符 `x` 的中心比绝对中心位置下沉。

想完全垂直居中，可以设置 `font-size:0`，此时 content area 高度为 0，绝对中心线和中线重合：

```css
/* div 为父元素 */
div { line-height: 240px; font-size: 0; }
/* img 是需要垂直居中的元素 */
img { vertical-align: middle; }
```

**注意：`vertical-align` 对块状水平的元素无感。**

### display:table-cell 无视行高

对 table-cell 元素而言，vertical-align 起作用的是 table-cell 元素自身。将 `vertical-align:middle` 设置在 table-cell 元素上，其子元素（即使是块级元素）就可以垂直居中：

```css
.cell {
  height: 128px;
  display: table-cell;
  vertical-align: middle;
}
.cell > img { height: 96px; }
```

### vertical-align 与 line-height 的关系

vertical-align 和 line-height 之间是“朋友”关系，只要出现内联元素，这对好朋友一定会同时出现。

容器高度不等于行高的例子（[demo](http://demo.cssworld.cn/5/3-1.php)）：

```css
.box { line-height: 32px; }
.box > span { font-size: 24px; }
```

```html
<div class="box">
  x<span>文字x</span>
</div>
```

`<span>` 前有一个看不见的“幽灵空白节点”。对字符而言，font-size 越大字符的基线位置越往下，因为文字默认基线对齐，当字号大小不一的两个文字在一起时会发生上下位移，超过行高限制就会导致意料之外的高度（如 36px 而非 32px）。

解决方法：让“幽灵空白节点”和 `<span>` 字号一样大，**或者改变垂直对齐方式**（如顶部对齐，非常常见）：

```css
.box { line-height: 32px; }
.box > span {
  font-size: 24px;
  vertical-align: top;
}
```

### 块级元素内图片底部间隙

任意块级元素里若有图片，则块级元素高度基本都比图片高。间隙产生的三大元凶：**“幽灵空白节点”、line-height 和 vertical-align**。

字母 x 往下的行高产生的多余间隙嫁祸到图片下面，让人以为是图片产生的间隙。清除该间隙的方法：

1. **图片块状化**（`display:block`）：一口气干掉幽灵空白节点、line-height 和 vertical-align。
2. **容器 line-height 足够小**（如 `line-height:0`）。
3. **容器 font-size 足够小**（需 line-height 与 font-size 相关，如 `line-height:1.5`）。
4. **图片设置其他 vertical-align 值**（top / middle / bottom 均可）。

### inline-block 与 baseline

vertical-align 的默认值 baseline，对文本内联元素是字符 x 的下边缘，对替换元素是其下边缘。

**但对 inline-block 元素规则更复杂：一个 inline-block 元素，如果里面没有内联元素，或者 overflow 不是 visible，则该元素的基线就是其 margin 底边缘；否则其基线就是元素里面最后一行内联元素的基线。**

```html
<span class="dib-baseline"></span>
<span class="dib-baseline">x-baseline</span>
```

两个同尺寸的 inline-block，第一个空框基线是 margin 下边缘，第二个框基线是字符 x 的下边缘，因此二者不在同一水平线对齐。

### vertical-align: top / bottom / text-top / text-bottom

* `top`：内联元素和这一行最高的内联元素顶部对齐；table-cell 元素则和 `<tr>` 上边缘对齐。这个“边缘”是当前“行框盒子”的上下边缘，不是块状容器的上下边缘。
* `text-top`：盒子顶部和父级内容区域顶部对齐。
* `text-bottom`：盒子底部和父级内容区域底部对齐。

“父级内容区域”指在父级元素当前 font-size 和 font-family 下应有的内容区域大小。

### 幽灵空白节点

**在 HTML5 文档声明下，块状元素内部的内联元素的行为表现，就好像块状元素内部还有一个（更可能是前后两个）看不见摸不着、没有宽度没有实体的空白节点，这个假想又似乎存在的空白节点，称之为“幽灵空白节点”。**

### 小结

* 一个元素属于 inline 或 inline-block，其身上的 vertical-align 属性才会起作用。
* 一个元素属于 block，其身上的 text-align 才会生效（多用于 `text-align: center`）。
* 元素浮动之后会变为块元素（display 为 block），vertical-align 就不起作用了。