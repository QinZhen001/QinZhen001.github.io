---
layout:     post
title:      "vertical-align的一些理解"
date:       2017-09-18 10:58:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.zhangxinxu.com/wordpress/2010/05/%E6%88%91%E5%AF%B9css-vertical-align%E7%9A%84%E4%B8%80%E4%BA%9B%E7%90%86%E8%A7%A3%E4%B8%8E%E8%AE%A4%E8%AF%86%EF%BC%88%E4%B8%80%EF%BC%89/)



vertical-align “垂直的”+“对齐”的意思


* 长度	通过距离升高（正值）或降低（负值）元素。'0cm'等同于'baseline'
* 百分值 – %通过距离（相对于1line-height1值的百分大小）升高（正值）或降低（负值）元素。'0%'等同于'baseline'
* baseline	默认。元素的基线与父元素的基线对齐。
* sub	降低元素的基线到父元素合适的下标位置。
* super	升高元素的基线到父元素合适的上标位置。
* top	把对齐的子元素的顶端与line box顶端对齐。
* text-top	把元素的顶端与父元素内容区域的顶端对齐。
* **middle	元素的中垂点与 父元素的基线加1/2父元素中字母x的高度 对齐。**  (一般使用这个作为垂直居中)
* bottom	把对齐的子元素的底端与line box底端对齐。
* text-bottom	把元素的底端与父元素内容区域的底端对齐。
* inherit	采用父元素相关属性的相同的指定值。


----------



.test{vertical-align:-2px;}
元素相对于基线向下偏移两像素，这个常常用来修复单选框/复选框与12像素文字大小不对齐的问题




**vertical- align属性的百分比值是相对于line-height的计算值计算的**



### 为什么我的vertical-align属性不起作用
vertical-align 称之为“inline-block依赖型元素

**只有一个元素属于inline或是inline-block（table-cell也可以理解为inline-block水平）水平，其身上的vertical-align属性才会起作用**。所以，类似下面的代码就不会起作用：

div{vertical-align:middle;}


----------

所谓inline-block水平的元素，就是既可以“吸”又可以“咬”的元素，既可以与inline水平元素混排，又能设置高宽属性的元素。哪些元素呢，例如图片，按钮，单复选框，单行/多行文本框等HTML控件，只有这些元素默认情况下会对vertical-align属性起作用。


虽然vertical-align属性只会在inline-block水平的元素上期作用，但是其影响到的元素涉及到inline属性的元素，这里千万记住，inline水平元素受vertical-align属性而位置改变等不是因为其对vertical-align属性敏感或起作用，而是受制于整个line box的变化而不得不变化的


#### 小例子


```css
.box {   
    height: 128px;
} 
.box > img { 
    height: 96px; 
    vertical-align: middle; 
} 
```

```html
<div class="box"> 
    <img src="1.jpg"> 
</div>
```






此时图片顶着.box元素的上边缘显示，根本没垂直居中，完全没起作用！ 


这种情况看上去是 vertical-align:middle 没起作用，实际上，vertical-align 是在努力地渲染的，只是行框盒子前面的“幽灵空白节点”高度太小，如果我们通过设置一个 足够大的行高让“幽灵空白节点”高度足够，就会看到vertical-align:middle起作用了



```css
.box {  
    height: 128px; 
    line-height: 128px;   /* 关键CSS属性 */
} 
.box > img {  
    height: 96px;  
    vertical-align: middle;
} 
```




### display:table-cell无视行高

对table-cell元素而言，vertical-align起作用的是table-cell 元素自身。

```css
.cell {  
    height: 128px;  
    display: table-cell;
}
.cell > img { 
    height: 96px; 
    vertical-align: middle; 
} 
```

```
<div class="cell">  
    <img src="1.jpg">   
</div> 
```


结果图片并没有要垂直居中的迹象，还是紧贴着父元素的上边缘


但是，如果 vertical-align:middle 是设置在 table-cell 元素上，CSS 代码 如下： 

```css
.cell {
      height: 128px;
      display: table-cell;
      vertical-align: middle;
    }

    .cell > img {
      height: 96px;
    }
```

那么图片就可以垂直居中了



**虽然就效果而言，table-cell元素设置vertical-align垂 直对齐的是子元素，但是其作用的并不是子元素，而是table-cell元素自 身。**就算table-cell元素的子元素是一个块级元素，也一样可以让其有各 种垂直对齐表现。








### vertical-align:middle
[测试vertical-align:middle](http://www.zhangxinxu.com/study/201005/verticle-align-test-demo.html)

**vertical-align:middle属性的表现与否，仅仅与其父标签有关，至于我们通常看到的与后面的文字垂直居中显示那都是假象！**





### vertical-align 和 line-height 之间的关系 

vertical-align和line-height之间的关系很明确，即“朋友”关系。 

只要出现内联元素，这对好朋友一定会同时出现

容器高度不等于行高的例子
[http://demo.cssworld.cn/5/3-1.php](http://demo.cssworld.cn/5/3-1.php)

为什么会出现这样的现象?

看一下相关的代码： 
```
.box { line-height: 32px; }
.box > span { font-size: 24px; }

<div class="box">  
    <span>文字</span>
</div> 
```


其中有一个很关键的点，那就是24px的font-size大小是设置在`<span>`元素上的，这就导 致了外部`<div>`元素的字体大小和`<span>`元素有较大出入


`<span>`标签前面实际上有一个看不见的类似 字符的“幽灵空白节点”。看不见的东西不利于理解，因此我们不妨使用一个看得见的字符 x 占位，同时“文字”后面也添加一个 x，便于看出基线位置，于是就有如下 HTML： 

```
<div class="box">   
    x<span>文字x</span>
</div> 
```

此时，我们可以明显看到两处大小完全不同的文字。一处是字母 x 构成了一个“匿名内联 盒子”，另一处是“文字 x”所在的`<span>`元素，构成了一个“内联盒子”


由于都受 line- height:32px影响，因此，这两个“内联盒子”的高度都是32px。下面关键的来了，对字符 而言，font-size 越大字符的基线位置越往下，因为文字默认全部都是基线对齐，所以当字 号大小不一样的两个文字在一起的时候，彼此就会发生上下位移，如果位移距离足够大，就会 超过行高的限制，而导致出现意料之外的高度



[如图 5-25 所示](https://github.com/QinZhen001/QinZhen001.github.io/blob/master/img/in-post/line-height.png)


非常直观地说明了为何后容器的高度会是 36px，而非 line-height 设置的 32px。 


知道了问题发生的原因，那问题就很好解决了。我们可以让“幽灵空白节点”和后面`<span>` 元素字号一样大，也就是： 

```
.box {  
    line-height: 32px;
    font-size: 24px; 
} 
.box > span { }
```

**或者改变垂直对齐方式**，如顶部对齐，这样就不会有参差位移了(**这种方法非常常见**)： 

```
.box { 
    line-height: 32px;
} 
.box > span { 
    font-size: 24px;  
    vertical-align: top; 
} 
```



### 块级元素内图片高度无法100%


搞清楚了大小字号文字的高度问题，对更为常见的图片底部留有间隙的问题的理解就容易 多了。


现象是这样的：任意一个块级元素，里面若有图片，则块级元素高度基本上都要比图片 的高度高


```
    .box {
      width: 280px;
      outline: 1px solid #aaa;
      text-align: center;
    }

    .box > img {
      height: 96px;
    }
```

```
<div class="box">
  <img src="./test.png" alt="">
</div>
```

间隙产生的三大元凶就是“幽灵空白节点”、line-height和vertical-align属性


为了直观演示原理，我们可以在图片前面辅助一个字符 x 代替“幽灵空白节点”，并想办法通过 背景色显示其行高范围，于是，大家就会看到如图 5-27 所示的现象。 

[图 5-27](https://github.com/QinZhen001/QinZhen001.github.io/blob/master/img/in-post/pic-gap.png)


当前line-height计算值是20px，而 font-size只有14px，因此，字母 x 往下一定 有至少3px的半行间距（具体大小与字体有关），而图片作为替换元素其基线是自身的下边缘。根据定义，默认和基线（也就是这里字母 x 的下边缘）对齐，字母 x 往下的行高产生的多余的 间隙就嫁祸到图片下面，让人以为是图片产生的间隙，实际上，是“幽灵空白节点”、 line-height和vertical-align属性共同作用的结果。  



知道了原理，要清除该间隙，就知道如何对症下药了。方法很多，具体如下

1. 图片块状化。可以一口气干掉“幽灵空白节点” 、line-height 和 vertical- align
2. 容器line-height足够小。只要半行间距小到字母 x 的下边缘位置或者再往上，自 然就没有了撑开底部间隙高度空间了。比方说，容器设置line-height:0。 
3. 容器font-size足够小。此方法要想生效，需要容器的line-height属性值和当 前font-size相关，如line-height:1.5或者line-height:150%之类；否则只会让下 面的间隙变得更大，因为基线位置因字符 x 变小而往上升了。 
4. 图片设置其他vertical-align属性值。间隙的产生原因之一就 是基线对齐，所以我们设置 vertical-align 的值为 top、middle、 bottom中的任意一个都是可以的。 





## 深入理解 vertical-align 线性类属性值 



### inline-block 与 baseline 

vertical-align属性的默认值baseline在文本之类的内联元素那里就是字符 x 的下 边缘，对于替换元素则是替换元素的下边缘


**但是，如果是 inline-block 元素，则规则要 复杂了：一个inline-block元素，如果里面没有内联元素，或者overflow不是visible， 则该元素的基线就是其margin底边缘；否则其基线就是元素里面最后一行内联元素的基线。** 

举个例子:


两个同尺寸的 inline-block 水平元素，唯一区别就是一个是空的，一个里面有字符， 代码如下： 


```
.dib-baseline {  
    display: inline-block;   
    width: 150px; height: 150px;  
    border: 1px solid #cad5eb; 
    background-color: #f0f3f9;
}
```

```
<span class="dib-baseline"></span> 
<span class="dib-baseline">x-baseline</span> 
```


你会发现，明明尺寸、display水平都是一样的，结果两个却不在一个水平线上对齐


第一个框里面没有内联元素，因此基线就是容器的margin 下边缘，也就是下边框下面的位置；而第二个框里面有字符，纯正的内联元素，因此 第二个框就是这些字符的基线，也就是字母 x 的下边缘了。于是，我们就看到了左边框框下边 缘和右边框框里面字符 x 底边对齐的好戏。 



### vertial-align:top/bottom 

vertial-align:top

用通俗的话解释就是：如果是内联元素，则和这一行位置高的内联元素的顶部对齐； 如果 display 计算值是 table-cell 的元素，我们不妨脑补成`<td>`元素，则和`<tr>`元素上边缘对齐。 



vertial-align:bottom 声明与此类似，只是把“顶部”换成“底部”，把“上边缘” 换成“下边缘”。



需要注意的是，内联元素的上下边缘对齐的这个“边缘”是当前“行框盒子”的上下边缘， 并不是块状容器的上下边缘。 

### vertial-align:text-top/text-bottom 
[http://demo.cssworld.cn/5/3-9.php](http://demo.cssworld.cn/5/3-9.php)


* vertical-align:text-top：盒子的顶部和父级内容区域的顶部对齐。 
* vertical-align:text-bottom：盒子的底部和父级内容区域的底部对齐

其中，理解的难点在于“父级内容区域”，这是个什么东西呢？ 


可以看成是 Firefox/IE 浏览器文本选中 的背景区域，或者默认状态下的内联文本的背景色区域。而所谓“父级内容区域”指的就是在父级元素当前font-size和font-family下应有的内容区域大小。 





## 补充
[http://image.zhangxinxu.com/flash/blog/201006/vertical-align-text-top.swf](http://image.zhangxinxu.com/flash/blog/201006/vertical-align-text-top.swf)




### Tip
元素浮动之后就会变为块元素，即 display 属性为 block ，所以 vertical-align 属性就会不起作用。

* 一个元素属于inline或是inline-block其身上的vertical-align属性才会起作用
* 一个元素属于block其身上的text-align才会生效 **(多用于text-align: center)**




### 幽灵空白节点
[http://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/](http://www.zhangxinxu.com/wordpress/2015/08/css-deep-understand-vertical-align-and-line-height/)

「幽灵空白节点」是个什么意思呢？
**在HTML5文档声明下，块状元素内部的内联元素的行为表现，就好像块状元素内部还有一个（更有可能两个-前后）看不见摸不着没有宽度没有实体的空白节点，这个假想又似乎存在的空白节点，我称之为“幽灵空白节点”。**

vertical-align默认值是baseline, 也就是基线对齐.






**一个inline-block元素，如果里面没有inline内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘，否则，其基线就是元素里面最后一行内联元素的基线。**

两个同尺寸的inline-block水平元素，唯一区别就是一个空的，一个里面有字符，代码如下：
```
.dib-baseline {
  display: inline-block; width: 150px; height: 150px;
  border: 1px solid #cad5eb; background-color: #f0f3f9;
}

<span class="dib-baseline"></span>
<span class="dib-baseline">x-baseline</span>
```

会发现，明明尺寸、display水平都是一样的，结果呢，两个却不在一个水平线上对齐，为什么呢？哈哈，上面的规范已经说明了一切。第一个框框里面没有内联元素，因此，基线就是容器的margin下边缘，也就是下边框下面的位置；而第二个框框里面有字符，纯正的内联元素，因此，第二个框框就是这些字符的基线，也就是字母x的下边缘了。于是，我们就看到了框框1下边缘和框框2里面字符x底边对齐的好戏。框框2有个小彩蛋，点击可以toggle其innerHTML，会发现，如果框框2里面没文字，就和框框1举案齐眉了。



