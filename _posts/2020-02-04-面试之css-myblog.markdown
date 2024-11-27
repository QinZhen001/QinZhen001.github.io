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

```html
<div style="border: 1px solid #000;overflow: hidden">
    <div style="width: 100px;height: 100px;background: #eee;float: left;">
    </div>
</div>
```

![enter description here][7]


**3. BFC 可以阻止元素被浮动元素覆盖**
先来看一个文字环绕效果：

```html
<div style="height: 100px;width: 100px;float: left;background: lightblue">我是一个左浮动的元素
</div>
<div style="width: 200px; height: 200px;background: #eee">我是一个没有设置浮动, 
也没有触发 BFC 元素, width: 200px; height:200px; background: #eee;
</div>
```

这时候其实第二个元素有部分被浮动元素所覆盖，(但是文本信息不会被浮动元素所覆盖) 如果想避免元素被覆盖，可触第二个元素的 BFC 特性，在第二个元素中加入 overflow: hidden，就会变成：

![enter description here][8]

>这个方法可以用来实现两列自适应布局，效果不错，这时候左边的宽度固定，右边的内容自适应宽度(去掉上面右边内容的宽度)。



### 根据样式判断颜色

```css
  <style type="text/css">
    .c1 .c2 div{  
      color: blue;
    }
    div #box3 {  
      color:green;
    }
    #box1 div { 
      color:red;
    }
  </style>
```



```html
  <div id="box1" class="c1">
    <div id="box2" class="c2">
      <div id="box3" class="c3">
        文字
      </div>
    </div>
  </div>
```

文字是什么颜色？

```
 red 上面两选择器的层级都是一样的, 后者覆盖前者 
```



---



```css
<style type="text/css">
  #father #son{ 
    color:blue;
  }
  #father p.c2{ 
    color:black;
  }
  div.c1 p.c2{  
    color:red;
  }
  #father{
    color:green !important;
  }
</style>
```



```html
<div id="father" class="c1">
  <p id="son" class="c2">
    试问这行字体是什么颜色的？
  </p>
</div>
```

答案：blue 







### line-height 计算的一道题

```html
<div class="p1">
    <div class="s1">1</div>
    <div class="s2">1</div>
</div>
<div class="p2">
    <div class="s5">1</div>
    <div class="s6">1</div>
</div>
```

```css
.p1 {font-size: 16px; line-height: 32px;}
.s1 {font-size: 2em;}
.s2 {font-size: 2em; line-height: 2em;}

.p2 {font-size: 16px; line-height: 2;}
.s5 {font-size: 2em;}
.s6 {font-size: 2em; line-height: 2em;}
```

第一组的答案

```
p1：font-size: 16px; line-height: 32px
s1：font-size: 32px; line-height: 32px
s2：font-size: 32px; line-height: 64px
```

- p1 无需解释
- s1 em作为字体单位，相对于父元素字体大小；line-height继承父元素计算值
- s2 em作为行高单位时，相对于自身字体大小

再来看看第二组的答案

```
p2：font-size: 16px; line-height: 32px
s5：font-size: 32px; line-height: 64px
s6：font-size: 32px; line-height: 64px
```

- p2 `line-height: 2`自身字体大小的两倍
- **s5 数字无单位行高，继承原始值，s5的line-height继承的2，自身字体大小的两倍**
- s6 无需解释









## 用纯CSS创建一个三角形

```
width: 0;
height: 0;
border-top: 40px solid transparent;
border-left: 40px solid transparent;
border-right: 40px solid transparent;
border-bottom: 40px solid #ff0000;
```

## 为什么margin:auto可以让块级元素水平居中

这是因为水平方向的 auto，其计算值取决于可用空间（剩余空间）。
**但是，注意display:inline-block和margin:auto同时出现时元素无法居中**





## BFC会与float元素相互覆盖吗

[https://juejin.cn/post/6936913689115099143?utm_source=gold_browser_extension#heading-41](https://juejin.cn/post/6936913689115099143?utm_source=gold_browser_extension#heading-41)

**不会，因为 BFC 是页面中一个独立的隔离容器，其内部的元素不会与外部的元素相互影响**，比如两个 div，上面的 div 设置了 float，那么如果下面的元素不是 BFC，也没有设置 float，会形成对上面的元素进行包裹内容的情况，如果设置了下面元素为 overflow：hidden；属性那么就能够实现经典的两列布局，左边内容固定宽度，右边因为是 BFC 所以会进行自适应。

注明出处。




## 品字布局

```html
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

## containing block

想想浏览器怎么把一个元素“画”出来，至少要知道定位和尺寸。定位有三种normal flow, floats和absolute，无论属于哪种首先要找所在的containing block(我翻译为容器块)，相当于一个大箱子里摆很多小盒子，小盒子怎么摆取决于大箱子。

怎么确定一个元素的containing block，由position属性确定：

1. static(默认的)/relative：简单说就是它的父元素的内容框（即去掉padding的部分）
2. absolute: 向上找最近的定位为absolute/relative的元素
3. fixed: 它的containing block一律为根元素(html/body)，根元素也是initial containing block




##  元素竖向的百分比设定是相对于容器的高度吗？

当按百分比设定一个元素的宽度时，它是相对于父容器的宽度计算的，但是，对于一些表示竖向距离的属性，例如 padding-top , padding-bottom , margin-top , margin-bottom 等，当按百分比设定它们时，**依据的也是父容器的宽度，而不是高度**。



## 单行文本垂直居中

行高是指一行文字的高度，具体说是两行文字间基线的距离。CSS中起高度作用的是height和line-height，没有定义height属性，最终其表现作用一定是line-height。

单行文本垂直居中：把line-height值设置为height一样大小的值可以实现单行文字的垂直居中



## 有一个高度自适应的div，里面有两个div，一个高度100px，希望另一个填满剩下的高度

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



## 行内元素设置margin padding

[https://leetcode-cn.com/circle/discuss/Ua2cjo/](https://leetcode-cn.com/circle/discuss/Ua2cjo/)

**设置 `margin-left` 和 `margin-right` 有影响，而 `margin-top` 和 `margin-bottom` 无影响。**

🚩 行内元素中，padding-left / padding-right / margin-left / margin-right 有影响结果；

🚩 行内元素中，padding-top / padding-bottom / margin-top / margin-bottom 不影响结果；

🚩 padding-top / padding-bottom 虽然不影响结果，**但实际上生效了**。

所以，padding实际上是生效的

padding的值是根据目标元素的width计算出来的，而inline， non-replace元素的width是不确定的



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



## will-change属性

will-change属性可以提前通知浏览器我们要对元素做什么动画，这样浏览器可以提前准备合适的优化设置。这样可以避免对页面响应速度有重要影响的昂贵成本。元素可以更快的被改变，渲染的也更快，这样页面可以快速更新，表现的更加流畅。

举个例子，当对于素使用 CSS 3D变形时，元素及其内容可以在合成到页面之前被创建到我们之前说的layer。然而把元素放到layer中是个昂贵的操作，这将会导致变形动画延迟一个课件的瞬间，也就是flicker

为了避免这种延时，我们可以在发生之前通知浏览器，这样浏览器会有一定的时间去准备这些变化，当发生的时候layer已经准备好了，这样动画酒会很流畅，不会闪屏


使用will-change提示浏览器关于即将发生的变形十分简单，添加个CSS属性就行

```css
will-change: transform;
```

也可以告诉浏览器要改变元素的滚动条位置，或者多个要变化的属性，写下属性的名字就行，也可以写多个，逗号隔开

```css
will-change: transform, opacity;
```

声明了元素即将进行的变化会让浏览器在渲染页面时做更好的决定，这明显比之前说的3D hacks要好。

**了解了will-change的行为，为浏览器上一切元素设置will-change是不是效率会变高？答案是否定的，will-change如果被滥用会使页面崩溃。**

will-change也有副作用，虽然并不直接可见，毕竟它只是在背后和浏览器说悄悄话，为了合理使用will-change，给一些小建议

不要声明太多属性或为太多元素声明

```css
*,
*::before,
*::after {
    will-change: all;
}
```


虽然看起来很屌，但其实对页面渲染伤害很大，这样的规则设了和没设没什么区别，浏览器本来就尝试最优的渲染所有元素，就等于你让老师重点照顾班里每个同学一样，就是废话！

其实这甚至是有害的，因为一些操作会占用太多的资源，甚至会导致页面奔溃，就等于强制要求老师为每个学生补课，累死了。。。





## transition和animation的区别

- **Transition（过渡）**：用于在元素的状态变化时平滑过渡样式的变化。它通常依赖于事件触发（如 hover、focus 等）。例如，当你将鼠标悬停在一个按钮上时，背景颜色可能会渐变。
- **Animation（动画）**：用于创建更复杂的效果，可以在时间轴上设置多个关键帧（keyframes），并在这些关键帧之间进行平滑过渡。动画可以独立于元素的状态变化。
- transition只有两个状态：**开始状态** 和 **结束状态**，但animation可能是多个状态，有帧的概念




## 设为 flex 属性之后，子元素的哪些属性会失效

float、clear 和 vertical-align





## css加载会造成**DOM**阻塞吗

[ https://zhuanlan.zhihu.com/p/43282197 ]( https://zhuanlan.zhihu.com/p/43282197 )

 js执行会阻塞DOM树的解析和渲染，那么css加载会阻塞DOM树的解析和渲染吗？ 

 **css并不会阻塞DOM树的解析 , css加载会阻塞DOM树渲染** 

其实我觉得，这可能也是浏览器的一种优化机制。因为你加载css的时候，可能会修改下面DOM节点的样式，如果css加载不阻塞DOM树渲染的话，那么当css加载完之后，DOM树可能又得重新重绘或者回流了，这就造成了一些没有必要的损耗。所以干脆就先把DOM树的结构先解析完，把可以做的工作做完，然后等你css加载完之后，在根据最终的样式来渲染DOM树，这种做法性能方面确实会比较好一点。

> 1. DOM解析和CSS解析是两个并行的进程，所以这也解释了为什么CSS加载不会阻塞DOM的解析。
> 2.  然而，由于Render Tree是依赖于DOM Tree和CSSOM Tree的，所以他必须等待到CSSOM Tree构建完成，也就是CSS资源加载完成(或者CSS资源加载失败)后，才能开始渲染。因此，CSS加载是会阻塞Dom的渲染的。 
> 3. 由于js可能会操作之前的Dom节点和css样式，因此浏览器会维持html中css和js的顺序。因此，样式表会在后面的js执行前先加载执行完毕。所以css会阻塞后面js的执行。



## **css加载会阻塞js运行吗**

由上面的推论，我们可以得出，css加载不会阻塞DOM树解析，但是会阻塞DOM树渲染。那么，css加载会不会阻塞js执行呢?

 **css加载会阻塞后面js语句的执行** 

```html
<html lang="en">
  <head>
    <title>css阻塞</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script>
      console.log('before css')
      var startDate = new Date()
    </script>
    <link href="https://cdn.bootcss.com/bootstrap/4.0.0-alpha.6/css/bootstrap.css" rel="stylesheet">
  </head>
  <body>
    <h1>这是红色的</h1>
    <script>
      var endDate = new Date()
      console.log('after css')
      console.log('经过了' + (endDate -startDate) + 'ms')   // 5600+ms
    </script>
  </body>
</html>
```

位于css加载语句前的那个js代码先执行了，但是位于css加载语句后面的代码迟迟没有执行，直到css加载完成后，它才执行 

得出结论：

 **css加载会阻塞后面js语句的执行** 

**渲染用的是GUI线程、js执行用的是js引擎线程就是v8，GUI线程与V8之间是互斥的。又因为浏览器会维持html中css和js的顺序，所以css渲染会阻塞js执行。**

-----

因此，为了避免让用户看到长时间的白屏时间，我们应该尽可能的提高css加载速度，比如可以使用以下几种方法:

1. 使用CDN(因为CDN会根据你的网络状况，替你挑选最近的一个具有缓存内容的节点为你提供资源，因此可以减少加载时间)
2. 对css进行压缩(可以用很多打包工具，比如webpack,gulp等，也可以通过开启gzip压缩)
3. 合理的使用缓存(设置cache-control,expires,以及E-tag都是不错的，不过要注意一个问题，就是文件更新后，你要避免缓存而带来的影响。其中一个解决防范是在文件名字后面加一个版本号)
4. 减少http请求数，将多个css文件合并，或者是干脆直接写成内联样式(内联样式的一个缺点就是不能缓存)







## 硬件加速的原理

浏览器接收到页面文档后，会将文档中的标记语言解析为DOM树。DOM树和CSS结合后形成浏览器构建页面的渲染树。渲染树中包含大量的渲染元素，每个渲染元素会被分到一个图层中，每个图层又会被加载到GPU形成渲染纹理，**而图层在GPU中transform是不会触发repaint的**，最终这些使用transform的图层都会由独立的合成器进程进行处理, CSS transform会**创建了一个新的复合图层，可以被GPU直接用来执行transform操作**。

**浏览器什么时候会创建一个独立的复合图层呢？事实上一般是在以下几种情况下：**

- 3D
- `<video>`和`<canvas>`标签
- `css filters(滤镜效果)`
- 元素覆盖时，比如使用了z-index属性
- 对 opacity、transform、fliter、backdropfilter 应用了 animation 或者 transition
- 设置will-change

**所以得出一个结论：当我们做css动画时多使用transform**



### 为什么硬件加速会使页面流畅

因为transform属性不会触发浏览器的repaint（重绘），而绝对定位absolute中的left和top则会一直触发repaint（重绘）。



### 为什么transform没有触发repaint呢？

简而言之，transform动画由GPU控制，支持硬件加载，并不需要软件方面的渲染。**并不是所有的CSS属性都能触发GPU的硬件加载，事实上只有少数的属性可以，比如transform、opacity、filter**

---

CSS的最终表现分为如下四步：`Recalculate Style` -> `Layout` -> `Paint Setup and Paint` -> `Composite Layers`

按照中文的意思大体是 查找并计算样式 -> 排布 -> 绘制 -> 组合层

这上面的几个步骤有点相似于上文说到的重排一定致使重绘，而查询属性会强制发生重排。因此上文提到的重排重绘内容能够结合这里进行理解。

因为`transform`是位于`Composite Layers`层，而`width`、`left`、`margin`等则是位于`Layout`层，在`Layout`层发生的改变一定致使`Paint Setup and Paint` -> `Composite Layers`，因此相对而言使用`transform`实现的动画效果确定比`left`这些更加流畅





## 图层树

浏览器会在构建完布局树之后，还会对特定的节点进行分层，构建一棵 **图层树（Layer Tree）**。

一般情况下，节点的图层会默认属于父亲节点的图层（这些图层也称为 **合成层**），那么什么时候会提升为一个独立的图层呢？

有两种情况需要分别讨论：

- 显式合成
- 隐式合成

### 显式合成

[层叠上下文相关](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)

我们假定用户正面向（浏览器）视窗或网页，而 HTML 元素沿着其相对于用户的一条虚构的 z 轴排开，**层叠上下文**就是对这些 HTML 元素的一个三维构想。众 HTML 元素基于其元素属性按照优先级顺序占据这个空间。

拥有 **层叠上下文** 的节点。

层叠上下文也基本上是由一些特定的 CSS 属性创建的，一般有以下的情况：

1. HTML 根元素本身就具有层叠上下文属性
2. 普通元素 position 不等于 static，并且设置了 z-index 属性，会产生层叠上下文
3. 元素的 opacity 值不是1
4. 元素的 transform 值不是 none
5. 元素的 filter 值不是 none
6. 元素的 isolation 值不是 isolate
7. **will-change 指定的属性值为上面任意一个。**



需要 **裁剪** 的地方

比如一个 div，你只个他设置了 100 * 100 的大小，而你在里面放置了很多内容，那么超出的文字部分就会被裁剪。如果你设置了滚动条，那么滚动条也会被单独提升为一个图层。



### **隐式合成**

接下来就是隐式合成，简单来说就是 **层叠等级低** 的节点被提升为单独的图层之后那么 **所有层叠等级比它高** 的节点都会成为一个单独的图层。

这个隐式合成其实隐藏着一个巨大的风险，如果在一个大型应用中，当一个 z-index 比较低的元素被提升为一个单独的图层之后，层叠在它上面的元素统统都会被提升为单独的图层，可能会增加上千个图层，大大增加内存的压力，甚至直接上页面崩溃，这就是 **层爆炸** 的原理。

值得注意的是，当需要 **重绘** 的时候，只需要重绘本身，而不会影响到其他图层。



### 图层创建的原因

[https://juejin.cn/post/7051926604666109988](https://juejin.cn/post/7051926604666109988)

* 根元素
* 有 z-index 是负值的子元素
* 有 3D 转换
* position：fixed
* 与其他元素可能重叠
* will-change 样式的值为 opacity、transform、transform-style、perspective、filter、backdrop-filter 这 6 个之

我们可以借助浏览器开发者的图层（Layer）来去判断





## 垂直居中的实现

[https://github.com/chokcoco/iCSS/issues/64](https://github.com/chokcoco/iCSS/issues/64)

* absolute + transform

```html
  <style lange="scss">
    .parent{
      position: relative;
      width: 100px;
      height: 100px;
      background:red;
    }
    .child{
      position: absolute;
      left: 50%;
      top: 50%;
      transform:translate(-50%,-50%);
      width: 10px;
      height: 10px;
      background:blue;
    }
  </style>

<div class="parent">
   <div class="child"></div>
 </div>
```

* flex/grid  + margin:auto

```html
  <style lange="scss">
    .parent{
      display: flex;
      width: 100px;
      height: 100px;
      background:red;
    }
    .child{
      margin: auto;
      width: 10px;
      height: 10px;
      background:blue;
    }
  </style> 

<div class="parent">
  <div class="child"></div>
</div>
```

* flex + align-items

  ```html
    <style lange="scss">
      .parent{
        display: flex;
        align-items: center;
        justify-content: center;
        width: 100px;
        height: 100px;
        background:red;
      }
      .child{
        width: 10px;
        height: 10px;
        background:blue;
      }
    </style> 
  
  
  <div class="parent">
     <div class="child"></div>
  </div>
  ```

* table-cell + vertical-align:middle

  ```html
    <style lange="scss">
      .parent{
        display: table-cell;
        vertical-align:middle;
        width: 100px;
        height: 100px;
        background:red;
      }
      .child{
        width: 10px;
        height: 10px;
        background:blue;
      }
    </style> 
  
  
  <div class="parent">
     <div class="child"></div>
  </div>
  ```

在 `display: block` 中，如果 `margin-left` 和 `margin-right` 都是 auto，则它们的表达值相等，从而导致元素的水平居中。( 这里的计算值为元素剩余可用剩余空间的一半) 而如果 `margin-top` 和 `margin-bottom` 都是 auto，则他们的值都为 0，当然也就无法造成垂直方向上的居中。





但是，让该元素处于 FFC(flex formatting context)，或者 GFC(grid formatting context) 上下文中，单个元素使用 `margin: auto` 就可以垂直居中

```css
{
    display: flex;
    display: inline-flex;
    display: grid;
    display: inline-grid;
}
```

**flex 格式化上下文**中，设置了 `margin: auto` 的元素，在通过 `justify-content` 和 `align-self` 进行对齐之前，任何正处于空闲的空间都会分配到该方向的自动 margin 中去

这里，很重要的一点是，margin auto 的生效不仅是水平方向，垂直方向也会自动去分配这个剩余空间。





## 判断图片是否在可视区

[**搞清clientHeight、offsetHeight、scrollHeight、offsetTop、scrollTop**](https://www.imooc.com/article/17571)

判断图片所在位置是否在可视区内，图片移到可视区内进行加载，提供三种判断方法

1. offsetTop < clientHeight + scrollTop
2. element.getBoundingClientRect().top < clientHeight   **(这里的clientHeight 是指设置了overflow: scroll的那个父元素)**
3. IntersectionObserver



这里2的判断其实不够完善

最终版本：

[https://www.cnblogs.com/zzy1996/archive/2004/01/13/11568234.html](https://www.cnblogs.com/zzy1996/archive/2004/01/13/11568234.html)

```js
function elementInView(element) {
  const rect = element.getBoundingClientRect()
  // window.innerHeight 可以替换成 xxx.clientHeight   
  const yInView = rect.top < window.innerHeight && rect.bottom > 0  
  const xInView = rect.left < window.innerWidth && rect.right > 0
  return yInView && xInView
}

```







## FOUC

[gsap使用时避免FOUC](https://gsap.com/resources/fouc)

FOUC（Flash of Unstyled Content，未样式内容闪烁）是指在网页加载时，用户在短时间内看到未应用样式的原始 HTML 内容，然后样式突然应用上去的现象。这种现象通常会导致用户体验不佳，因为页面在加载过程中会出现闪烁或布局变化。

FOUC 产生的原因

FOUC 通常是由于以下原因引起的：

1. **样式表加载延迟**：
   - 样式表（CSS）文件加载较慢，导致浏览器在渲染 HTML 内容时没有样式可用。

2. **异步加载样式表**：
   - 使用 `<link rel="stylesheet" href="styles.css" media="none" onload="this.media='all';">` 等方法异步加载样式表，但在样式表加载完成前页面已经开始渲染。

3. **JavaScript 动态加载样式**：
   - 使用 JavaScript 动态加载样式表，导致样式表在 HTML 内容加载后才应用。

解决 FOUC 的方法

以下是一些常见的解决方法，可以帮助减少或消除 FOUC 现象：

1. **将关键 CSS 内联到 HTML 中**：
   - 将关键的 CSS 直接内联到 HTML 文档的 `<head>` 部分，这样在页面加载时立即应用样式：
     ```html
     <style>
       /* 关键 CSS */
       body { font-family: Arial, sans-serif; }
       /* 其他关键样式 */
     </style>
     ```

2. **确保 CSS 文件尽早加载**：
   - 确保样式表尽早加载，可以将 `<link>` 标签放在 HTML 文档的 `<head>` 部分：
     ```html
     <head>
       <link rel="stylesheet" href="styles.css">
     </head>
     ```

3. **减少 CSS 文件的数量和大小**：
   - 减少 CSS 文件的数量和大小，合并和压缩 CSS 文件，减少加载时间。

4. **使用服务器端渲染（SSR）**：
   - 使用服务器端渲染技术，将 HTML 和 CSS 在服务器端生成并发送给客户端，减少客户端加载时间。

5. **优化网络请求**：
   - 使用内容分发网络（CDN）加速样式表的加载。
   - 启用浏览器缓存，减少重复加载样式表。

6. **预加载关键资源**：
   - 使用 `<link rel="preload" href="styles.css" as="style">` 提前加载关键资源：
     ```html
     <link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
     <noscript><link rel="stylesheet" href="styles.css"></noscript>
     ```

通过这些方法，可以有效减少或消除 FOUC 现象，提升用户体验。









[1]: https://user-images.githubusercontent.com/17233651/41773411-91e22044-764e-11e8-8ad4-9066db87166f.png
[2]: https://user-images.githubusercontent.com/17233651/41775365-36a0b0da-7656-11e8-8495-bd58f7ab0bf2.png
[3]: https://user-images.githubusercontent.com/17233651/41775919-6a41ae42-7658-11e8-8e54-43ad05c12d43.png
[4]: https://pic4.zhimg.com/80/v2-0a9ca8952c83141250a2d9002e6d2047_hd.jpg
[5]: https://pic4.zhimg.com/80/v2-5b8d6e8b2b507352900c1ece00018855_hd.jpg
[6]: https://pic1.zhimg.com/80/v2-371eb702274af831df909b2c55d6a14b_hd.jpg
[7]: https://pic2.zhimg.com/80/v2-cc8365db5c9cc5ca003ce9afe88592e7_hd.jpg