---
layout:     post
title:      "深入理解line-height"
date:       2017-09-18 12:48:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.w3school.com.cn/cssref/pr_dim_line-height.asp)

[CSS:line-height:150%与line-height:1.5的真正区别是什么？](CSS:line-height:150%与line-height:1.5的真正区别是什么？)



[http://www.zhangxinxu.com/wordpress/](http://www.zhangxinxu.com/wordpress/2009/11/css%e8%a1%8c%e9%ab%98line-height%e7%9a%84%e4%b8%80%e4%ba%9b%e6%b7%b1%e5%85%a5%e7%90%86%e8%a7%a3%e5%8f%8a%e5%ba%94%e7%94%a8/)


**“行高”顾名思意指一行文字的高度。具体来说是指两行文字间基线之间的距离**

>该属性会影响行框的布局。在应用到一个块级元素时，它定义了该元素中基线之间的最小距离而不是最大距离。

先说一个大家都熟知的现象，有一个空的div，`<div></div>`，如果没有设置至少大于1像素高度height值时，该div的高度就是个0。如果该div里面打入了一个空格或是文字，则此div就会有一个高度。那么您思考过没有，为什么div里面有文字后就会有高度呢？

这是个看上去很简单的问题，是理解line-height非常重要的一个问题。可能有人会跟认为是：文字撑开的！文字占据空间，自然将div撑开。我一开始也是这样理解的，但是事实上，深入理解inline模型后，我发现，**根本不是文字撑开了div的高度，而是line-height**！


line-height 与 font-size 的计算值之差（在 CSS 中成为“行间距”）分为两半，分别加到一个文本行内容的顶部和底部。可以包含这些内容的最小框就是行框。

原始数字值指定了一个缩放因子，后代元素会继承这个缩放因子而不是计算值。

### 可能的值


| normal  | 默认。设置合理的行间距。                             |
| ------- | ---------------------------------------------------- |
| number  | 设置数字，此数字会与当前的字体尺寸相乘来设置行间距。 |
| length  | 设置固定的行间距。                                   |
| %       | 基于当前字体尺寸的百分比行间距。                     |
| inherit |

### 行高的垂直居中性

line-height的最终表现是通过line boxes实现的，而无论line boxes所占据的高度是多少（无论比文字大还是比文字小），其占据的空间都是与文字内容公用水平中垂线的。



**这一重要的特性可以用来实现文字或图片的垂直居中对齐。**




#### 单行文字的垂直居中对齐
line-height值设置为height一样大小的值可以实现单行文字的垂直居中


#### 多行文字的垂直居中



line-height 属性设置行间的距离（行高）。
该属性会影响行框的布局。在应用到一个块级元素时，它定义了该元素中基线之间的最小距离而不是最大距离。



line-height 与 font-size 的计算值之差（在 CSS 中成为“行间距”）分为两半，分别加到一个文本行内容的顶部和底部。可以包含这些内容的最小框就是行框。

* normal	默认。设置合理的行间距。
* number	设置数字，此数字会与当前的字体尺寸相乘来设置行间距。
* length	设置固定的行间距。
* %	基于当前字体尺寸的百分比行间距。
* inherit	规定应该从父元素继承 line-height 属性的值。

----------

### 深入理解line-height

[http://www.zhangxinxu.com/wordpress/2009/11/css%E8%A1%8C%E9%AB%98line-height%E7%9A%84%E4%B8%80%E4%BA%9B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E5%8F%8A%E5%BA%94%E7%94%A8/](http://www.zhangxinxu.com/wordpress/2009/11/css%E8%A1%8C%E9%AB%98line-height%E7%9A%84%E4%B8%80%E4%BA%9B%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%E5%8F%8A%E5%BA%94%E7%94%A8/)



<strong>撑开div高度的是line-height不是文字内容</strong>





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



关键联系在于：**百分比值**



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









### height:100%不起作用？

[https://segmentfault.com/a/1190000012707337](https://segmentfault.com/a/1190000012707337)

Web浏览器在计算有效宽度时会考虑浏览器窗口的打开宽度。如果你不给宽度设定任何缺省值，那浏览器会自动将页面内容平铺填满整个横向宽度。即我们不设置宽，会自动填满整个横向宽度


但是高度的计算方式完全不一样。事实上，浏览器根本就不计算内容的高度，除非内容超出了视窗范围(导致滚动条出现)。或者你给整个页面设置一个绝对高度。否则，浏览器就会简单的让内容往下堆砌，页面的高度根本就无需考虑。


**因为页面并没有缺省的高度值，所以，当你让一个元素的高度设定为百分比高度时，无法根据获取父元素的高度，也就无法计算自己的高度。**


即父元素的高度只是一个缺省值：height: auto;我们设置height：100%时，是要求浏览器根据这样一个缺省值来计算百分比高度时，只能得到undefined的结果。也就是一个null值，浏览器不会对这个值有任何的反应。


#### 如何解决

现在你知道了吧，line-height,设置%是一个相对父元素计算得来的高度，要想使他有效，我们需要设置父元素的height;

```
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