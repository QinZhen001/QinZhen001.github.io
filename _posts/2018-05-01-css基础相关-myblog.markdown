---
layout:     post
title:      "css基础相关"
date:       2017-07-28 23:14:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文


### 选择器

```html
element>element(子选择期) 	div>p 	选择父级是 <div> 元素的 <p> 元素

element+element(相邻兄弟选择器) 	div+p 	选择紧接着<div>元素之后的<p>元素

element+element(通用兄弟选择器) 	div+p 	选择<div>元素之后的所有<p>元素

```

>这三个老是忘记 Orz 做个笔记 记录一下。。。




### 浮动 


[网页链接](http://www.w3school.com.cn/css/css_positioning_floating.asp)
浮动的框可以向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。
由于浮动框不在文档的普通流中，所以文档的普通流中的块框表现得就像浮动框不存在一样。


---



关于float是否脱离文档流存在争议
float他还是有占位的。一般来说是只有position是脱离文档流的。

<strong>没有完全脱离文档流</strong>

浮动元素不占任何正常文档流空间，而浮动元素的定位还是基于正常的文档流，然后从文档流中抽出并尽可能远的移动至左侧或者右侧。文字内容会围绕在浮动元素周围。当一个元素从正常文档流中抽出后，仍然在文档流中的其他元素将忽略该元素并填补他原先的空间。

浮动概念让人迷惑根源在于浏览器对理论的解读造成的。只能说很多人以IE做标准，其实它不是。


----------


position:absolute和float 都可以脱离标准文档流


与absolute不同的是：
float 的元素还会在文档流上占据一个位置

**float 导致了文字环绕效果。**

CSS中脱离文档流，也就是将元素从普通的布局排版中拿走，其他盒子在定位的时候，会当做脱离文档流的元素不存在而进行定位。

需要注意的是，使用float脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在周围。
　　
更多查看：
[float和position:absolute脱离文本流的区别](http://blog.csdn.net/paediatrician/article/details/52583653)








### @keyframes

[网页链接](http://www.runoob.com/cssref/css3-pr-animation-keyframes.html)

使用@keyframes规则，你可以创建动画。
创建动画是通过逐步改变从一个CSS样式设定到另一个。
在动画过程中，您可以更改CSS样式的设定多次。
指定的变化时发生时使用％，或关键字"from"和"to"，这是和0％到100％相同。

0％是开头动画，100％是当动画完成。

为了获得最佳的浏览器支持，您应该始终定义为0％和100％的选择器。
注意: 使用animation属性来控制动画的外观，还使用选择器绑定动画。
* animationname	必需的。定义animation的名称。
* keyframes-selector	必需的。动画持续时间的百分比。
合法值：0-100%  from (和0%相同)  to (和100%相同)
注意： 您可以用一个动画keyframes-selectors。
* css-styles	必需的。一个或多个合法的CSS样式属性

```css
@keyframes mymove
{
0%   {top:0px; left:0px; background:red;}
25%  {top:0px; left:100px; background:blue;}
50%  {top:100px; left:100px; background:yellow;}
75%  {top:100px; left:0px; background:green;}
100% {top:0px; left:0px; background:red;}
}

@-webkit-keyframes mymove /* Safari and Chrome */
{
0%   {top:0px; left:0px; background:red;}
25%  {top:0px; left:100px; background:blue;}
50%  {top:100px; left:100px; background:yellow;}
75%  {top:100px; left:0px; background:green;}
100% {top:0px; left:0px; background:red;}
}
```


### box-sizing

[网页链接](http://www.w3chtml.com/css3/properties/user-interface/box-sizing.html)

box-sizing：content-box | border-box
默认值：content-box
适用于：所有接受width和height的元素
继承性：无

#### content-box：
padding和border不被包含在定义的width和height之内。对象的实际宽度等于设置的width值和border、padding之和，即 ( Element width = width + border + padding )

此属性表现为标准模式下的盒模型。


#### border-box：
padding和border被包含在定义的width和height之内。对象的实际宽度就等于设置的width值，即使定义有border和padding也不会改变对象的实际宽度，即 ( Element width = width )

此属性表现为怪异模式下的盒模型。

**示例：**

```css
content-box:
.test1{ box-sizing:content-box; width:200px; padding:10px; border:15px solid #eee; }
```

![enter description here][1]

```css
border-box:
.test2{ box-sizing:border-box; width:200px; padding:10px; border:15px solid #eee; }
```

![enter description here][2]




### children与childNodes的区别


[网页链接](https://blog.csdn.net/yhn1121/article/details/52461353)


* childNodes是正统属性   亲生的   比较讲究   事无巨细  都要返回来  
* children是野孩子   它返回来的就只有DOM元素了  其他的文字啥的   都不返回   











### naturalWidth和naturalHeight




[网页链接](https://segmentfault.com/a/1190000007664778)

naturalWidth和naturalHeight是html5新增的属性，它们可以直接获取图片的原始宽高。而且这在Fixefox/Chrome/Safari/Opera/IE9里已经实现。


[https://caniuse.com/#search=naturalWidth](https://caniuse.com/#search=naturalWidth)

页面中的img元素想要获取图片的原始尺寸通常使用innerWidths属性或者使用jQuery的width()方法，
但是如果给图片添加了width样式，那么用innerWidth或width()获得宽度是不是你想要的，因为innerWidth或width()获取的是元素盒模型的实际渲染的宽度，而不是图片的原始宽度。

所以我们使用naturalWidth和naturalHeight去获取图片的原始尺寸，考虑的兼容问题，可以采用new Image()去获得图片的原始尺寸：

```javascript
  function getNaturalSize(img) {
    var narturalSize = {}
    if (img.naturalWidth && img.naturalHeight) {
      narturalSize.width = img.naturalWidth
      narturalSize.height = img.naturalHeight
    } else {
      var image = new Image()
      image.src = img.src
      narturalSize.width = image.width
      narturalSize.height = image.height
    }
    return narturalSize
  }
```




使用该方法可以获取图片的原始尺寸，通常在图片放大缩小，动态生成图片处需要用到该方法！




### 权重


[网页链接](https://www.cnblogs.com/wolfwows/p/5928856.html)

讨论CSS的权重，则必须想了解而且是深刻的了解CSS样式的6种基础选择器：**ID选择器、类选择器、属性选择器、伪类和伪对象选择器、标签选择器以及统配选择器**。

所有在CSS样式中定义的选择符都是由这6种基础选择符组合而成的，组合的方式也分为三种：**后代选择符、子选择符、相邻选择符**。

CSS的权重指的是这些选择符的优先级，优先级高的CSS样式会覆盖优先级低的样式，优先级越高说明权重越高，反之亦然。

![enter description here][1]

图里是英文的，我翻译过来分别说一下，4个等级的定义如下：
* 第一等：代表内联样式，如: style=””，权值为1000。
* 第二等：代表ID选择器，如：#content，权值为100。
* 第三等：代表类，伪类和属性选择器，如.content，权值为10。
* 第四等：代表类型选择器和伪元素选择器，如div p，权值为1。

最后把这些值加起来，再就是当前元素的权重了。


按照规则，基础选择器具有这样的优先级：

```bash
　　　　　　ID > 类 | 伪类 | 属性选择 > 标签类型 | 伪对象 > 通配符
```

----------


**属性选择器** 
a[href] {color:red;}


**伪元素选择器**
伪元素的效果是需要通过添加一个实际的元素才能达到的。
```css
/* 为某个元素的第一行文字使用样式。 */
:first-line
/* 为某个元素中的文字的首字母或第一个字使用样式。 */
:first-letter
/* 在某个元素之前插入一些内容。 */
:before
/* 在某个元素之后插入一些内容。 */
:after
```

>Css3之后伪元素 要用::

----------


1. 如果样式上加有!important标记，例如：
`p{ color: gray !important}`
那么始终采用这个标记的样式。
2. 匹配的内容按照CSS权重排序，权重大的优先。
3. 如果权重也一样，按照它在CSS样式表里声明的顺序，后声明的优先，例如：
```css
h1 {color: blue}
h1 {color: red}

最终胜出的是color: red。
```

#### 定义合适的选择符
　明白了CSS选择符的权重后，我们如何依照选择符的权重定义合适的选择符？

　　定义选择符的原则是：尽量使选择付的权重低，目的是保证样式在应用于多个元素时容易被覆盖，这可提高样式代码的重用性和可维护性。

具体的原则如下：

1. CSS样式中尽量不要使用ID选择器
ID选择器有很高的权重，如果要覆盖使用了ID选择器的样式，就必须在原先使用ID选择器的基础上添加新的选择符（类选择器或者标签类型选择器或者额使用个!important,但这样做的结果是无法重用的样式代码会越来越多）。
2. 减少子选择器的层级
减少选择器的层级的过程也是降低选择符整体权重的过程。
3. 使用组合的CSS类选择器
使用CSS选择器组合的方式，开发者可以不用考虑CSS样式覆盖的问题，避开了计算选择符权重的过程，同时也提高了代码的重用性。




#### !important修改权重

[网页链接](https://www.cnblogs.com/tianma3798/p/6202008.html)


!important为开发者提供了一个增加样式权重的方法。应当注意的是!important是对整条样式的声明，包括这个样式的属性和属性值。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
    .div1 {
        color: red !important;
        /*color: red;*/
    }
    
    #div1 {
        color: blue;
        font-weight: bold;
    }
    </style>
</head>

<body>
    <div class="div1" id='div1' style='color:green;'>
        <p>div1内容</p>
        <span>span中的内容</span>
    </div>
</body>

</html>
```

1. !important会修改当前对应元素的当前css属性和值得权重
2. !important指定的属性权重比class选择器，ID选择器，内联样式的权重都高。
3. !important只对当前元素当前属性权重有影响，对子元素权重没影
4. 如果在在不同选择器中，相同属性和值都指定了!important，选择器权重高的属性起作用

例如：
```css
    .div1 {
        color: red !important;
    }
    
    #div1 {
        color: blue !important;
    }
```
浏览器解析结果，div中颜色为蓝色。


>关键字!important必须放在一行样式的末尾并且要放在该行分号前，否则就没有效果


**非到万不得已不要用!important。如果你是出于懒惰使用!important，为了避免例行的调试而滥用它，那么你（或者是那些后继开发你项目的人）将会深受其害。**






  [1]: https://gss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/zhidao/wh=600,800/sign=4a0826fc9d22720e7b9beafc4bfb267e/b219ebc4b74543a9c1d979521b178a82b8011469.jpg


























  [1]: http://www.w3chtml.com/css3/properties/user-interface/images/content-box.png
  [2]: http://www.w3chtml.com/css3/properties/user-interface/images/border-box.png


















