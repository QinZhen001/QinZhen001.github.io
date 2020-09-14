---
layout:     post
title:      "优化CSS重排重绘与浏览器性能"
date:       2018-03-02 12:04:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 性能优化
---

> “Yeah It's on. ”


## 正文
[网页链接](http://caibaojian.com/css-reflow-repaint.html)


在制作中考虑浏览器的性能，减少重排能够节省浏览器对其子元素及父类元素的重新渲染；避免过分的重绘也能节省浏览器性能；优化动画，使用3D启用GPU硬件加速；慎重选择高消耗的样式，如box-shadow、border-radius、transform、css filters等。


浏览器渲染展示网页的过程，大致分为以下几个步骤：
1. 解析HTML(HTML Parser)
2. 构建DOM树(DOM Tree)
3. 渲染树构建(Render Tree)
4. 绘制渲染树(Painting)



![enter description here][1]


### 慎重选择高消耗的样式
什么 CSS 属性是高消耗的？就是那些绘制前需要浏览器进行大量计算的属性。

* box-shadows
* border-radius
* transparency
* transforms
* CSS filters（性能杀手）




### 什么是reflow
浏览器为了重新渲染部分或整个页面，重新计算页面元素位置和几何结构的进程叫做reflow.

通俗点说就是当开发人员定义好了样式后(也包括浏览器的默认样式),浏览器根据这些来计算并根据结果将元素放到它应该出现的位置上，这个过程叫做reflow.

由于reflow是一种浏览器中的用户拦截操作，所以我们了解如何减少reflow次数，及DOM的层级，css效率对refolw次数的影响是十分有必要的。

reflow(回流)是导致DOM脚本执行效率低的关键因素之一，页面上任何一个节点触发了reflow，会导致它的子节点及祖先节点重新渲染。

简单解释一下 Reflow：当元素改变的时候，将会影响文档内容或结构，或元素位置，此过程称为 Reflow。

```
<body>
  <div class="hello">
    <h4>hello</h4>
    <p><strong>Name:</strong>BDing</p>
    <h5>male</h5>
    <ol>
      <li>coding</li>
      <li>loving</li>
    </ol>
  </div>
</body>
```

当p节点上发生reflow时，hello和body也会重新渲染，甚至h5和ol都会收到影响。

#### 什么时候会导致reflow发生呢？

* 改变窗口大小
* 改变文字大小
* 添加/删除样式表
* 内容的改变，(用户在输入框中写入内容也会)
* 激活伪类，如:hover
* 操作class属性
* 脚本操作DOM
* 计算offsetWidth和offsetHeight
* 设置style属性



#### 常见的重排元素			
width	height	padding	margin
display	border-width	border	top
position	font-size	float	text-align
overflow-y	font-weight	overflow	left
font-family	line-height	vertical-align	right
clear	white-space	bottom	min-height

#### 减少reflow对性能的影响的建议

1. 不要一条一条地修改 DOM 的样式，预先定义好 class，然后修改 DOM 的 className
2. 把 DOM 离线后修改，比如：先把 DOM 给 display:none (有一次 Reflow)，然后你修改100次，然后再把它显示出来
3. 不要把 DOM 结点的属性值放在一个循环里当成循环里的变量
4. 尽可能不要修改影响范围比较大的 DOM
5. 为动画的元素使用绝对定位 absolute / fixed
6. 不要使用 table 布局，可能很小的一个小改动会造成整个 table 的重新布局

----------


1. 尽可能限制reflow的影响范围，尽可能在低层级的DOM节点上，上述例子中，如果你要改变p的样式，class就不要加在div上，通过父元素去影响子元素不好。
2. 避免设置大量的style属性，因为通过设置style属性改变结点样式的话，每一次设置都会触发一次reflow，所以最好是使用class属性
3. 实现元素的动画，它的position属性，最好是设为absoulte或fixed，这样不会影响其他元素的布局
4. 动画实现的速度的选择。比如实现一个动画，以1个像素为单位移动这样最平滑，但是reflow就会过于频繁，大量消耗CPU资源，如果以3个像素为单位移动则会好很多。
5. 不要使用table布局，因为table中某个元素旦触发了reflow，那么整个table的元素都会触发reflow。那么在不得已使用table的场合，可以设置table-layout:auto;或者是table-layout:fixed这样可以让table一行一行的渲染，这种做法也是为了限制reflow的影响范围
6. 如果CSS里面有计算表达式，每次都会重新计算一遍，出发一次reflow




### 什么是repaint
repaint是在一个元素的外观被改变，但没有改变布局的情况下发生的，如改变了visibility、outline、background等。当repaint发生时，浏览器会验证DOM树上所有其他节点的visibility属性。

通俗来说，就是当各种盒子的位置、大小以及其他属性，例如颜色、字体都确定下来后，浏览器便把这些元素都按照各自的特性绘制一遍，于是页面的内容出现了，这个过程叫做repaint

#### 避免过分重绘(Repaints)
当元素改变的时候，将不会影响元素在页面当中的位置（比如 background-color, border-color, visibility），浏览器仅仅会应用新的样式重绘此元素，此过程称为 Repaint。

#### 常见的重绘元素			
color	border-style	visibility	background
text-decoration	background-image	background-position	background-repeat
outline-color	outline	outline-style	border-radius
outline-width	box-shadow	background-size



### 优化动画
css3 动画是优化的重中之重。除了做到上面两点，减少 Reflow 和 Repaints 之外，还需要注意以下方面。



#### transition优化

[https://juejin.im/post/6844904077394984968](https://juejin.im/post/6844904077394984968)



> 在使用height，width，margin，padding作为transition的值时，会造成浏览器主线程的工作量较重，例如从margin-left：-20px渲染到margin-left:0，主线程需要计算样式margin-left:-19px,margin-left:-18px，一直到margin-left:0，而且每一次主线程计算样式后，合成进程都需要绘制到GPU然后再渲染到屏幕上，前后总共进行20次主线程渲染，20次合成线程渲染，20+20次，总计40次计算。



> 而如果使用transform的话，例如tranform:translate(-20px,0)到transform:translate(0,0)，主线程只需要进行一次tranform:translate(-20px,0)到transform:translate(0,0)，然后合成线程去一次将-20px转换到0px，这样的话，总计1+20计算。



**结论：在使用css3 transtion做动画效果时，优先选择transform，尽量不要使用height，width，margin和padding。**



如今，`transform` 是用来制作动画的最佳属性，因为 GPU 可以辅助这项繁重的工作。因此，可以将动画的限制在以下几种类型。

 

- `opacity`
- `translate`
- `rotate`
- `scale`







### 启用 GPU 硬件加速
GPU（Graphics Processing Unit） 是图像处理器。GPU 硬件加速是指应用 GPU 的图形性能对浏览器中的一些图形操作交给 GPU 来完成，因为 GPU 是专门为处理图形而设计，所以它在速度和能耗上更有效率。

GPU 加速可以不仅应用于3D，而且也可以应用于2D。这里， GPU 加速通常包括以下几个部分：Canvas2D，布局合成（Layout Compositing）, CSS3转换（transitions），CSS3 3D变换（transforms），WebGL和视频(video)。

```
/*
 * 根据上面的结论
 * 将 2d transform 换成 3d
 * 就可以强制开启 GPU 加速
 * 提高动画性能
 */
div {
  transform: translate(10px, 10px);
}
div {
  transform: translate3d(10px, 10px, 0);
}
```
需要注意的是，开启硬件加速相应的也会有额外的开销



在 Blink 和 WebKit 浏览器中，会为具有 CSS 过渡或不透明度动画的元素创建新的图层，但是许多开发人员使用`translateZ(0)` 或 `translate3d(0, 0, 0)` 手动强制创建图层。



强制创建图层可确保在动画开始时立即绘制图层并准备就绪（创建和绘制图层是一项非常重要的操作，可能会延迟动画的开始），并且没有由于抗锯齿的变化导致外观上的突然变化。不过，应该谨慎地进行使用它，[过多的图层可能会导致出现 jank](http://wesleyhales.com/blog/2013/10/26/Jank-Busting-Apples-Home-Page/)。




### 浏览器的优化：渲染队列
举个小例子 
比如我们想用js中修改一个div元素的样式 
写下了以下代码
```
div.style.left = '10px';
div.style.top = '10px';
div.style.width = '20px';
div.style.height = '20px';
```
我们修改了元素的left、top、width、height属性 
满足我们发生重排的条件 
理论上会发生4次重排 
但是实际上只会发生1次重排 
这是因为我们现代的浏览器都有渲染队列的机制 
当我改变了元素的一个样式会导致浏览器发生重排或重绘时 
它会进入一个渲染队列 
然后浏览器继续往下看，如果下面还有样式修改 
那么同样入队 
直到下面没有样式修改 
浏览器会按照渲染队列批量执行来优化重排过程，一并修改样式 
这样就把本该4次的重排优化为1次

----------

但是我们现在想要修改样式后在控制台打印
```
div.style.left = '10px';
console.log(div.offsetLeft);
div.style.top = '10px';
console.log(div.offsetTop);
div.style.width = '20px';
console.log(div.offsetWidth);
div.style.height = '20px';
console.log(div.offsetHeight);
```

千万不要写这样的代码，因为发生了4次重排 
有同学可能不懂了，不是说浏览器有渲染队列优化机制吗？ 
为什么这样写就会发生4次重排 
因为offsetLeft/Top/Width/Height非常叼 
**它们会强制刷新队列要求样式修改任务立刻执行 **
想一想其实这么做是有道理的 
毕竟浏览器不确定在接下来的代码中你是否还会修改同样的样式 
为了保证获得正确的值，它不得不立刻执行渲染队列触发重排（错的不是我，是这个世界）

----------


以下属性或方法会刷新渲染队列
* offsetTop、offsetLeft、offsetWidth、offsetHeight
* clientTop、clientLeft、clientWidth、clientHeight
* scrollTop、scrollLeft、scrollWidth、scrollHeight
* getComputedStyle()（IE中currentStyle）

>我们在修改样式过程中，要尽量避免使用上面的属性








### 重绘与重排的性能优化

#### 分离读写操作

了解了原理我们就可以对上面的代码进行优化
```
div.style.left = '10px';
div.style.top = '10px';
div.style.width = '20px';
div.style.height = '20px';
console.log(div.offsetLeft);
console.log(div.offsetTop);
console.log(div.offsetWidth);
console.log(div.offsetHeight);
```

这样就仅仅发生1次重排了，原因相信大家已经很清晰了 
把所有的读操作移到所有写操作之后 
效率高多了 
这是其中一种优化的方法



#### 元素批量修改
现在我们想要向ul中循环添加大量li 
（如果ul还不存在，最好的办法是先循环添加li到ul，然后再把ul添加到文档，1次重排）
```
var ul = document.getElementById('demo');
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    ul.appendChild(li);
}
```
我可以做出下面的优化
```
var ul = document.getElementById('demo');
ul.style.display = 'none'; <--
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    ul.appendChild(li);
}
ul.style.display = 'block'; <--
```

```
var ul = document.getElementById('demo');
var frg = document.createDocumentFragment(); <--
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    frg.appendChild(li); <--
}
ul.appendChild(frg); <--
```


```
var ul = document.getElementById('demo');
var clone = ul.cloneNode(true); <--
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    clone.appendChild(li); <--
}
ul.parentNode.replaceChild(clone,ul); <--
```

----------


[更快地构建 DOM: 使用预解析, async, defer 以及 preload](http://www.zcfy.cc/article/building-the-dom-faster-speculative-parsing-async-defer-and-preload-x2605-mozilla-hacks-8211-the-web-developer-blog-4224.html)








[1]: http://img.caibaojian.com/uploads/2016/11/1478763564983256.png
