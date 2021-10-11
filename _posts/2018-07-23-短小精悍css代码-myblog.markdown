---
layout:     post
title:      "短小精悍css代码"
date:       2018-07-23 21:07:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文

### 自定义滚动条

```
// scss
scrollBar {
  &::-webkit-scrollbar-track-piece {
    background: #d3dce6;
  }
  &::-webkit-scrollbar {
    width: 6px;
  }
  &::-webkit-scrollbar-thumb {
    background: #99a9bf;
    border-radius: 20px;
  }
}
```


### 文字抗锯齿
[网页链接](http://usabilitypost.com/2012/11/05/stop-fixing-font-smoothing/)

```css
body {
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
}
```

antialiased - 平滑像素级别上的字体，而不是子像素。在深色背景上从子像素渲染切换到抗锯齿，使其看起来更轻。



grayscale - 使用灰度抗锯齿渲染文本，而不是子像素。在深色背景上从子像素渲染切换到抗锯齿，使其看起来更轻。



**说人话，也就是在深色背景中浅色字体会更加纤细**


### 优化渲染文本
```css
body {
  text-rendering: optimizeLegibility;
}
```

该text-renderingCSS属性提供信息，以什么来优化渲染文本时的渲染引擎。

浏览器在速度，易读性和几何精度之间进行权衡。


```css
/* Keyword values */
text-rendering: auto;
text-rendering: optimizeSpeed;
text-rendering: optimizeLegibility;
text-rendering: geometricPrecision;

/* Global values */
text-rendering: inherit;
text-rendering: initial;
text-rendering: unset;
```

>该text-rendering属性是SVG属性，未在任何CSS标准中定义。但是，Gecko和WebKit浏览器允许您将此属性应用于Windows，macOS和Linux上的HTML和XML内容。



**一个非常明显的效果是optimizeLegibility**，对于某些字体（例如，Microsoft的Calibri，Candara，Constantia和Corbel，或者DejaVu字体系列），文本中的连字（ff，fi，fl等）小于20px 。



#### auto
浏览器在绘制文本时做出有关何时优化速度，易读性和几何精度的有根据的猜测。有关浏览器如何解释此值的差异，请参阅兼容性表。
#### optimizeSpeed
浏览器在绘制文本时强调渲染速度超过易读性和几何精度。它禁用字距调整和连字。
#### optimizeLegibility
浏览器强调了渲染速度和几何精度的易读性。这样可以进行字距调整和可选的连字。
#### geometricPrecision
浏览器强调几何精度，而不是渲染速度和易读性。字体的某些方面（例如字距调整）不会线性缩放。因此，此值可以使使用这些字体的文本看起来很好。




###  padding 的百分比值 

**padding 百分比值无论是水平方向还是垂直方向均是相对于宽度计算的！** 


margin 的百分比值 和padding 属性一样



### 内联元素垂直方向的 margin 

margin对尺寸的影响是针对具有块状特性的元素而言的，对于纯内联元素则不适用。 


和 padding 不同，内联元素垂直方向的 margin 是没有任何影响的，既不会影响外部尺寸， 也不会影响内部尺寸，有种石沉大海的感觉。对于水平方向，由于内联元素宽度表现为“包裹 性”，也不会影响内部尺寸。 



### margin:auto计算有一个前提条件

为什么明明 容器定高、元素定高，margin:auto 却无法垂直居中？ 

```css
.father {    
    height: 200px; 
} 
.son { 
    height: 100px; 
    margin: auto; 
} 
```

触发 margin:auto 计算有一个前提条件，就是 width 或 height 为 auto 时， 元素是具有对应方向的自动填充特性的


比方说这里，假如说把.son 元素的 height:100px 去 掉，.son 的高度会自动和父元素等高变成 200px 吗？显然不会！因此无法触发 margin:auto 计算，故而无法垂直居中。 

垂直方向 margin 无法实现居中了吗？当然是可以的，而且场景还不止一种。





### 元素尺寸



**元素尺寸**：对应 jQuery 中的$().width()和$().height()方法，包括 padding 和border，也就是元素的border box的尺寸。在原生的DOM API中写作offsetWidth 和 offsetHeight，所以，有时候也成为“元素偏移尺寸”。 


**元素内部尺寸**：对应 jQuery 中的$().innerWidth()和$().innerHeight()方法， 表示元素的内部区域尺寸，包括 padding 但不包括 border，也就是元素的 padding box 的尺寸。在原生的 DOM API 中写作 clientWidth 和 clientHeight，所以， 有时候也称为“元素可视尺寸”。 



**元素外部尺寸**：对应 jQuery 中的$().outerWidth(true)和$().outerHeight (true)方法，表示元素的外部尺寸，不仅包括 padding 和 border，还包括 margin， 也就是元素的 margin box 的尺寸。没有相对应的原生的 DOM API。





### margin改变元素尺寸


CSS 世界默认的流方向是水平方向，因此，对于普通流体元素，margin 只能改变元素水 平方向尺寸；但是，对于具有拉伸特性的绝对定位元素，则水平或垂直方向都可以，因为此时 的尺寸表现符合“充分利用可用空间”。



### margin负值与等高布局实例页面

[http://demo.cssworld.cn/4/3-2.php](http://demo.cssworld.cn/4/3-2.php)


核心思想:
```css
.column-left,
.column-right {
    margin-bottom: -9999px;
    padding-bottom: 9999px;
}
```


### display:table-cell实现垂直居中

在父元素设置

```css
.container {
            display: table-cell;
            vertical-align: middle;
            width: 240px;
            height: 180px;
        }
```

这样子元素就可以垂直居中了


特别提醒

**table-cell不感知margin，在父元素上设置table-row等属性，也会使其不感知height。**


>与其他一些display属性类似，table-cell同样会被其他一些CSS属性破坏，例如float, position:absolute，所以，在使用display:table-cell与float:left或是position:absolute属性尽量不用同用。设置了display:table-cell的元素对宽度高度敏感，响应padding属性，基本上就是活脱脱的一个td标签元素了。



### 绝对定位结合margin: auto实现垂直居中

```css
    .container {
      position: relative;
      margin: 20px;
      width: 300px;
      height: 300px;
      background: red;
    }

    .cell {
      position: absolute;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      margin: auto;
      width: 100px;
      height: 100px;
      text-align: center;
      background: rebeccapurple;
    }
```


```html
<div class="container">
  <div class="cell">
    asdasd
    asdlfkkljl
  </div>
</div>
```

这种实现方式的两个核心是：把要垂直居中的元素相对于父元素绝对定位，top和bottom设为相等的值，我这里设成了0，当然你也可以设为99999px或者-99999px无论什么，只要两者相等就行，这一步做完之后再将要居中元素的margin设为auto，这样便可以实现垂直居中了。


### 三维闪动 bug 处理

```css
.transform-fix() { 
    -webkit-backface-visibility: hidden;
    -webkit-transform-style: preserve-3d;
}
```


transform-style 属性 让转换的子元素保留3D转换：


transform--style属性指定嵌套元素是怎样在三维空间中呈现。


>注意： 使用此属性必须先使用 transform 属性.


| 值          | 描述                           |
| ----------- | ------------------------------ |
| flat        | 表示所有子元素在2D平面呈现。   |
| preserve-3d | 表示所有子元素在3D空间中呈现。 |




### Animation的Mixin封装
```
.ani(@name, @time: 1s, @ease: ease-in-out, @fillmode: forwards) {
  animation-name: @name;
  animation-duration: @time;
  animation-timing-function: @ease;
  animation-fill-mode: @fillmode;
}
```


### 禁止文本被选择

```css
.user-select() { 
    -webkit-user-select: none; 
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
```


### 隐藏鼠标手势

```css
.hide-cursor() { 
    cursor: none !important; 
}
```




### backface-visibility 属性

**常用  backface-visibility: hidden;**

backface-visibility 属性定义当元素不面向屏幕时是否可见。

如果在旋转元素不希望看到其背面时，该属性很有用。


### transform-style 属性

**常用 transform-style: preserve-3d;**

| 值          | 描述                       |
| ----------- | -------------------------- |
| flat        | 子元素将不保留其 3D 位置。 |
| preserve-3d | 子元素将保留其 3D 位置。   |



**当为元素定义 perspective 属性时，其子元素会获得透视效果，而不是元素本身。**


###  压扁弹起动画

```css
@keyframes rubberBand {
    from {
        transform: scale3d(1,1,1);
    }

    30% {
        transform: scale3d(1.25,0.75,1);
    }

    40% {
        transform: scale3d(0.75,1.25,1);
    }

    50% {
        transform: scale3d(1.15,0.85,1);
    }

    65% {
        transform: scale3d(0.95,1.05,1);
    }

    75% {
        transform: scale3d(1.05,0.95,1);
    }

    to {
        transform: scale3d(1,1,1);
    }
}
```



###   `input`框输入超出宽度



 如何判断input框中输入超出input框的宽度



**使用`clientWidth`与`scrollWidth`对比**



`input`一类标签在渲染文本时,会讲超出部分渲染为横向滚动,并且`scrollWidth`能读到其渲染完毕所需的正常值





核心代码就一句话:



```js
const visible = this.$input.clientWidth < this.$input.scrollWidth

if(visible){
  // TODO something
}
```







### leading-trim 实现垂直居中

[https://juejin.im/post/6882275388920332302?utm_source=gold_browser_extension](https://juejin.im/post/6882275388920332302?utm_source=gold_browser_extension)

```css
h1 { 
 text-edge: cap alphabetic;
 leading-trim: both;
}
```



Text Crop

[http://text-crop.eightshapes.com/](http://text-crop.eightshapes.com/)

Want to rid your components of that pesky space above and below a block of text due to line height

删除你的组件的讨厌的空间上面和下面的文本块由于行高







### **caret-color 光标颜色**

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/caret-color](https://developer.mozilla.org/zh-CN/docs/Web/CSS/caret-color)

**`caret-color`** 属性用来定义**插入光标**（caret）的颜色，这里说的插入光标，就是那个在网页的可编辑器区域内，用来指示用户的输入具体会插入到哪里的那个一闪一闪的形似竖杠 `|` 的东西。

```css
caret-color: red;
```



### 去掉input尾部的箭头

[https://juejin.cn/post/7016476364446367780](https://juejin.cn/post/7016476364446367780)

默认情况下`input type="number"`时尾部会出现小箭头，但是很多时候我们想去掉它，应该怎么办呢？

```html
<input type="number" class="no-arrow" />
```

```css
/* 关键css */
.no-arrow::-webkit-inner-spin-button {
  -webkit-appearance: none;
}
```



### 移除input状态线

[https://juejin.cn/post/7016476364446367780](https://juejin.cn/post/7016476364446367780)

输入框选中时，默认会带蓝色状态线，使用`outline:none`一键移除

```html
<input type="number" class="no-outline" />
```

```css
.no-outline{
  outline: none;
}
```



### 解决IOS滚动条卡顿

在IOS机器上，经常遇到元素滚动时卡顿的情况，只需要一行css即可让其支持弹性滚动

```css
body,html{   
  -webkit-overflow-scrolling: touch;
}
```



### 自定义文本选中的样式

```html
<div class="box">
  <p class="box-default">
    昨天遇见小学同学，没有想到他混的这么差--只放了一块钱到我的碗里
  </p>
  <p class="box--custom">
    今年情人节，不出意外的话，一个人过，出意外的话--去医院过
  </p>
</div>
```

```css
.box-custom::selection {
  color: #ffffff;
  background-color: #ff4c9f;
}
```







### 禁止选择文本

```html
 <div class="box">
  <p>好不容易习惯了自己的长相--去理了个发，又换了一种丑法</p>
  <p>国庆节放假，想跟女朋友去旅游，请大家帮忙推荐下--哪里有女朋友</p>
</div>
```

```css
.box p:last-child{
  user-select: none;
}
```









### 灰色滤镜

```css
body{
  filter: grayscale(1);
}
```





