---
layout:     post
title:      "flex布局"
date:       2017-08-04 23:02:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.jianshu.com/p/07e0c16a4ff5#)



弹性布局（flexible box）模块旨在提供一个更加有效的方式来布置，对齐和分布在容器之间的各项内容，即使它们的大小是未知或者动态变化的。

弹性布局的主要思想是让容器有能力来改变项目的宽度和高度，以填满可用空间（主要是为了容纳所有类型的显示设备和屏幕尺寸）的能力。


![enter description here][1]



### **flex-direction (适用于父类容器的元素上)**

flex-direction: row | row-reverse | column | column-reverse

* row：横向从左到右排列（左对齐），默认的排列方式。
* row-reverse：反转横向排列（右对齐，从后往前排，最后一项排在最前面。
* column：纵向排列。
* column-reverse：反转纵向排列，从后往前排，最后一项排在最上面。


----------

### **flex-wrap (适用于父类容器上)**
设置或检索伸缩盒对象的子元素超出父容器时是否换行。

flex-wrap: nowrap | wrap | wrap-reverse

* nowrap：当子元素溢出父容器时不换行。
* wrap：当子元素溢出父容器时自动换行。
* wrap-reverse：反转 wrap 排列。

----------

### **flex-flow (适用于父类容器上)**
复合属性。设置或检索伸缩盒对象的子元素排列方式。

flex-flow: `<‘flex-direction’>` 和 `<‘flex-wrap’>`

* [ flex-direction ]：定义弹性盒子元素的排列方向。
* [ flex-wrap ]：定义弹性盒子元素溢出父容器时是否换行

 [flex-flow的demo](http://caibaojian.com/demo/flexbox/flex-flow.html)


----------

### **justify-content (适用于父类容器上)**
设置或检索弹性盒子元素在**主轴（横轴）方向上**的对齐方式。

当弹性盒里一行上的所有子元素都不能伸缩或已经达到其最大值时，这一属性可协助对多余的空间进行分配。当元素溢出某行时，这一属性同样会在对齐上进行控制。

justify-content: flex-start | flex-end | center | space-between | space-around


* flex-start：弹性盒子元素将向行起始位置对齐。该行的第一个子元素的主起始位置的边界将与该行的主起始位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。
* flex-end：弹性盒子元素将向行结束位置对齐。该行的第一个子元素的主结束位置的边界将与该行的主结束位置的边界对齐，同时所有后续的伸缩盒项目与其前一个项目对齐。
* center：弹性盒子元素将向行中间位置对齐。该行的子元素将相互对齐并在行中居中对齐，同时第一个元素与行的主起始位置的边距等同与最后一个元素与行的主结束位置的边距（如果剩余空间是负数，则保持两端相等长度的溢出）。
* space-between：弹性盒子元素会平均地分布在行里。如果最左边的剩余空间是负数，或该行只有一个子元素，则该值等效于'flex-start'。在其它情况下，第一个元素的边界与行的主起始位置的边界对齐，同时最后一个元素的边界与行的主结束位置的边距对齐，而剩余的伸缩盒项目则平均分布，并确保两两之间的空白空间相等。
* space-around：弹性盒子元素会平均地分布在行里，两端保留子元素与子元素之间间距大小的一半。如果最左边的剩余空间是负数，或该行只有一个伸缩盒项目，则该值等效于'center'。在其它情况下，伸缩盒项目则平均分布，并确保两两之间的空白空间相等，同时第一个元素前的空间以及最后一个元素后的空间为其他空白空间的一半。





----------
### **align-items (适用于父类容器上)**
设置或检索弹性盒子元素在**侧轴（纵轴）方向**上的对齐方式。

>联想记忆：vertical-align => 垂直方向对齐方式 => align-items

align-items: flex-start | flex-end | center | baseline | stretch

* flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴（纵轴）起始边界。
* flex-end：弹性盒子元素的侧轴（纵轴）结束位置的边界紧靠住父容器的侧轴结束边界。
* center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
* baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
* stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

![align-items][3]

----------

### **align-self (适用于弹性盒模型子元素)**
设置或检索弹性盒子元素自身在侧轴（纵轴）方向上的对齐方式。

align-self: auto | flex-start | flex-end | center | baseline | stretch

* auto：如果'align-self'的值为'auto'，则其计算值为元素的父元素的'align-items'值，如果其没有父元素，则计算值为'stretch'。
* flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
* flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
* center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
* baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

----------
**order (适用于弹性盒模型容器子元素)**
order: `<integer>`
`<integer>`：用整数值来定义排列顺序，数值小的排在前面。可以为负值。

----------


**flex:0 1 auto;**

* flex-grow:0;	定义弹性盒子项的拉伸因子，即子项分配父项剩余空间的比，默认值为 0
* flex-shrink:1;	指定了 flex 元素的收缩规则，子项的收缩所占的份数，默认值为1 
[ 当所有子项相加的宽度大于父项的宽度，每个子项减少的多出的父项宽度的 1/n ]
* felx-basis:auto;	指定了 flex 元素在主轴方向上的初始大小，即子项的宽度



### flex-basis和width的关系

**在Flex布局中，子项设置width是没有直接效果的。**



同时，在Flex布局中，**flex-basis优先级是比width高的**（可以理解为覆盖）。



```scs
by-zhangxinxu {
    display: flex;
}
item-basis-width {
    padding: 1em;
    border: 1px solid deepskyblue;
    color: deepskyblue;
    box-sizing: border-box;
    width: 200px;
    flex-basis: 100px;
}
```



结果`width:200px`完全是一个盒饭角色，所谓盒饭角色，就是明明参与了这部电视剧，也吃了剧组的盒饭，但是观众完全就没有意识到他的存在。这里的`width`属性就是这种性质，代码中确实出现过，但是样式渲染却完全体现不出来。

所以，宽度表现为`100px`。



`flex-basis:auto`的含义是，子项的基本尺寸根据其自身的尺寸决定。而这个自身尺寸与下面这几个方面有关：

- `box-sizing`盒模型（这个已经介绍过了）；
- `width`/`min-width`/`max-width`等CSS属性设置；
- `content`内容（min-content最小宽度）；



[flex-basis和width不同表现](https://www.zhangxinxu.com/wordpress/2019/12/css-flex-basis/)



更深入的案例：最小内容宽度、width和flex-basis同时满足



根据上面的分析（不考虑容器尺寸不足或溢出），我们可以得到结论：

- `width:100px` + `flex-basis:auto` = 元素自身100px
- content + `flex-basis:100px` = max(content, flex-basis) = 大于等于100px



再加上**flex-basis优先级是比width高**的结论，我们就可以得到：

- content + `width:100px` + `flex-basis:100px` = content + `flex-basis:100px` = max(content, flex-basis) = 大于等于100px







## 常用例子

### 居中对齐
```scss
.flex-container {
  /* We first create a flex layout context */
  display: flex;

  /* Then we define the flow direction and if we allow the items to wrap 
   * Remember this is the same as:
   * flex-direction: row;
   * flex-wrap: wrap;
   */
  flex-flow: row wrap;

  /* Then we define how is distributed the remaining space */
  justify-content: space-around;
}
```

* 通过设置父类容器的css代码控制子元素的排列方式（flex-direction:row）从左到右（默认方式）。
* 子元素超出内容时是否换行。flex-wrap:wrap（采用换行的方式）。合起来就是flex-flow:row wrap
* 设置子元素的弹性盒堆叠伸缩行的对齐方式为在盒子中平局分布 justify-content:space-around


[居中对齐demo](http://caibaojian.com/demo/flexbox/flexbox3.html)

### 自适应导航
```scss
/* Large */
.navigation {
  display: flex;
  flex-flow: row wrap;
  /* This aligns items to the end line on main-axis */
  justify-content: flex-end;
}

/* Medium screens */
@media all and (max-width: 800px) {
  .navigation {
    /* When on medium sized screens, we center it by evenly distributing empty space around items */
    justify-content: space-around;
  }
}

/* Small screens */
@media all and (max-width: 500px) {
  .navigation {
    /* On small screens, we are no longer using row direction but column */
    flex-direction: column;
  }
}
```




* 设置子元素为从左到右（flex-direction:row），内容超出换行（flex-wrap:wrap）.
* 设置子元素的内容向右对齐（justify-content:flex-end）
* 当小于800px时，内容为居中，当小于500px时，内容纵向排列，从上到下。



[自适应导航demo](http://caibaojian.com/demo/flexbox/flexbox4.html#)

### 常见3栏移动优先布局
```scss
.wrapper {
  display: flex;
  flex-flow: row wrap;
}

/* We tell all items to be 100% width */
.header, .main, .nav, .aside, .footer {
  flex: 1 100%;
}

/* We rely on source order for mobile-first approach
 * in this case:
 * 1. header
 * 2. nav
 * 3. main
 * 4. aside
 * 5. footer
 */

/* Medium screens */
@media all and (min-width: 600px) {
  /* We tell both sidebars to share a row */
  .aside { flex: 1 auto; }
}

/* Large screens */
@media all and (min-width: 800px) {
  /* We invert order of first sidebar and main
   * And tell the main element to take twice as much width as the other two sidebars 
   */
  .main { flex: 2 0px; }

  .aside-1 { order: 1; }
  .main    { order: 2; }
  .aside-2 { order: 3; }
  .footer  { order: 4; }
}
```

*设置子元素从左到右，超出换行（flex-flow:row wrap;）。
默认情况下所有子元素拓展比例为1（flex-grow:1），伸缩比例为100%（flex-basis:100%）。

* 大于800px时，.main的拓展比例为2.伸缩值为0（flex-basis:0px）,并且侧栏aside-1排列在第一位，main在第二位，aside-2在第三位。
* 大于600时。.aside元素的拓展比例为1（flex-grow:1），伸缩比例为auto（flex-basis:auto）。

[3栏移动优先布局demo](http://caibaojian.com/demo/flexbox/flexbox5.html)

## 后记
flex 属性，是 flex-grow 、flex-shrink 和 flex-basis 属性的简写，描述弹性项目的整体的伸缩性

display:flex; 设置在外层容器父级，表示该容器使用弹性盒布局方式


----------


* **如果缩写flex:1,则其计算值为：1 1 0** 
* **如果缩写flex:none,则计算值为 0 0 auto**
* **如果缩写flex:auto,则计算值为 1 1 auto**


当 flex 取值为一个长度或百分比，则视为 flex-basis 值，flex-grow 取 1，flex-shrink 取 1，有如下等同情况（注意 0% 是一个百分比而不是一个非负数字）：
```
.item-1 {flex: 0%;}
.item-1 {
flex-grow: 1;
flex-shrink: 1;
flex-basis: 0%;
}
.item-2 {flex: 24px;}
.item-1 {
flex-grow: 1;
flex-shrink: 1;
flex-basis: 24px;
}
```

当 flex 取值为一个非负数字，则**该数字为 flex-grow** 值，flex-shrink 取 1，flex-basis 取 0%，如下是等同的：
```
.item {flex: 1;}
.item {
    flex-grow: 1;
    flex-shrink: 1;
    flex-basis: 0%;
}
```


当 flex 取值为两个非负数字，则分别视为 flex-grow 和 flex-shrink 的值，flex-basis 取 0%，如下是等同的：
```
.item {flex: 2 3;}
.item {
    flex-grow: 2;
    flex-shrink: 3;
    flex-basis: 0%;
}
```


当 flex 取值为一个非负数字和一个长度或百分比，则分别视为 flex-grow 和 flex-basis 的值，flex-shrink 取 1，如下是等同的：
```
.item {flex: 2333 3222px;}
.item {
    flex-grow: 2333;
    flex-shrink: 1;
    flex-basis: 3222px;
}
```


### align-items 和align-content

[demo](http://www.cnblogs.com/mysearchblog/p/5651671.html)

都是写在容器元素上的属性
**align-items**
align-items属性适用于所有的flex容器，它是用来设置每个flex元素在侧轴上的默认对齐方式

**align-content**
align-content属性只适用于**多行**的flex容器，并且当侧轴上有多余空间使flex容器内的flex线对齐,感觉这样翻译了之后还是略微有些抽象，不过有一个重点就是多行， 


[1]: http://img.caibaojian.com/uploads/2014/05/flexbox.png
[2]: http://img.caibaojian.com/uploads/2014/05/justify-contetnt.png
[3]: http://img.caibaojian.com/uploads/2014/05/align-items.png