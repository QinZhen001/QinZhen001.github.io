---
layout:     post
title:      "css中line boxes盒子模型"
date:       2018-06-03 22:00:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.zhangxinxu.com/wordpress/2010/01/css-float%e6%b5%ae%e5%8a%a8%e7%9a%84%e6%b7%b1%e5%85%a5%e7%a0%94%e7%a9%b6%e3%80%81%e8%af%a6%e8%a7%a3%e5%8f%8a%e6%8b%93%e5%b1%95%e4%b8%80/)

先看下面一段普通的HTML代码：
```html
<p>这是一行普通的文字，这里有个 <em>em</em> 标签。</p>
```

1. 首先是`<p>`标签所在的containing box，此box包含了其他的boxes； 
2. 然后就是inline boxes，如下图标注，inline boxes不会让内容成块显示，而是排成一行，如果外部含inline属性的标签(`<span>, <a>, <cite>`等)，则属于inline boxes，如果是个光秃秃的文字，则属于匿名inline boxes。 
3. line boxes。在containing boxes里，一个一个的inline boxes组成了line boxes。这是浮动影响布局的关键box类型
4. content area。content area 是一种围绕文字看不见的box。content area的大小与font-size大小相关。 

![enter description here][1]


![enter description here][2]



![enter description here][3]




默认情况下，图片与文字混排应该是这个样子：图片与文字基线对齐，图片与文字在同一行上，如下图所示：

![enter description here][4]




上图中，图片为一个inline boxes，两边的文字也是inline boxes。**由于line boxes的高度是由其内部最高的inline boxes的高度决定的**，所以这里line boxes的高度就是图片的高度。此时图片与文字是同一box类型的元素（都是inline boxes），是在同一行上的，所以，默认状态下，一张图片只能与一行文字对齐。而要想让一张图片要与多行文字对齐，您唯一能做的就是破坏正常的line boxes模型。



### 含有浮动属性的图片与文字
先看一下图片添加了float:left样式后的表现


![enter description here][5]


刚才说过，正常情况下，图片自身就是个inline boxes，与两侧的文字inline boxes共同组成了line boxes，但是，一旦图片加入了浮动，情况就完全变了。浮动彻底破坏了img图片的inline boxes特性。一旦图片失去了inline boxes特性就无法与inline boxes的文字排在一行了，其会从line boxes上脱离出来，跟随自身的方位属性，靠边排列。

----------


在目前的CSS的世界中，所有的高度都是有两个CSS模型产生的，一个是box盒状模型，对应CSS为”height+padding+margin“，另外一个是line box模型，对应样式为"line-height"。


前者的height属性分为明显的height值和隐藏的height值，所谓隐藏的height值是指图片的高度，一旦载入一张图片，其内在的height值就会起作用，即使您看不到"height"这个词。

而后者针对于文字等这类inline boxes的元素（图片也属于inline boxes，但其height比line-height作用更凶猛，故其inline boxes高度等于其自身高度，对line-height无反应），**inline boxes的高度直接受line-height控制**（改变line-height文字拉开或重叠就是这个原因），而真正的高度表现则是由每行众多的inline boxes组成的line boxes（等于内部最高的inline box的高度），而这些line boxes的高度垂直堆叠形成了containing box的高度，也就是我们见到的`<div>`或是`<p>`标签之类的高度了。




对于line box模型的元素而言，没有inline boxes，就没有高度了，而浮动却恰恰做了这么龌龊的事情，其直接将元素的inline boxes也破坏了，于是这些元素也就没有了高度。 





###  **inline-block和baseline**



**一个inline-block元素，如果里面没有inline内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘，否则，其基线就是元素里面最后一行内联元素的基线。**

纳尼，还是没反应过来？

那我们看下面这个例子，应该就知道什么意思了。

两个同尺寸的`inline-block`水平元素，唯一区别就是一个空的，一个里面有字符，代码如下：

```html
.dib-baseline {
  display: inline-block; width: 150px; height: 150px;
  border: 1px solid #cad5eb; background-color: #f0f3f9;
}

<span class="dib-baseline"></span>
<span class="dib-baseline">x-baseline</span>
```





![](https://s1.ax1x.com/2020/04/10/Go4ujO.png)



   会发现，明明尺寸、display水平都是一样的，结果呢，两个却不在一个水平线上对齐，为什么呢？哈哈，上面的规范已经说明了一切。第一个框框里面没有内联元素，因此，基线就是容器的margin下边缘，也就是下边框下面的位置；而第二个框框里面有字符，纯正的内联元素，因此，第二个框框就是这些字符的基线，也就是字母x的下边缘了 



-----





 下面我们要做一件很有必要的事情，用来帮助我们理解上面复杂例子在`line-height`值为`0`后的表现，什么事情呢？哈，同境界模拟，我们也设置框框2的`line-height`值为`0`，于是，就会是下面这样的表现： 





![](https://s1.ax1x.com/2020/04/10/Go4t8P.png)



知道框框2为何又下沉了一点吗？





 **因为字符实际占据的高度是由行高决定的，当行高变成0的时候，字符占据的高度也是`0`**，此时，高度的起始位置就变成了字符content area的垂直中心位置，于是，文字就一半落在看看2的外面了。















[1]: http://image.zhangxinxu.com/image/blog/201001/2010-01-20_220341.png
[2]: http://image.zhangxinxu.com/image/blog/201001/2010-01-20_221641.png
[3]: http://image.zhangxinxu.com/image/blog/201001/2010-01-20_223108.png
[4]: http://image.zhangxinxu.com/image/blog/201001/2010-01-20_230801.png
[5]: http://image.zhangxinxu.com/image/blog/201001/2010-01-20_234149.png