---
layout:     post
title:      "面试之css"
date:       2019-03-13 21:24:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 面试
---

> “Yeah It's on. ”



## 页面布局类

### 经典三栏布局

[代码地址](https://github.com/QinZhen001/front-end-demo/blob/master/layout/three-column-layout/three-column-layout.html)

出现的问题：
当屏幕width过小时，中间部分的文字会跑到左边(例如：当采取float解决方案时)

>这里是因为元素被浮动元素覆盖(但是文本信息不会被浮动元素所覆盖)，想要避免，可以将中间部分改为BFC 特性


**当屏幕width过小时,中间部分挤压变高，如果是flexbox解决方案，左右部分也会跟着变高，这就也是为什么推荐尽可能使用flex布局的原因之一。**


### 百分比

#### 子元素height和width的百分比

子元素的height或width中使用百分比，是相对于子元素的直接父元素，width相对于父元素的width，height相对于父元素的height

```html
<div class="parent">
  <div class="child"></div>
</div>
```

```css
.father{
    width:200px;
    height:100px;
}
.child{
    width:50%;
    height:50%;
}
```

![][1]


#### 子元素top和bottom 、left和right的百分比

* 子元素的top和bottom如果设置百分比，则相对于直接非static定位(默认定位)的父元素的高度
* 子元素的left和right如果设置百分比，则相对于直接非static定位(默认定位的)父元素的宽度。



#### **子元素padding的百分比**

**子元素的padding如果设置百分比，不论是垂直方向或者是水平方向，都相对于直接父亲元素的width，而与父元素的height无关。**

```css
.parent{
  width:200px;
  height:100px;
  background:green;
}
.child{
  width:0px;
  height:0px;
  background:blue;
  color:white;
  padding-top:50%;
  padding-left:50%;
}
```

展示的效果为：


![enter description here][2]


**通常我们可以利用这个特性实现自适应的图片或背景(重要)**


#### **子元素margin的百分比**

跟padding一样，margin也是如此，子元素的margin如果设置成百分比，不论是垂直方向还是水平方向，都相对于直接父元素的width。

#### **子元素border-radius的百分比**


border-radius不一样，如果设置border-radius为百分比，则是相对于自身的宽度，举例来说：

```html
  <div class="trangle"></div>
```

设置border-radius为百分比：

```css
.trangle{
  width:100px;
  height:100px;
  border-radius:50%;
  background:blue;
  margin-top:10px;
}
```


![enter description here][3]

**除了border-radius外，还有比如translate、background-size等都是相对于自身的**




### BFC

[网站链接](https://zhuanlan.zhihu.com/p/25321647)

Formatting context(格式化上下文) 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。


BFC 即 Block Formatting Contexts (块级格式化上下文)，它属于普通流 (normal flow)

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

通俗一点来讲，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

#### 触发 BFC

* body 根元素
* 浮动元素：float 除 none 以外的值
* 绝对定位元素：position (absolute、fixed)
* display 为 inline-block、table-cell、flex
* overflow 除了 visible 以外的值 (hidden、auto、scroll)



#### BFC 特性及应用

**1.同一个 BFC 下外边距会发生折叠**

```
<head>
div{
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
</head>
<body>
    <div></div>
    <div></div>
</body>
```

![enter description here][4]


从效果上看，因为两个 div 元素都处于同一个 BFC 容器下 (这里指 body 元素) 所以第一个 div 的下边距和第二个 div 的上边距发生了重叠，所以两个盒子之间距离只有 100px，而不是 200px。

如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器中。

```
<div class="container">
    <p></p>
</div>
<div class="container">
    <p></p>
</div>
.container {
    overflow: hidden;
}
p {
    width: 100px;
    height: 100px;
    background: lightblue;
    margin: 100px;
}
```

这时候，两个盒子边距就变成了 200px 


![enter description here][5]

**2.BFC 可以包含浮动的元素（清除浮动）**
我们都知道，浮动的元素会脱离普通文档流

```
<div style="border: 1px solid #000;">
    <div style="width: 100px;height: 100px;background: #eee;float: left;">
    </div>
</div>
```

![enter description here][6]

由于容器内元素浮动，脱离了文档流，所以容器只剩下 2px 的边距高度。如果使触发容器的 BFC，那么容器将会包裹着浮动元素。

```
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;">
    </div>
</div>
```

![enter description here][7]


**3. BFC 可以阻止元素被浮动元素覆盖**
先来看一个文字环绕效果：

```
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素
</div>
<div style="width: 200px; height: 200px;background: #eee">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;
</div>
```

这时候其实第二个元素有部分被浮动元素所覆盖，(但是文本信息不会被浮动元素所覆盖) 如果想避免元素被覆盖，可触第二个元素的 BFC 特性，在第二个元素中加入 overflow: hidden，就会变成：

![enter description here][8]

>这个方法可以用来实现两列自适应布局，效果不错，这时候左边的宽度固定，右边的内容自适应宽度(去掉上面右边内容的宽度)。


### 用纯CSS创建一个三角形

```
width: 0;
height: 0;
border-top: 40px solid transparent;
border-left: 40px solid transparent;
border-right: 40px solid transparent;
border-bottom: 40px solid #ff0000;
```

### 为什么margin:auto可以让块级元素水平居中

这是因为水平方向的 auto，其计算值取决于可用空间（剩余空间）。
**但是，注意display:inline-block和margin:auto同时出现时元素无法居中**


### 品字布局

```
		<style type="text/css">
	
			.container {
				text-align: center;
				font-size: 0;
			}
			.top {
				width: 200px;
				height: 200px;
				margin: 0 auto;
				background: #FF0000;
			}
			.bottom-left,
			.bottom-right {
				width: 200px;
				height: 200px;
				display: inline-block;
				margin: 0 auto;
			}
			.bottom-left {
				background: aqua;
			}
			.bottom-right {
				background: #0000FF;
			}
		</style>
	<body>
		<div class="container">
			<div class="top">
			</div>
			<div class="bottom-left">
			</div>
			<div class="bottom-right">
			</div>
		</div>
	</body>
```

### containing block

想想浏览器怎么把一个元素“画”出来，至少要知道定位和尺寸。定位有三种normal flow, floats和absolute，无论属于哪种首先要找所在的containing block(我翻译为容器块)，相当于一个大箱子里摆很多小盒子，小盒子怎么摆取决于大箱子。

怎么确定一个元素的containing block，由position属性确定：

1. static(默认的)/relative：简单说就是它的父元素的内容框（即去掉padding的部分）
2. absolute: 向上找最近的定位为absolute/relative的元素
3. fixed: 它的containing block一律为根元素(html/body)，根元素也是initial containing block

* 


###  元素竖向的百分比设定是相对于容器的高度吗？

当按百分比设定一个元素的宽度时，它是相对于父容器的宽度计算的，但是，对于一些表示竖向距离的属性，例如 padding-top , padding-bottom , margin-top , margin-bottom 等，当按百分比设定它们时，**依据的也是父容器的宽度，而不是高度**。



### 利用line-height使单行文本垂直居中

行高是指一行文字的高度，具体说是两行文字间基线的距离。CSS中起高度作用的是height和line-height，没有定义height属性，最终其表现作用一定是line-height。

单行文本垂直居中：把line-height值设置为height一样大小的值可以实现单行文字的垂直居中，

### 有一个高度自适应的div，里面有两个div，一个高度100px，希望另一个填满剩下的高度

外层div使用position：relative；高度要求自适应的div使用position: absolute; top: 100px; bottom: 0; left: 0





## 全屏滚动的原理是什么

原理：有点类似于轮播，整体的元素一直排列下去，假设有5个需要展示的全屏页面，那么高度是500%，只是展示100%，剩下的可以通过transform进行y轴定位，也可以通过margin-top实现
`verflow：hidden；transition：all 1000ms ease；`





## 清除浮动的方式

* 父级div定义height
* 最后一个浮动元素后加空div标签 并添加样式clear:both。
* 包含浮动元素的父标签添加样式overflow为hidden或auto。
* 父级div定义zoom:1
* 父级div定义伪类:after{display:block;clear:both;content:"";visibility:hidden;height:0}



## 在网页中的应该使用奇数还是偶数的字体？

使用偶数字体。偶数字号相对更容易和 web 设计的其他部分构成比例关系。Windows 自带的点阵宋体(中易宋体)从 Vista 开始只提供 12、14、16 px 这三个大小的点阵，而 13、15、17 px时用的是小一号的点。(即每个字占的空间大了 1 px，但点阵没变)，于是略显稀疏。



## 伪类与伪元素

伪类如：:focus,:hover,:link,:visited
伪元素如：::before,::after


特性及其区别：

* 伪类本质上是为了弥补常规CSS选择器的不足，以便获取到更多信息；
* 伪元素本质上是创建了一个有内容的虚拟容器；
* CSS3中伪类和伪元素的语法不同；
* 可以同时使用多个伪类，而只能同时使用一个伪元素；

>在CSS3中，单冒号(:)用于CSS3伪类，双冒号(::)用于CSS3伪元素。



## CSS Sprites

将一个页面涉及到的所有图片都包含到一张大图中去，然后利用CSS的 background-image，background- repeat，background-position 的组合进行背景定位。利用CSS Sprites能很好地减少网页的http请求，从而大大的提高页面的性能；CSS Sprites能减少图片的字节。





##  position跟display、overflow、float这些特性相互叠加后会怎么样？

display属性规定元素应该生成的框的类型；position属性规定元素的定位类型；float属性是一种布局方式，定义元素在哪个方向浮动。

类似于优先级机制：position：absolute/fixed优先级最高，有他们在时，float不起作用，display值需要调整。**float 或者absolute定位的元素，只能是块元素或表格。**

## box-sizing属性

用来控制元素的盒子模型的解析模式，默认为content-box

* context-box：W3C的标准盒子模型，设置元素的 height/width 属性指的是content部分的高/宽
* border-box：IE传统盒子模型。设置元素的height/width属性指的是border + padding + content部分的高/宽





## [display的几种常用取值](https://www.cnblogs.com/demonswang/p/7161313.html)



* none 此元素不会被显示，并且不占据页面空间
* inline 行内元素 元素会在一行内显示，超出屏幕宽度自动换行，不能设置宽度和高度，元素的宽度和高度只能是靠元素内的内容撑开。
* block 块级元素 会独占一行，如果不设置宽度，其宽度会自动填满父元素的宽度，可以设置宽高，即使设置了宽度，小于父元素的宽度，块级元素也会独占一行。
* inline-block 内块元素 与行内元素一样可以再一行内显示，而且可以设置宽高，可以设置margin和padding。





## 相邻的两个inline-block节点为什么会出现间隔



原因：



元素被当成行内元素排版的时候，元素之间的空白符（空格、回车换行等）都会被浏览器处理，根据white-space的处理方式（默认是normal，合并多余空白），原来HTML代码中的回车换行被转成一个空白符，在字体不为0的情况下，空白符占据一定宽度，所以inline-block的元素之间就出现了空隙。这些元素之间的间距会随着字体的大小而变化，当行内元素font-size:16px时，间距为8px。



解决办法：

**为父元素中设置font-size: 0，在子元素上重置正确的font-size**































[1]: https://user-images.githubusercontent.com/17233651/41773411-91e22044-764e-11e8-8ad4-9066db87166f.png
[2]: https://user-images.githubusercontent.com/17233651/41775365-36a0b0da-7656-11e8-8495-bd58f7ab0bf2.png
[3]: https://user-images.githubusercontent.com/17233651/41775919-6a41ae42-7658-11e8-8e54-43ad05c12d43.png
[4]: https://pic4.zhimg.com/80/v2-0a9ca8952c83141250a2d9002e6d2047_hd.jpg
[5]: https://pic4.zhimg.com/80/v2-5b8d6e8b2b507352900c1ece00018855_hd.jpg
[6]: https://pic1.zhimg.com/80/v2-371eb702274af831df909b2c55d6a14b_hd.jpg
[7]: https://pic2.zhimg.com/80/v2-cc8365db5c9cc5ca003ce9afe88592e7_hd.jpg