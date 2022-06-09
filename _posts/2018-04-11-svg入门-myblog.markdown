---
layout:     post
title:      "svg入门"
date:       2018-04-11 15:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Svg
---

> “Yeah It's on. ”


## 正文
[网页链接](https://juejin.im/post/5acd7c316fb9a028c813348d)

可缩放矢量图形的一大优势（除了它们可以被无限缩放而没有质量损失之外）是一旦你知道了基本的原理，无需打开绘图程序你就能很容易地手写出一些简单的形状。

只要很少的几行代码你就可以拥有你的自定义图标，而且你还很清楚每个图标是怎么组合出来的。当你创建自己的 SVG 时，你可以确保以最有效的方式来编写它们，并且当你在站点中使用它们时，你拥有最大的控制权。


### 基本的 SVG 元素
* `<svg>` 包裹并定义整个矢量图。`<svg>` 标签之于矢量图就如同 `<html>` 标签之于一个 web 页面。
* `<line>` 创建一条直线。
* `<polyline>` 创建折线。
* `<rect>` 创建矩形。
* `<ellipse>` 创建圆和椭圆。
* `<polygon>` 创建多边形。
* `<path>` 通过指定点以及点和点之间的线来创建任意形状。
* `<defs>` 定义一个可复用的图形。初始情况下 `<defs>` 里面的内容是不可见的。`<defs>` 标签之于矢量图就如同 `<head>` 标签之于一个 web 页面。
* `<g>` 将多种形状组合起来。将组合后的形状置于 `<defs>` 中可以让它能够被复用。
* `<symbol>` 类似于一个组合，但是拥有一些额外的特性。通常被置于`<defs>` 标签中便于复用。
* `<use>` 获取在`<defs>` 中定义的复用对象并在 SVG 中显示出来。



### 默认的 SVG 样式
注意在初始的 HTML 文件中有一些内嵌的 CSS 设置了我们即将创建的图标的默认样式。

```
svg {
  stroke: #000;
  stroke-width: 5;
  stroke-linecap: round;
  stroke-linejoin: round;
  fill: none;
}
```





### `<path>`

我们将会只用到 `<path>` 的属性 d。d 代表 data，在这里，你将定义路径的所有点和线。在这个属性里，设置路径点和在点之间创建连线的命令是由诸如 M 或 L 这样的单个字母来提供的，然后是一组 x 和/或 y 坐标。


* M 表示移动到（moveto）。它用 x 值和 y 值来给定一条新的路径的起始点。可以想象成把你的鼠标放在画布上的某一点以准备开始绘画。大写的 M 表示移动到一个绝对坐标集合（小写的 m 表示移动到一个相对坐标集合）。
* L 表示划线到（lineto）。从当前位置到新的位置画一条线。大写的 L 表示移动到一个绝对坐标集合（小写的 l 表示移动到一个相对坐标集合）。
* Z 表示闭合路径。通过在当前点和路径的起始点之间画一条直线来闭合形状。


```
    <path d="
        M 18 3
        L 46 3
        L 46 40
        L 61 40
        L 32 68
        L 3 40
        L 18 40
        Z
    "></path>
```


#### svg路径path做的几种效果
1. [做一个路径变形动画](https://codepen.io/chriscoyier/pen/NRwANp) (它的实现是hover的时候改变path的d值，然后做d的transition动画)
2. 不规则形状的点击
3. 沿着路径拖拽
4. 路径的变形动画
5. clip-path做一些遮罩效果



### 使用 `<use>` 来放置组合
在 `<defs>` 元素内部我们现在有三个图标被定义成组合，我们准备在 SVG 中使用它们。为了使用它们，我们需要做的就是添加一个 `<use>` 元素（确保在 `<defs>` 元素以外且之后的地方添加它），并设置一个 href 属性指向想要的那个图标的 ID。



### 使用 symbols 创建模板对象
除了组合之外，你也可以使用模板来定义你的图标。模板几乎和组合一样，但是你可以获得额外的设置来控制视口（viewbox）和长宽比。

如果你想要把我们目前创建的图标设置为居中，这将非常有用。我们将把剩下的三个图标转换成模板，然后使它们垂直填充100像素高的空间，并在这个空间中水平居中。


我们以与创建组合相同的方式来创建我们的模板，只是将我们最后三个图标的代码包裹进 `<symbol></symbol>` 模板中。我们也需要给每个模板加一个唯一的 ID。


但是我们也需要添加一个叫做 viewBox 的属性。该属性让我们可以定义每个模板的可见部分应该是什么。当浏览器能够访问这些信息时，它就可以正确地缩放和排列模板。


####  viewBox
所述的值viewBox属性是四个数字的列表min-x，min-y，width和height，由空格和/或逗号，其指定在其中应被映射到由给定元素建立的视口的边界用户空间中的矩形分隔


作者：富途web开发团队
链接：https://juejin.im/post/5acd7c316fb9a028c813348d
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### textPath

[网页链接](https://blog.csdn.net/huanhuanq1209/article/details/71425612)

在学习css揭秘中，发现可以用svg的textPath实现各种路径的文字效果，打开了新世界大门。所以在此深入理解下SVG的textPath。

[环形文字](https://github.com/QinZhen001/learn-css-secrets/blob/master/%E5%AD%97%E4%BD%93%E6%8E%92%E5%8D%B0/%E7%8E%B0%E5%AE%9E%E4%B8%AD%E7%9A%84%E6%96%87%E5%AD%97%E6%95%88%E6%9E%9C/%E7%8E%AF%E5%BD%A2%E6%96%87%E5%AD%97/circular-text.html)


----------


SVG的文本可以沿着一条自定义的Path来排布，比如曲线、圆形等等，使用方式如下所示


```css
<svg viewBox="0 0 1000 300"
     xmlns="http://www.w3.org/2000/svg" 
     xmlns:xlink="http://www.w3.org/1999/xlink">
  <defs>
    <path id="MyPath"
          d="M 100 200 
             C 200 100 300   0 400 100
             C 500 200 600 300 700 200
             C 800 100 900 100 900 100" />
  </defs>

  <use xlink:href="#MyPath" fill="none" stroke="red"  />

 <text font-family="Verdana" font-size="42.5">
    <textPath xlink:href="#MyPath">
      We go up, then we go down, then up again
    </textPath>
  </text>

  <!-- Show outline of the viewport using 'rect' element -->
  <rect x="1" y="1" width="998" height="298"
        fill="none" stroke="black" stroke-width="2" />
</svg>
```


使用很简单，在`<defs>`下定义一个path，在`<text>`元素下添加一个textPath引用，即可达到效果。



我们来对代码做一点儿修改，给text元素添加x和y：

```css
 <text x=100 y=100  font-family="Verdana" font-size="42.5">
    <textPath xlink:href="#MyPath">
      We go up, then we go down, then up again
    </textPath>
  </text>
```

可以注意到，text并没有进行简单的平移操作


这要如何理解呢？



原因很简单，text的坐标系被修改了，没有加入textPath之前，text处于一个直角坐标系下，x轴和y轴是两条相互垂直的直线。加入textPath之后，text的坐标系有如下性质：

1. 坐标系的x轴为path；
2. 坐标系的y轴在x轴的任意点上，方向都不一致，但是必然是该点对于**x轴切线的垂直线**。









## 补充

### stroke属性

[网页链接](http://blog.csdn.net/ning0_o/article/details/54970474)

定义一条线，文本或元素轮廓颜色

#### stroke-width
定义一条线，文本或元素轮廓厚度

#### stroke-linecap
描边端点表现形式
```
<svg>
  <g fill='none' stroke='black' stroke-width='10'>
    <path stroke-linecap='butt' d='M5 20 l215 0' />
    <path stroke-linecap='round' d='M5 40 l215 0' stroke='red'/>
    <path stroke-linecap='square' d='M5 60 l215 0' />
  </g>
</svg>
```

#### **stroke-dasharray**
用于创建虚线
* stroke-dasharray = '10'
* stroke-dasharray = '10, 10'
* stroke-dasharray = '10, 10, 5, 5'

绘制虚线: 一个参数时：表示一段虚线长度和每段虚线之间的间距

两个参数或者多个参数时：一个表示长度，一个表示间距

#### **stroke-dashoffset**
定义一条线，文本或元素距离（相当于基于position：relative；**设置left值**。只是不像left单纯的基于x方向设置， stroke-dashoffset是基于svg路径设置的） 


----------

>stroke-dasharray和stroke-dashoffset相结合可以做出很炫酷的效果

举个例子：按钮鼠标滑过动效，（鼠标滑过按钮，边框绕自身选中一周）

```
   #shape {
      stroke-width: 6px;
      fill: transparent;
      stroke: #009FFD;
      stroke-dasharray: 85 400;
      stroke-dashoffset: -220;
      transition: 1s all ease
    }
    svg:hover #shape {
      stroke-dasharray: 70 0;
      stroke-width: 3px;
      stroke-dashoffset: 0;
      stroke: #06D6A0
    }

  <svg height="40" width="150">
    <rect id="shape" height="40" width="150" />
  </svg>
```

#### stroke-linejoin
描边转角的表现方式
stroke-linejoin = miter
stroke-linejoin = round
stroke-linejoin = bevel

#### stroke-opacity
描边透明度


### clipPathUnits属性
[https://blog.csdn.net/leo8729/article/details/48374631](https://blog.csdn.net/leo8729/article/details/48374631)

`<clipPath>`元素包括很多个属性，比如id,class,transform和fill和stroke等，其中最有用的是clipPathUnits属性。
clipPathUnits主要用来给`<clipPath>`元素内容指定一个坐标系统。它具有两个值：

* userSpaceOnUse（默认值）：当clipPath元素是用来当作参考物时，clipPath元素内容是以用户坐标系统作为参考点
* objectBoundingBox：当你设置了objectBoundingBox值后，`<clipPath>`元素中的内容必须在指定的坐标[0,1]内。


`<clipPath>`元素不会直接在页面上呈现，他唯一的作用就是可以通过clip-path来引用。
display属性不能运用于`<clipPath>`元素上，因此，就算display设置none外的其他值，<clipPath>元素也不会直接呈现。

[心形遮罩](https://github.com/QinZhen001/animation-demo/blob/master/clip-path/index.html)


### 获取path的某点坐标和总长度
```css
<svg class="volumn-controller" width="580" height="400" xmlns="http://www.w3.org/2000/svg">
  <path class="volumn-path" stroke="#000" d="m100,247c93,-128 284,-129 388,6" opacity="0.5" stroke-width="1"
        fill="#fff"/>
  <circle class="drag-button" r="12" cy="247" cx="100" stroke-width="1" stroke="#000" fill="#fff"/>
  </g>
</svg>
```

```javascript
  let volumnPath = document.querySelector('.volumn-path')
    // 输出path在长度为100的位置的点坐标
    console.log(volumnPath.getPointAtLength(100))
    // 输出当前path的总长度
    console.log(volumnPath.getTotalLength());
```


[svg的path实现的控制音量](https://github.com/QinZhen001/animation-demo/blob/master/svg-path-volumn/index.html)



