---
layout:     post
title:      "深入理解z-index"
date:       2017-10-19 23:27:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
 
[网页链接](http://www.w3cplus.com/css/what-no-one-told-you-about-z-index.html)


### 堆叠顺序
z-index看上去很简单，z-index值大的元素在z-index值小的元素前面，对吧？但其实这只是z-index的一部分用法。

HTML中的每一元素都是在其他元素的前面或者后面。这是众所周知的堆叠顺序（Stacking Order），这条规则在w3c规范里面说的很清楚，大部分程序猿并不真正理解。

如果没有涉及z-index和position属性的话，那规则很简单，堆叠顺序就是元素在HTML中出现的顺序。(当然如果你对行内元素使用负margin的话，可能情况会复杂一些。)

加上position属性的话，就是所有定位了得元素在没有被定位的元素前面。（一个元素被定位的意思这里指的是它有一个position属性，但是不是static，而是relative,absolute等）

**再加上z-index属性，事情就变得有点诡异。首先z-index值越大，越靠前。但是z-index属性只作用在被定位了的元素上。所以如果你在一个没被定位的元素上使用z-index的话，是不会有效果的。还有就是z-index会创建一个堆叠的上下文（Stacking Contexts），我们可以理解为一个层。**


![enter description here][1]

>事实上，大多数的一切都比z-index为0的层叠等级低。

### 堆叠上下文

同一个父元素下面的元素会受父元素的堆叠顺序影响，所以堆叠上下文是我们理解z-index和堆叠顺序的关键。（下面为了简化，我们称堆叠上下文为层。）

每一个层都有唯一的根节点。当一个元素创建一个层，那么它的所有子元素都会受到父元素的堆叠顺序影响。意味着**如果一个元素位于一个最低位置的层，那你z-index设置得再大，它也不会出现在其它层元素的上面。**

现在我们来说说什么情况下会产生新的堆叠上下文：

1. 当一个元素位于HTML文档的最外层`（<html>元素）`
2. 当一个元素被定位了并且拥有一个z-index值（不为auto）
3. 当一个元素被设置了opacity，transform, filter, css-regions, paged media,perspective,clip-path,mask / mask-image / mask-border等属性。

一二条规则，Web开发者都知道，虽然他们不一定知道怎么描述

最后一条，是很多非w3c规范里面的文章很少提到的。通常来讲，如果一个CSS属性需要做一些特效的话，它都会创建一个新的层。

### 同一层里面的堆叠顺序

下面是同一层里面的堆叠顺序（从后到前）：

* 层的根元素
* 被定位了的元素并且z-index值为负，相同z-index的情况下，按照HTML元素的书写顺序排列，下面相同。
* 没有被定位的元素
* 被定位的元素，并且z-index值为auto
* 被定位了的元素并且z-index值为正。

注意：z-index值为负的元素比较特殊，他们会先被绘制，意味着他们可以出现在其他元素的后面，甚至出现在它的父元素后面。但是必要条件是该元素必须与父元素处于同一层，并且父元素不是这个层的根元素。一个很好的例子

理解了如何和什么时候会产生一个新的层，那么下次如果你遇到z-index值设了很大，但是不起作用的话就去看看它的祖先是否产生了一个新的层。

## 总结

说了这么多，我们来给之前的代码加上堆叠顺序。
```
<div><!-- 1 -->
  <span class="red"><!-- 6 --></span>
</div>
<div><!-- 2 -->
  <span class="green"><!-- 4 --><span>
</div>
<div><!-- 3 -->
  <span class="blue"><!-- 5 --></span>
</div>
```
当我们设置了opacity之后变成下面这样。
```
<div><!-- 1 -->
  <span class="red"><!-- 1.1 --></span>
</div>
<div><!-- 2 -->
  <span class="green"><!-- 4 --><span>
</div>
<div><!-- 3 -->
  <span class="blue"><!-- 5 --></span>
</div>
```
红色的`<span>`从6变成1.1，我用'.'来标记它是新生成的层里面的第一个元素。


最后我们来总结一下为什么红色的`<span>`会去到下面： 一开始有两个层，一个由根节点产生，一个由设置了z-index:1并且position:absolute的红色`<span>`产生。当我们设置了opacity时，产生了第三个层，并且第三个层把红色`<span>`产生的层包裹了，意味着刚开始的z-index的作用域只在第三个层里面。而所有的`<div>`都没有定位或者z-index，所以他们的堆叠顺序按照HTML出现顺序排列，于是第三个层就去到下面。


----------

其他例子：


```
<div class="one">
  <div class="two"></div>
  <div class="three"></div>
</div>
<div class="four"></div>
```
CSS:
```
div {
  width: 200px;
  height: 200px;
  padding: 20px;
}
 
.one, .two, .three, .four {
  position: absolute;
}
  
.one {
  background: #f00;
  outline: 5px solid #000;
  top: 100px;
  left: 200px;
  z-index: 10;
}
  
.two {
  background: #0f0;
  outline: 5px solid #000;
  top: 50px;
  left: 75px;
  z-index: 100;
}
 
.three {
  background: #0ff;
  outline: 5px solid #000;
  top: 125px;
  left: 25px;
  z-index: 150;
}
 
.four {
  background: #00f;
  outline: 5px solid #ff0;
  top: 200px;
  left: 350px;
  z-index: 50;
}
```


尽管div.two有着更大的z-index (100)，它实际上比同一页面上的div.four (z-index为50) 位置更低。 你可以在下面的图中看到上面代码的结果。 黑色和黄色的边框表示着每个元素所处的不同的层叠上下文。



![enter description here][2]



由于div.two被包含在div.one中，它的z-index值也是相对于div.one的层叠上下文来说的。 事实上，我们真正得到的是如下结果：

.one — z-index = 10
.two — z-index = 10.100
.three — z-index = 10.150
.four — z-index = 50








商业转载请联系作者获得授权,非商业转载请注明出处。

原文: http://www.w3cplus.com/css/what-no-one-told-you-about-z-index.html






## 补充
[https://zhuanlan.zhihu.com/p/33984503](https://zhuanlan.zhihu.com/p/33984503)




![enter description here][3]

**设置了 position: relative 属性后，元素 z-index:auto 生效导致层叠水平提升，比普通内联元素来的高**


### z-index 基础
**z-index 的默认值为 auto**

**只有定位元素(position:relative/absolute/fixed)的 z-index 才有作用**

**如果你的 z-index 作用于一个非定位元素(一些 CSS3 也会生效)，是不起任何作用的**



#### 元素层叠水平相当
那么当两个元素层叠水平相同的时候，这时候就要遵循下面两个准则：

1. 后来居上原则
2. 谁 z-index 大，谁在上的准则



#### 不同的层叠上下文
这个就比较复杂了，可以总结成一句话：打狗还得看主人



## 最佳实践
1. 不犯二准则：对于非浮层元素，避免设置 z-index 值，z-index 值没有任何道理需要超过 2
2. 对于浮层元素，可以通过 JS 获取 body 下子元素的最大 z-index 值，然后在此基础上加 1 作为浮层元素的 z-index 值


对于非浮层元素，不要过多地去运用 z-index 去调整显示顺序，要灵活地去运用层叠水平和后来居上的准则去让元素获得正确的显示，如果是在要设置 z-index 去调整，不建议非浮层元素 z-index 数值超过 2，对于 DOM 元素，-1, 0, 1, 2 足够让元素有正确的显示顺序。

对于浮层元素，往往是第三方组件开发，当你无法确认你的浮层是否会百分百覆盖在 DOM 树上的时候，你可以去动态获取页面 body 元素下所有子元素 z-index 的最大值，在此基础加一作为浮层元素 z-index 值，用于保证该浮层元素能够显示在最上方。



### 网页层次结构（css也会对网页的分层策略产生重要影响）

对于一个html文件webkit会为某些元素和它的子节点建立新层，这样webkit可以单独对某层操作提升性能，下列情况会产生新层。

1. video标签 – webkit在新层中有效的处理视频解码器和浏览器之间的交互和渲染问题。
2. div、p等普通标签 – 涉及到3D变换时。
3. canvas标签 – 复杂的2D和3D绘图操作。








  [1]: https://cdn.tutsplus.com/webdesign/uploads/2013/11/stacking-order1.png
  [2]: https://cdn.tutsplus.com/webdesign/uploads/2013/11/stacking1.png
  [3]: https://pic3.zhimg.com/80/v2-1ec9491a660c0e11b7272633976da869_hd.jpg