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



# 选择器

```html
element > element(子选择期) 	div>p 	选择父级是 <div> 元素的 <p> 元素
element + element(相邻兄弟选择器) 	div+p 	选择紧接着<div>元素之后的<p>元素
element ~ element(通用兄弟选择器) 	div+p 	选择<div>元素之后的所有<p>元素
```

>这三个老是忘记 Orz 做个笔记 记录一下。。。

## 交集选择器

[ https://www.jianshu.com/p/cae9663a7c97 ]( https://www.jianshu.com/p/cae9663a7c97 )

```html
p.text{
  background:red;
}

<body>
   <p class="text">类名为text的p标签</p>    
   <div class="text">类名为text的div标签</div>
</body>
```

p.text  意思是选择p标签的类名为text的元素

注意点:     

*  选择器之间没有任何的连接符号      
*   选择器可以是标签名称,也可以是id、class名称   

## 属性选择器

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Attribute_selectors)

CSS **属性选择器**通过已经存在的属性名或属性值匹配元素。

```css
/* 存在title属性的<a> 元素 */
a[title] {
  color: purple;
}

/* 存在href属性并且属性值匹配"https://example.org"的<a> 元素 */
a[href="https://example.org"] {
  color: green;
}

/* 存在href属性并且属性值包含"example"的<a> 元素 */
a[href*="example"] {
  font-size: 2em;
}

/* 存在href属性并且属性值结尾是".org"的<a> 元素 */
a[href$=".org"] {
  font-style: italic;
}

/* 存在class属性并且属性值包含以空格分隔的"logo"的<a>元素 */
a[class~="logo"] {
  padding: 2px;
}
```

## 逻辑选择器

is、where、not、has

`:is()` 的优先级是由它的选择器列表中优先级最高的选择器决定的

从语法上，`:is` 和 `:where` 是一模一样的。它们的核心区别点在于 **优先级**。

`:where()` 的优先级**总是为 0** ，但是 `:is()` 的优先级是由它的选择器列表中优先级最高的选择器决定的。

## 为何CSS不支持父选择器

这个问题的答案和“为何CSS相邻兄弟选择器只支持后面的元素，而不支持前面的兄弟元素？”是一样的。

浏览器解析HTML文档，是从前往后，由外及里的。所以，我们时常会看到页面先出现头部然后主体内容再出现的加载情况。

但是，如果CSS支持了父选择器，那就必须要页面所有子元素加载完毕才能渲染HTML文档，因为所谓“父选择器”，就是后代元素影响祖先元素，如果后代元素还没加载处理，如何影响祖先元素的样式？于是，网页渲染呈现速度就会大大减慢，浏览器会出现长时间的白板。加载多少HTML就可以渲染多少HTML，在网速不是很快的时候，就显得尤为的必要。比方说你现在看的这篇文章，只要文章内容加载出来就可以了，就算后面的广告脚本阻塞了后续HTML文档的加载，我们也是可以阅读和体验。但是，如果支持父选择器，则整个文档不能有阻塞，页面的可访问性则要大大降低。



# 伪类和伪元素

伪类是用于选择文档中某些特定状态的元素

伪元素则用于选择并样式化文档中某些特定部分的特定区域。



## 伪类

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes)

伪类就是一种虚构的状态或者说是一个具有特殊属性的元素可以使用CSS进行样式修饰。常见的几种伪类是

:only-child ,:not,  :link , :visited , :hover , :active , **:first-child 以及 :nth-child**

### hover

```
#a:hover > #b{....}    
#a:hover ~ div{....} // 鼠标停留在a元素的时候让所有同层级元素有某某样式
// 防止选择器层级替换了下面的样式
#a:hover + #c{....} // 鼠标停留在a元素的时候让同层级中的c元素有某某样式
#a:hover + #c > #b{....} //  鼠标停留在a元素的时候让同层级中的c元素下的b元素有某某样式
```

### scope

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/:scope](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:scope)

**`:scope`** 属于 [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) [伪类](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Pseudo-classes)，它表示作为选择器要匹配的参考点的元素。

**当需要获取已检索到的的直接后代元素时，`:scope` 伪类很有用。**

例子：

```html
  <div id="context">
    <div id="element-1">
      <div id="element-1.1"></div>
      <div id="element-1.2"></div>
    </div>
    <div id="element-2">
      <div id="element-2.1"></div>
    </div>
  </div>
```

```js
  let context = document.getElementById('context')
  let selected = context.querySelectorAll(':scope > div'); // context下的直接div
  console.log(selected) // element-1 element-2
```

### nth-of-type 和 nth-child

 div:nth-of-type(n) 

 代表他是第n个类型为div的子元素，不管在他之前有多少个同级元素，只要不是div类型就都不算数，就是说这个div元素不一定在父元素中是第n个子元素，但他一定是第n个div元素 

 div:nth-child(n) 

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-child](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:nth-child)

就代表他是父元素的div元素，并且！并且他是爸爸元素的第n个孩子元素，他之前的所有同级元素都算数 

如果第n个子元素不是div元素，那对不起，爸爸要的不是你，爸爸宁可没有儿子也不会乱认儿子 

**总结 ：nth- child的要求比较高，可以多用nth-of-type**

举个例子

```html
<div>
  <p>第一个p元素</p>
  <span>第一个span元素</span>
  <p>第二个p元素</p>
  <span>第二个span元素</span>
</div>
```

```css
div p:nth-child(1) {
   background: red; // 命中 ”第一个p元素“
}

div p:nth-of-type(2) {
   background: brown; // 命中 ”第二个p元素“
}
```

补充：

nth-of-type(2n)   =>  设置偶数元素样式

nth-of-type(2n+1)  => 设置奇数元素样式

### first-child 不生效

[https://www.jianshu.com/p/4837fb9a6f6e](https://www.jianshu.com/p/4837fb9a6f6e)

我们经常有需求需要查找第一个子元素，但是使用了:first-child后发现没有效果，其实是我们理解错了:first-child的用法

```scss
E::first-child { sRules }
```

解读：匹配**同类型**中的第一个同级兄弟元素E。

这里有个问题，当父元素的第一个子元素类型不是E类型时，会失效，如：

```scss
p:first-child{color:#f00;}

<div>
    <h2>我是一个标题</h2>
    <p>我是一个p</p>
</div>
```

你会发现，没有选中p元素，这是因为div的第一个子元素不是p类型的，而是h2，因此这种情况下我们应该使用

```scss
E:first-of-type { sRules }
```

解读：匹配**同类型中的第一个同级兄弟元素E**。

> 这里的同类型是指元素类型p,div,span等等与类名无关，不是同一类名就可以触发，必须是同类型

同理 :last-child 也是一样的

### ios中触发伪类active

 :active伪类常用于设定点击状态下或其他被激活状态下一个链接的样式。最常用于锚点`<a href="#">`这种情况，一般主流浏览器下也支持其他元素 

 **值得注意的是：**伪类是一种比较方便的实现方式，但在ios中，需要在相关的元素或者`body`上绑定`touchstart`事件才能使元素的`:active`生效。 

```js
// 在main.js

// iOS 必须绑定 touch 事件才能触发 :active 伪类的 CSS 效果
document.body.addEventListener('touchstart', function noop() { })
```



### where

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/:where](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:where)

https://caniuse.com/?search=where

[anti design 样式兼容](https://ant-design.antgroup.com/docs/react/compatible-style-cn)

**where 在某些android的微信浏览器会有兼容性问题。**





### 在Sass中

```scss
@mixin links ($link, $visited, $hover, $active) {
    & {
        color: $link;
        &:visited {
            color: $visited;
        }
        &:hover {
            color: $hover;
        }
        &:active, &:focus {
            color: $active;
        }
    }
}
```

用法:

```scss
a {
    @include links(orange, blue, yellow, teal);
}
```


## 伪元素

它们更像是虚拟的元素可以和HTML元素一样对待。区别在于它们并不存在于文档树或者DOM之中。这意味着我们并没有真正的指定伪元素，但是可以使用CSS进行创建。

常见的几种伪元素是: :after , :before 以及 :first-letter


### 伪元素使用单冒号还是双冒号

在大多数情况下，两者均可。

CSS3中引入双冒号(::)是为了在伪类中，如:hover,:active,区分伪元素，如::before，::after。除了IE8及其以下版本不支持外，所有的浏览器均支持伪元素中双冒号的使用。

一些伪元素，如::backdrop只接受双冒号的使用。

>无需向后兼用处理的话，伪类用:,伪元素用::



### CSS 生成内容

[https://www.zhangxinxu.com/wordpress/2010/04/css-content%E5%86%85%E5%AE%B9%E7%94%9F%E6%88%90%E6%8A%80%E6%9C%AF%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8/](https://www.zhangxinxu.com/wordpress/2010/04/css-content%E5%86%85%E5%AE%B9%E7%94%9F%E6%88%90%E6%8A%80%E6%9C%AF%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8/)


通过CSS生成内容是通过content性结合:before或者:after伪元素实现的。

这里的“内容”可能是纯文本或者是通过操纵CSS来显示图形形状或者装饰元素的一个容器。


**注意： :before或者:after伪元素一定要记得加上content属性**
**注意： :before或者:after伪元素一定要记得加上content属性**
**注意： :before或者:after伪元素一定要记得加上content属性**

由于以下几个原因，生成内容不适用于重要副本或者文本:

* 对于屏幕阅读器是不可读的。
* 不可以进行选择。
* 如果生成的内容使用过多的内容进行修饰，屏幕阅读器会将其放大化，从而导致较为糟糕的用户体验。


使用CSS生成内容用于修饰或者一些非重要的文本信息，需要处理好与屏幕阅读器的关系，这样子才可以确保使用辅助技术的人可以拥有最佳的使用效果。当你使用CSS生成内容时，要时刻考虑"渐进增强"地设计理念。




### 伪元素与z-index 的那点事

[https://fatesinger.com/100258](https://fatesinger.com/100258)



```css
.box{
    width:100px;
    height:100px;
    position:relative;
    z-index:2;
    box-shadow:0 3px 5px #eee;
    background-color:#fff;
}

.box:before{
    content:"";
    position:absolute;
    top:5px;
    left:5px;
    right:5px;
    bottom:-5px;
    box-shadow:0 3px 5px #eee;
    background-color:#fff;
    // 关键代码
    z-index:1;
     // 关键代码
}
```


:before跑到box上面来了


解决方案


```css
.box{
    width:100px;
    height:100px;
    position:relative;
    top:0;
    box-shadow:0 3px 5px #eee;
    background-color:#fff;
    transiton:.5s;
}

.box:before{
    content:"";
    position:absolute;
    top:5px;
    left:5px;
    right:5px;
    bottom:-5px;
    box-shadow:0 3px 5px #eee;
    background-color:#fff;
     // 关键代码
    z-index:-1;
     // 关键代码
}

.box:hover{
    top:-5px;
}
```



**父元素不设置z-index值，伪元素设置负z-index 值，父元素位移效果不使用transform，简单说下原因。**


* 同一个层叠上下文里面, 层叠顺序从后向前依次是: 背景和边框、负z-index、块级盒、浮动盒、行内盒、z-index:0、正z-index.


即便是 position 不为 static 的元素, 如果没有指定一个非 auto 值的 z-index, 该元素就不会建立一个层叠上下文。




通俗的来讲，也就是如果box创建了层叠上下文，before伪元素一定会在box上面，因为**层叠上下文内部嵌套的子元素均受父元素影响。**


----------------



**如果想实现box层叠效果，需要元素和对应的伪元素处在相同层级级别，所以不能让元素创建层叠上下文。**



-------------------

以下情况会创建层叠上下文


**元素的transform值不是none。**




### 获取伪元素的属性值

获取伪元素的属性值可以使用 window.getComputedStyle() 方法，获取伪元素的CSS样式声明对象。然后利用getPropertyValue方法或直接使用键值访问都可以获取对应的属性值。

语法：window.getComputedStyle(element[, pseudoElement])


参数如下：

element（Object）：伪元素的所在的DOM元素；

pseudoElement（String）：伪元素类型。可选值有：”:after”、”:before”、”:first-line”、”:first-letter”、”:selection”、”:backdrop”；

举个栗子：

```css
// CSS代码
#myId:before {
content: "hello world!";
display: block;
width: 100px;
height: 100px;
background: red;
}
```

```html
// HTML代码
<div id="myId"></div>
```

```javascript
// JS代码
var myIdElement = document.getElementById("myId");
var beforeStyle = window.getComputedStyle(myIdElement, ":before");
console.log(beforeStyle); // [CSSStyleDeclaration Object]
console.log(beforeStyle.width); // 100px
console.log(beforeStyle.getPropertyValue("width")); // 100px
console.log(beforeStyle.content); // "hello world!"
```


>getPropertyValue()方法在IE9+和其他现代浏览器中都支持；在IE6~8中，可以使用getAttribute()方法来代替；

### 父元素的第一个子元素的margin-top越界

1. 为父元素加border-top: 1px;——有副作用
2. 为父元素指定padding-top: 1px;——有副作用
3. 为父元素指定overflow:hidden;——有副作用
4. **为父元素添加前置内容生成——推荐使用
   .parent:before {
   content: '  ';
   display: table;
   }**


### 子元素浮动后父元素高度变为0

1. 为父元素指定overflow:hidden;——有副作用
2. 为父元素指定高度：height: xxx;——有局限性
3. **为父元素添加后置内容生成——推荐使用
   .parent:after {
   content: '  ';
   display: table;
   clear: both;
   }**



### before/after图片不能设置宽高

[ https://segmentfault.com/q/1010000004569689 ]( https://segmentfault.com/q/1010000004569689 )

```css
    #center_box:before{
        content:url(http://localhost/quding/photos/u14.png);
        position: absolute;
        width:1000px;
        height:200px;
        z-index: 100;
        top: -110px;
    }
```

随我怎么调`width`,`height`都没变化.

加了`border`后包含图片的框出现了而且很大,但图片只在它的左上角,也就是说这里的`width`只改变了图像外面的div

:before /:after伪元素默认是一个行内元素，所以这个元素设置width/height是无效的
就像你对a元素设置width/height一样
设置position: absolute;后这个元素的display属性计算为block值；
但是设置的width/height针对的是:before/:after生成的匿名替换元素，而不是其中的content
所以图片的大小是没有效果的

解决：

你可以把图片设为背景图片，通过bakckground-size来设置大小

```css
#center_box:before{
    content:'';
    background-image:url(http://localhost/quding/photos/u14.png);
    background-size:1000px 200px;
    position: absolute;
    width:1000px;
    height:200px;
    z-index: 100;
    top: -110px;
}
```













# 浮动 

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



## 清除浮动 



### overflow:hidden

[为什么overflow:hidden能清除浮动(float)的影响](https://www.zhihu.com/question/30938856)

overflow 取值只要不是 visible ，就会开启这个超级属性（BFC），此超级属性反过来决定了 height: auto 是如何计算的

从 BFC 的机制设计来看，把 float 元素纳入尺寸计算（即所谓父块包裹子块）是合理的。



### clearfix

[网页链接](http://www.jianshu.com/p/9d6a6fc3e398)

通常我们在写html+css的时候，如果一个父级元素内部的子元素是浮动的（float），那么常会发生父元素不能被子元素正常撑开的情况

要解决此问题，我们可以给包裹层(包裹了float元素)添加一个类，叫做clearfix，下面是clearfix的实现形式（之一）：


**正是由于“块级元素”具有换行特性，因此理论上它都可以配合 clear 属性来清除浮动 带来的影响**


**最好的方式**

```css
.clearfix:after {
   content:""; 
   display: table;  // 也可以是 block，或者是 list-item 
   clear:both; 
}
```

上述代码通过伪类 :after 在container后添加内容（content），来实现清除浮动。


```css
.clearfix::before,
.clearfix::after {
    content: ".";
    display: block;
    height: 0;
    visibility: hidden;
}
.clearfix:after {clear: both;}
.clearfix {zoom: 1;}
```





# At 规则

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule)

**At 规则**是一个 [CSS 语句](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Syntax#css_语句)，用来指示 CSS 如何运行

## @media

[网页链接](http://www.cnblogs.com/zyl-Tara/p/5519144.html)

Media Queries能在不同的条件下使用不同的样式，使页面在不同在终端设备下达到不同的渲染效果

## @keyframes

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



## @supports

@supports是CSS3新引入的规则之一，主要用于检测当前浏览器是否支持某个CSS属性并加载具体样式.


```css
@supports (display: grid) {
    .container {
        color: red;
    }
}

```


当浏览器支持display:grid这个CSS属性时才应用其中的样式


**当然，这个支持性IE下全跪**


个人觉得这其中有个很矛盾的地方，就是这个属性基本只有在“高级”浏览器下才会是生效，但“高级”浏览器的支持范围又支持大部分的CSS属性，所以觉得把他当做一个“低级”浏览器检测器倒是个不错的选择


举个例子：


适配iphone刘海屏


```css
header {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 44px;

    /* Status bar height on iOS 10 */
    padding-top: 20px;   
}

@supports (constant(safe-area-inset-top)) {
    header {
        /* Status bar height on iOS 11+ */
        padding-top: constant(safe-area-inset-top);
    }
}
```





## @viewport

[https://udn.realityripple.com/docs/Web/CSS/@viewport](https://udn.realityripple.com/docs/Web/CSS/@viewport)



### viewport-fit

[https://udn.realityripple.com/docs/Web/CSS/@viewport/viewport-fit](https://udn.realityripple.com/docs/Web/CSS/@viewport/viewport-fit)

```html
// 效果同
<meta name="viewport"content="viewport-fit=cover"/>
```

- `auto`

  This value doesn’t affect the initial layout viewport, and the whole web page is viewable.

- `contain`

  The viewport is scaled to fit the largest rectangle inscribed within the display.  (视口被缩放以适应显示内的最大矩形。)

- `cover`

  The viewport is scaled to fill the device display. It is highly recommended to make use of the [safe area inset variables](https://udn.realityripple.com/docs/Web/CSS/env) to ensure that important content doesn't end up outside the display.  (视口被缩放以填充设备显示)  (需要自己手动处理 safe area)



# 滚动条

## 隐藏滚动条

在 PC 端，无论是什么浏览器，默认滚动条均来自`<html>`，而不是`<body>`标签。


所以，如果我们想要去除页面默认滚动条，只需要： 

```
html { overflow: hidden; }
```

>注：此方法在移动端基本上无效

在 PC 端， 窗体滚动高度可以使用 document.documentElement.scrollTop 获取，但是在移动端， 可能就要使用document.body.scrollTop获取。

----------

**滚动条会占用容器的可用宽度或高度**

IE7 及以上版本 IE、Chrome、Firefox 浏览器滚动栏所占据的宽度均是**17px**

## 自定义滚动条

支持-webkit-前缀的浏览器。例如，对于 Chrome 浏览器：

* 整体部分，::-webkit-scrollbar；
* 两端按钮，::-webkit-scrollbar-button； 
* 外层轨道，::-webkit-scrollbar-track  
* 内层轨道，::-webkit-scrollbar-track-piece；   
* 滚动滑块，::-webkit-scrollbar-thumb；
* 边角，::-webkit-scrollbar-corner

但是我们平时开发中只用下面 3 个属性： 


```css
    ::-webkit-scrollbar { /* 血槽宽度 */
      width: 8px;
      height: 8px;
    }

    ::-webkit-scrollbar-thumb { /* 拖动条 */
      background-color: rgba(0, 0, 0, .3);
      border-radius: 6px;
    }

    ::-webkit-scrollbar-track { /* 背景槽 */
      background-color: #ddd;
      border-radius: 6px;
    }
```



# position



## releative

1.  使用相对定位的盒子，会相对于它原本的位置，通过偏移指定的距离，到达新的位置
2.  使用相对定位的盒子仍在标准流中（会占据原来的位置），它对父亲和兄弟盒子都没有任何影响
3.  如果设定TRBL，并且父级没有设定position属性，仍旧以父级的左上角为原点进行定位(和absolute不同)

## absolute

若想把一个定位属性为absolute的元素定位于其父级元素内
必须满足两个条件：

* 设 定TRBL
* 父 级设定Position属性

1. **使用绝对定位的盒子以它的“最近”的一个“已经定位”的“祖先元素”为基准进行定位。**如果没有已经定位的祖先元素，那么会以浏览器窗口为基准进行定位

2. 绝对定位的框从标准流中脱离，这意味着他们对其后的兄弟盒子的定位没有影响，其他的盒子好像就好像这个盒子不存在一样

3. 所谓“已经定位”元素的含义是，position属性被设置。

如果设定TRBL，并且父级设定position属性(无论是absolute还是relative)，则以父级的左上角为原点进行定位，位置由TRBL决 定。**如果父级有Padding属性，那么就以内容区域的左上角为原点，进行定位。（也就是说父级Padding会影响子元素定位）**

如果设定TRBL，并且父级没有设定position属性，那么当前的absolute则以浏览器左上角为原始点进行定位，位置将由TRBL决定。
<strong>父级的padding对其根本没有影响。</strong>

**absolute和float的相似处：包裹性 和 高度欺骗**

[网页链接](http://www.jianshu.com/p/a3da5e27d22b)

### 和z-index的关系

* 让absolute元素覆盖正常文档流内元素（不用设z-index，自然覆盖）
* 让后一个absolute元素覆盖前一个absolute元素（不用设z-index，只要在HTML端正确设置元素顺序即可）


那什么时候需要设置z-index呢？当absolute元素覆盖另一个absolute元素，且HTML端不方便调整DOM的先后顺序时，需要设置z-index: 1。非常少见的情况下多个absolute交错覆盖，或者需要显示最高层次的模态对话框时，可以设置z-index > 1。





### 父元素padding子元素absolute

[https://segmentfault.com/q/1010000042686894](https://segmentfault.com/q/1010000042686894)

**子元素的position: absolute定位默认会从父元素的padding值开始计算定位**

```html
  <div class="parent" >
    parent
    <div class="children" >children</div>
  </div>
```

```scss
    .parent{
      display: inline-block;
      position: relative;
      padding-top:100px;
      width: 500px;
      height: 500px;
      background: red
    }
    
    .children{
      position: absolute;
      top:0;  /* 要让父元素的padding-top不起作用，得设置top:0 */
      width:100px;
      height: 100px;
      background: rebeccapurple;   
    }
```















## fixed和absolute的区别

fixed:固定定位
absolute:绝对定位

区别很简单：

1. 没有滚动条的情况下没有差异
2. 在有滚动条的情况下，fixed定位不会随滚动条移动而移动，而absolute则会随滚动条移动


可以这么理解，fixed：固定在当前window不动， absolute：会随参照对象元素的高度和宽度变化而变化

>一般fixed用在遮盖层和固定在页面某个位置，如固定在顶端的菜单栏，又如弹出提示框居中显示



## sticky

[ https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/ ]( https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/ )

[ https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/ ]( https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/ )

 **sticky元素效果完全受制于父级元素们** 

这和`position:fixed`定位有着根本性的不同，fixed元素直抵页面根元素，其他父元素对其left/top定位无法限制。

 sticky元素以下一些特性表现： 

* 父级元素不能有任何`overflow:visible`以外的overflow设置，否则没有粘滞效果。因为改变了滚动容器（即使没有出现滚动条）。因此，如果你的`position:sticky`无效，看看是不是某一个祖先元素设置了`overflow:hidden`，移除之即可。
* 父级元素设置和粘性定位元素等高的固定的`height`高度值，或者高度计算值和粘性定位元素高度一样，也没有粘滞效果。 
* 同一个父容器中的sticky元素，如果定位值相等，则会重叠；如果属于不同父元素，且这些父元素正好紧密相连，则会鸠占鹊巢，挤开原来的元素，形成依次占位的效果 
* sticky定位，不仅可以设置`top`，基于滚动容器上边缘定位；还可以设置`bottom`，也就是相对底部粘滞。如果是水平滚动，也可以设置`left`和`right`值。 
* 定位用的`bottom`，效果和`top`正好是对立的。设置`top`粘滞的元素随着往下滚动，是先滚动后固定；而设置`bottom`粘滞的元素则是先固定，后滚动；







# box



## box-sizing

[网页链接](http://www.w3chtml.com/css3/properties/user-interface/box-sizing.html)

box-sizing：content-box | border-box
默认值：content-box
适用于：所有接受width和height的元素
继承性：无

### content-box

**padding和border不被包含在定义的width和height之内。**对象的实际宽度等于设置的width值和border、padding之和，即 ( Element width =  设置的width + border + padding )

此属性表现为标准模式下的盒模型。

### border-box

**padding和border被包含在定义的width和height之内。**对象的实际宽度就等于设置的width值，即使定义有border和padding也不会改变对象的实际宽度，即 ( Element width = 设置的width )

此属性表现为怪异模式下的盒模型。



## box-shadow

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-shadow](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-shadow)

[https://www.w3cplus.com/css3/css3-box-shadows-unnoticed-spread](https://www.w3cplus.com/css3/css3-box-shadows-unnoticed-spread)

 box-shadow不仅仅只有
【投景方式：外阴影或内阴影】
【X轴偏移量：x-offset】
【Y轴偏移量：y-offset】
【阴影模糊半径：blur-radius】和【阴影颜色：color】

它还有一个常被我们遗忘的另外一个值——【阴影扩展半径：spread-radius】。我们可以通过【spread-radius扩展半径】来控制阴影的扩展方向：扩展半径有两个值，如果取值为正值，则个阴影会向外扩展，如果你给其取负值，整个阴影向内缩小。如此可以通个这个值配合阴影的模糊半径来制作单边的不同阴影效果，而且不会影响其他边的效果。或者换句简单的点，可以使用他制作单边投影效果。”


[更多效果](https://conceptboard.github.io/box-shadow-spread-examples/)

[Box-shadow生成器](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Background_and_Borders/Box-shadow_generator)



## line boxes

[CSS float浮动的深入研究、详解及拓展](https://www.zhangxinxu.com/wordpress/2010/01/css-float%e6%b5%ae%e5%8a%a8%e7%9a%84%e6%b7%b1%e5%85%a5%e7%a0%94%e7%a9%b6%e3%80%81%e8%af%a6%e8%a7%a3%e5%8f%8a%e6%8b%93%e5%b1%95%e4%b8%80/)

**一个inline-block元素，如果里面没有inline内联元素，或者overflow不是visible，则该元素的基线就是其margin底边缘，否则，其基线就是元素里面最后一行内联元素的基线。**







# 权重

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



明白了CSS选择符的权重后，我们如何依照选择符的权重定义合适的选择符？

定义选择符的原则是：尽量使选择付的权重低，目的是保证样式在应用于多个元素时容易被覆盖，这可提高样式代码的重用性和可维护性。

具体的原则如下：

1. CSS样式中尽量不要使用ID选择器
   ID选择器有很高的权重，如果要覆盖使用了ID选择器的样式，就必须在原先使用ID选择器的基础上添加新的选择符（类选择器或者标签类型选择器或者额使用个!important,但这样做的结果是无法重用的样式代码会越来越多）。
2. 减少子选择器的层级
   减少选择器的层级的过程也是降低选择符整体权重的过程。
3. 使用组合的CSS类选择器
   使用CSS选择器组合的方式，开发者可以不用考虑CSS样式覆盖的问题，避开了计算选择符权重的过程，同时也提高了代码的重用性。

## !important修改权重

[网页链接](https://www.cnblogs.com/tianma3798/p/6202008.html)


!important为开发者提供了一个增加样式权重的方法。应当注意的是!important是对整条样式的声明，包括这个样式的属性和属性值。

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



# flex布局

弹性布局（flexible box）模块旨在提供一个更加有效的方式来布置，对齐和分布在容器之间的各项内容，即使它们的大小是未知或者动态变化的。

弹性布局的主要思想是让容器有能力来改变项目的宽度和高度，以填满可用空间（主要是为了容纳所有类型的显示设备和屏幕尺寸）的能力。

## align-items 和align-content

[demo](http://www.cnblogs.com/mysearchblog/p/5651671.html)

都是写在容器元素上的属性 **align-items** align-items属性适用于所有的flex容器，它是用来设置每个flex元素在侧轴上的默认对齐方式

**align-content** align-content属性只适用于**多行**的flex容器，并且当侧轴上有多余空间使flex容器内的flex线对齐,感觉这样翻译了之后还是略微有些抽象，不过有一个重点就是多行，



# grid 布局

>注意：在 网格容器(Grid Container) 上使用column，float，clear， vertical-align 不会产生任何效果。

[最强大的 CSS 布局 —— Grid 布局](https://juejin.cn/post/6854573220306255880)

[5分钟学会Grid布局](http://www.css88.com/archives/8506)

[Grid布局完全指南](http://www.css88.com/archives/8510)

CSS Grid 布局由两个核心组成部分是 wrapper（父元素）和 items（子元素）。 wrapper 是实际的 grid(网格)，items 是 grid(网格) 内的内容。

## Columns(列) 和 rows(行)

为了使其成为二维的网格容器，我们需要定义列和行将使用grid-template-row和grid-template-column属性。

举个例子：

```css
.wrapper {
    display: grid;
    grid-template-columns: 100px 100px 100px; //  3 列
    grid-template-rows: 50px 50px;  // 2 行
}
```

举个例子：

```css
  .wrapper {
    display: grid;
    grid-gap: 10px;
    // 5列  宽度 min:200px height:240px 
    grid-template-columns: repeat(5, minmax(200px, 240px));
    // 5行  高度 min:100px height:140px 
    grid-template-rows: repeat(5, minmax(100px, 140px));
  }
```

## 子元素

要定位和调整 items(子元素) 大小，我们将使用 grid-column 和 grid-row 属性来设置：

```
.item1 {
    grid-column-start: 1;
    grid-column-end: 4;
}
```

item1 占据从第一条网格线开始，到第四条网格线结束。



### table

合并border

```css
table {
  border-spacing: 0;
  border-collapse: collapse;
}

th {
  border: 1px solid rgb(142, 136, 136);
}

td {
  border: 1px solid rgb(142, 136, 136);
}
```





# background



## background-attachment

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-attachment](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-attachment)

决定背景图像的位置是在视口内固定，或者随着包含它的区块滚动。



## background-image

默认值padding盒子

background-image: 定位的起点默认是是padding的外边缘, 可以通过background-origin修改

```
background-origin: padding-box|border-box|content-box;
```

| 值          | 描述                           |
| :---------- | :----------------------------- |
| padding-box | 背景图像相对于内边距框来定位。 |
| border-box  | 背景图像相对于边框盒来定位。   |
| content-box | 背景图像相对于内容框来定位。   |



### 



# animation

[网页链接](http://www.w3school.com.cn/cssref/pr_animation.asp)

animation 属性是一个简写属性，用于设置六个动画属性：

* animation-name
* animation-duration
* animation-timing-function
* animation-delay
* animation-iteration-count
* animation-direction

>animation: doUpDown .5s ease-in-out infinite alternate both

## animation-direction 

animation-direction 属性定义是否应该轮流反向播放动画。

如果 animation-direction 值是 "alternate"，则动画会在奇数次数（1、3、5 等等）正常播放，而在偶数次数（2、4、6 等等）向后播放。

animation-direction: normal | alternate;

* normal 	默认值。动画应该正常播放。 	测试
* alternate 	动画应该轮流反向播放。

## animation-fill-mode

animation-fill-mode : none | forwards | backwards | both;

* none 	不改变默认行为。
* forwards 	当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）。
* backwards 	在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）。
* both 	向前和向后填充模式都被应用。

动画按执行时间来划分，它分为三个过程，或者说一次动画过程可以将元素划分为三个状态：**动画等待、动画进行和动画结束**。默认情况之下，只有在动画进行状态，才会应用@keyframes所声明的动画；而在动画等待和动画结束状态，对元素样式并不会产生任何的影响。

## animation-play-state 

暂停动画：

```
div
{
animation-play-state:paused;
-webkit-animation-play-state:paused; /* Safari 和 Chrome */
}
```

值:

* running
  当前动画正在运行。
* paused
  当前动画以被停止。 

## animation-timing-function

规定动画的速度曲线。
速度曲线定义动画从一套 CSS 样式变为另一套所用的时间。




| linear                | 动画从头到尾的速度是相同的。                                 |
| --------------------- | ------------------------------------------------------------ |
| ease                  | 默认。动画以低速开始，然后加快，在结束前变慢。               |
| ease-in               | 动画以低速开始。                                             |
| ease-out              | 动画以低速结束。                                             |
| ease-in-out           | 动画以低速开始和结束。                                       |
| cubic-bezier(n,n,n,n) | 在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值。 |


**但是漏掉一个很重要的 steps**

steps 函数指定了一个**阶跃函数**

第一个参数指定了时间函数中的间隔数量（必须是正整数）

第二个参数可选，接受 start 和 end 两个值，指定在每个间隔的起点或是终点发生阶跃变化，默认为 end。

step-start等同于steps(1,start)，动画分成1步，动画执行时为开始左侧端点的部分为开始；

step-end等同于steps(1,end)：动画分成一步，动画执行时以结尾端点为开始，默认值为end。

### steps第一个参数的错误的理解

steps(5，start)

steps() 第一个参数 number 为指定的间隔数，即把动画分为 n 步阶段性展示，估计大多数人理解就是keyframes写的变化次数

例如:

```
@-webkit-keyframes circle {
        0% {}
        25%{}
        50%{}
        75%{}
        100%{}
 }
```

之前也一直认为steps(5，start）中的5 就是指的keyframes中的0% 25% 50% 75% 100% 分成5个间隔等分

为什么会出现这种理解错误，我们看一个例子

keyframes的关键帧是只有2个规则的时候，假如我们有一张400px长度的雪碧图

```
@-webkit-keyframes circle {
        0% {background-position-x: 0;}
        100%{background-position-x: -400px;}
 }
```

此刻设置steps(5，start）那么会发现5张图会出现帧动画的效果，因为steps中的5把 0% – 100%的规则，内部分成5个等分

实际内部会执行这样一个关键帧效果

```
@-webkit-keyframes circle {
        0% {background-position-x: 0;}
        25% {background-position-x: -100px;}
        50% {background-position-x:-200px;}
        75%{background-position-x: -300px;}
        100%{background-position-x: -400px;}
 }
```


将这个规则稍微修改下，加入一个50%的状态

```
@-webkit-keyframes circle {
        0% {background-position-x: 0;}
        50% {background-position-x: -200px;}
        100%{background-position-x: -400px;}
 }
```

那么同样用steps(5，start）效果就会乱套

此刻你会很迷惑，所以关键要理解第一个参数的针对点，首先引入一个核心点：


**timing-function 作用于每两个关键帧之间，而不是整个动画**

那么第一个参数很好理解了，steps的设置都是针对两个关键帧之间的，而非是整个keyframes，所以第一个参数对 - 次数对应了每次steps的变化 

换句话说也是 0-25 之间变化5次，  25-50之间 变化5次 ，50-75 之间变化5次，以此类推 


 第二个参数可选，接受 start 和 end 两个值，指定在每个间隔的起点或是终点发生阶跃变化，默认为 end

通过案例看下 step-start，step-end 的区别

```
@-webkit-keyframes circle {
        0% {background: red}
        50%{background: yellow}
        100% {background: blue}
    }
```

step-start ： 黄色与蓝色相互切换

step-end  ： 红色与黄色相互切换


2个参数都会选择性的跳过前后部分，start跳过0%，end跳过100% 
step-start在变化过程中，都是以下一帧的显示效果来填充间隔动画，所以0% 到 50%  直接就显示了黄色yellow

step-end与上面相反，都是以上一帧的显示效果来填充间隔动画，所以0% 到 50% 直接就显示了红色red  

总结：
steps函数，它可以传入两个参数，第一个是一个大于0的整数，他是将间隔动画等分成指定数目的小间隔动画，然后根据第二个参数来决定显示效果。

第二个参数设置后其实和step-start，step-end同义，在分成的小间隔动画中判断显示效果。可以看出：steps(1, start) 等于step-start，steps(1,end)等于step-end

最核心的一点就是：timing-function **作用于每两个关键帧之间，而不是整个动画** 

 

## animation-delay负值

animation-delay:-2s

**允许负值，-2s 使动画马上开始，但跳过 2 秒进入动画。**

[https://www.w3school.com.cn/cssref/pr_animation-delay.asp](https://www.w3school.com.cn/cssref/pr_animation-delay.asp)

**被设置了 animation-dealy 为负值的动画会立刻执行，开始的位置是其动画阶段中的一个阶段** 

[https://juejin.im/post/5d4b8707f265da03a65302bd?utm_source=gold_browser_extension](https://juejin.im/post/5d4b8707f265da03a65302bd?utm_source=gold_browser_extension) 



## 例子

```
.music-note#one {
    margin-left: -250px;
    top: 50%;
    animation: note-anim 2s 3.5s infinite ease;
    animation-fill-mode: forwards;
    -webkit-animation: note-anim 2s 3.5s infinite ease;
    -webkit-animation-fill-mode: forwards;
}

@keyframes note-anim {
    0% {
        opacity: 0;
        -webkit-transform: translate(0px, 50px);
        transform: translate(0px, 50px);
    }
    30% {
        -webkit-transform: rotate(12deg) translate(-30px, 0px);
        transform: rotate(12deg) translate(-30px, 0px);
    }
    45% {
        opacity: 1;
    }
    60% {
        -webkit-transform: rotate(-12deg) translate(30px, -100px);
        transform: rotate(-12deg) translate(30px, -100px);
    }
    100% {
        opacity: 0;
        -webkit-transform: rotate(0deg) translate(0px, -200px);
        transform: rotate(0deg) translate(0px, -200px);
    }
}
```

>这里的3.5s是指animation-delay，规定在第一次动画开始之前的延迟。如果有设置多次播放，对后面播放的动画无延迟效果



著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。

原文: https://www.w3cplus.com/css3/understanding-css-animation-fill-mode-property.html © w3cplus.com





# transform



## transform-origin

[网页链接](http://blog.csdn.net/xu_ya_fei/article/details/51711968)

transform-origin 属性允许您改变被转换元素的位置。
默认值： 50% 50% 0
2D 转换元素能够改变元素 x 和 y 轴。3D 转换元素还能改变其 Z 轴。



## matrix

[理解CSS3 transform中的Matrix(矩阵)](https://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)



# filter

[网页链接](http://www.w3cplus.com/css3/ten-effects-with-css3-filter)

Filters主要是运用在图片上，以实现一些特效。**他会直接影响其后代元素，有点类似于opacity。**  兼容性比较好，不仅仅作用于当前元素，后代元素也会继承这个属性，作用于一个空背景元素没有效果

```css
 elm {
        filter: none | <filter-function > [ <filter-function> ]* 
      }      
```

其默认值是none，他不具备继承性，其中filter-function一个具有以下值可选：

* grayscale灰度
* sepia褐色（求专业指点翻译）
* saturate饱和度
* hue-rotate色相旋转
* invert反色
* opacity透明度
* brightness亮度
* contrast对比度
* blur模糊
* drop-shadow阴影



![](http://www.w3cplus.com/sites/default/files/filter-demo.png )

## backdrop-filter

是在Filter Level2提出来的。其取值和filter
Level1中filter属性的属性值一样，包括：

* `<url>`
* blur()
* brightness()
* contrast()
* drop-shadow()
* grayscale()
* hue-rotate()
* invert()
* opacity()
* saturate()
* sepia()

既然backdrop-filter整出的效果和filter没有差别，那还整个新属性出来，这不蛋疼？其实否也，你是否有发现过，最早在SVG上得到的filter效果，只能使用在SVG元素上；而filter使用在元素上，会直接影响其后代所有元素。那么问题来了，如果只需要对元素的背景做filter效果，怎么破。这个时候就突显了backdrop-filter的重要性。

[backdrop-filter](https://www.w3cplus.com/css3/advanced-css-filters.html)

例子:

```css
background: rgba(0,0,0,.2); 
backdrop-filter: blur(2px) hue-rotate(180deg);
```

使用backdrop-filter可以实现我们一直无法实现的高斯模糊效果。

```css
.header {
    background-color: rgba(255,255,255,.6);
    backdrop-filter: blur(5px)
}
```

backdrop-filter除了可以实现类似iOS系统上的高斯模糊效果之外，还可以实现一些其它效果，比如说提高图像上的文本的可读性效果，而且还可以结合多个backdrop-filter属性值，可以实现类似于CSS混合模式的图片合层效果



注意事项:

* **运用backdrop-filter元素的背景应该使用半透明，不然永远看不到效果  (重要)**
* 当backdrop-filter属性和裁剪属性(如border-radius、mask、clip-path等)结全在一起使用时，会有Bug产生
* backdrop-filter可以创建一个堆栈文本(Stacking Context)，类似于opacity属性一样
* 可以配合动画属性animation一起使用
* 到目前为止，仅有Safari浏览器支持，而且还需要添加前缀：-webkit-backdrop-filter,如果你使用autoprefixer这样的插件，无需考虑前缀相关事项

## filter()

但很多时候，只是希望元素的背景做效果调整，又不希望他会影响其他元素。而且又没有backdrop-filter属性的情形之下，filter()就显得格外的重要。

filter()并不等于以前介绍过的filter属性。简单的理解，一个是函数，一个是属性。这里是filter()函数。


filter(`<url>`, `<filter-function-list>`)

其中<url>是指一个图像，`<filter-function-list>`是一个过滤器。这两者结合在一起将会返回一个处理过的新图像。如：

```
.element {
    background: filter(url(path/to/img.jpg), blur(5px));
}
```

因此，你可以给图片使用过滤效果，然后填充到元素中，比如background-filter、background-opacity、background-blur

注意事项

* 规范中定义的过滤函数
* 和background-size属性一起使用会有Bug
* 支持动画
* 需要添加前缀：-webkit-filter()



# Dark Mode

[Using Tailwind CSS to style your site in dark mode.](https://v2.tailwindcss.com/docs/dark-mode)

有两种实现方式：

* class （可以手动切换）
* media （根据系统判断）@media (prefers-color-scheme)

[@media (prefers-color-scheme)](https://codepen.io/jonneal/pen/vzPwWo/)



# css in js

[https://dev.to/srmagura/why-were-breaking-up-wiht-css-in-js-4g9b](https://dev.to/srmagura/why-were-breaking-up-wiht-css-in-js-4g9b)

Good:

1. Styles are locally-scoped. 样式是局部范围的。
2. Styles are colocated with the components they apply to. 样式与其应用的组件位于同一位置。
3. You can use JavaScript variables in styles.您可以在样式中使用 JavaScript 变量。

Ugly:

* **Frequently inserting CSS rules forces the browser to do a lot of extra work** **频繁插入CSS规则迫使浏览器做很多额外的工作。**

* **With CSS-in-JS, there's a lot more that can go wrong, especially when using SSR and/or component libraries****使用 CSS-in-JS，还有很多可能出错的地方，尤其是在使用 SSR 和/或组件库时。



## css modules



### [define a global class](https://stackoverflow.com/questions/34279517/using-css-modules-how-do-i-define-a-global-class)

[Using css modules how do I define a global class](https://stackoverflow.com/questions/34279517/using-css-modules-how-do-i-define-a-global-class)

```css
:global(.tweet) {
    text-align: left;
}
:global(.user) {
    text-align: left;
}

.test:global(.StaticClass) {
    background-color: orange;
}
```





# 移动端border-1px



## 伪类实现

[网页链接](https://blog.csdn.net/qq_34543438/article/details/73839086)

即通过伪类+子绝父相 实现1px的下边框

**stylus写法**
在 stylus文件夹中创建mixin.styl文件

```stylus
border-1px($color)
  position: relative
  &:after
    display: block
    position: absolute
    left: 0
    bottom: 0
    width: 100%
    border-top: 1px solid $color
    content: ' '
```

在 stylus文件夹中创建base.styl文件，内容如下：（根据设备的dpr确定y轴的缩放比例）

```stylus
@media (-webkit-min-device-pixel-ratio: 1.5),(min-device-pixel-ratio: 1.5)  
   .border-1px  
       &::after  
          -webkit-transform: scaleY(0.7)  
          transform: scaleY(0.7)  
  
@media (-webkit-min-device-pixel-ratio: 2),(min-device-pixel-ratio: 2)  
   .border-1px  
       &::after  
          -webkit-transform: scaleY(0.5)  
          transform: scaleY(0.5)  
```

调用

```stylus
 .food
          position relative
          padding 12px 0
          box-sizing border-box
          border-1px(rgba(7, 17, 27, 0.1))
```

## border-img 实现

[https://www.w3cplus.com/css/fix-1px-for-retina.html](https://www.w3cplus.com/css/fix-1px-for-retina.html)

## postcss-write-svg 实现

使用border-image每次都要去调整图片，总是需要成本的。基于上述的原因，我们可以借助于PostCSS的插件postcss-write-svg来帮助我们。如果你的项目中已经有使用PostCSS，那么只需要在项目中安装这个插件

## 检测浏览器是否支持0.5px实现

 下面的代码存在于flexible.js中

```js
if (dpr >= 2) {
    var fakeBody = document.createElement('body')
    var testElement = document.createElement('div')
    testElement.style.border = '.5px solid transparent'
    fakeBody.appendChild(testElement)
    docEl.appendChild(fakeBody)
    if (testElement.offsetHeight === 1) {
      docEl.classList.add('hairlines')
    }
    docEl.removeChild(fakeBody)
}
```

上面的代码用于检测分辨率大于等于 2 的设备中的浏览器是否支持 0.5 px，如果支持则在 html 标签在添加 `hairlines` 类。其思路是通过设置一个 div 元素的 border 为 0.5px,然后获取这个 div 元素的高度，如果高度为 1 则表示当前浏览器支持 0.5px 写法。这段代码的是为了编写 1px 的兼容代码：

```css
.hairlines .bb{
  border-bottom:0.5px !important;
}
```





# text

## text-overflow

clip|ellipsis|string;

* clip	修剪文本。	
* ellipsis	显示省略符号来代表被修剪的文本。	
* string	使用给定的字符串来代表被修剪的文本。


text-overflow只是用来说明文字溢出时用什么方式显示，要实现溢出时产生省略号的效果，还须定义强制文本在一行内显示（white-space:nowrap）及溢出内容为隐藏（overflow:hidden）

## text-shadow

文本阴影

* X-Offset：表示阴影的水平偏移距离，其值为正值时阴影向右偏移，反之向左偏移；
* Y-Offset：是指阴影的垂直偏移距离，如果其值是正值时，阴影向下偏移，反之向上偏移；
* Blur：是指阴影的模糊程度，其值不能是负值，如果值越大，阴影越模糊，反之阴影越清晰，如果不需要阴影模糊可以将Blur值设置为0；
* Color：是指阴影的颜色，其可以使用rgba色。

例如：

text-shadow: 0 1px 1px #fff;



## 文字溢出显示省略号

[https://www.daqianduan.com/6179.html](https://www.daqianduan.com/6179.html)

### 单行文字

如果实现单行文本的溢出显示省略号同学们应该都知道用text-overflow:ellipsis属性来，当然还需要加宽度width属来兼容部分浏览。

```css
overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
```

### 多行文字

```css
display: -webkit-box;
-webkit-box-orient: vertical;
-webkit-line-clamp: 3;
overflow: hidden;
```

因使用了WebKit的CSS扩展属性，该方法适用于WebKit浏览器及移动端；

* -webkit-line-clamp用来限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他的WebKit属性。常见结合属性：
* display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
* -webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。



---



after伪类元素实现

```html
  <div class="wrapper">
    <div class="overflow">行溢出多行溢行溢出多行溢多行溢出多行溢出多行溢出多行溢出多行多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出多行溢出</div>
  </div>
```

```css
    /* 最多两行 */
    .overflow{
      position: relative;
      line-height: 20px;
      max-height: 40px;
      overflow: hidden;
    }
    .overflow::after{
      content:'...';
      position: absolute;
      bottom: 0;
      right: 0;
      /* 下面这两行可加可不加 */
      /* padding-left: 10px;
      background: linear-gradient(to right, transparent, red 55%); */
    }
```

* 将height设置为line-height的整数倍，防止超出的文字露出。
* 给p::after添加渐变背景可避免文字只显示一半。



# margin折叠

[网页链接](http://www.w3school.com.cn/css/css_margin_collapsing.asp)

[github代码](https://github.com/QinZhen001/animation-demo/blob/master/other/margin-top.html)

**外边距合并指的是，当两个垂直外边距相遇时，它们将形成一个外边距。
合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者**。

## 产生条件

* 这些margin都处于普通流中，并在同一个BFC中；
* 这些margin没有被非空内容、padding、border 或 clear 分隔开；
* 这些margin在垂直方向上是毗邻的，包括以下几种情况：
  * 一个box的top margin与第一个子box的top margin
  * 一个box的bottom margin与紧接着的下一个box的top margin
  * 一个box的top margin与其自身的bottom margin，但须满足没创建BFC、零min-height、零或者“auto”的height、没有普通流的子box

## 解决方案

* 给父元素添加padding-top值
* 给父元素添加border值
* 给父元素添加属性overflow:hidden;
* 给父元素或者子元素声明浮动float
* 使父元素或子元素声明为绝对定位：position:absolute;
* 给父元素添加属性 overflow:auto或positon:relative；







# other






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



### white-space

[网页链接](http://www.w3school.com.cn/cssref/pr_text_white-space.asp)

white-space 属性设置如何处理元素内的空白。
这个属性声明建立布局过程中如何处理元素中的空白符

* normal	默认。空白会被浏览器忽略。
* pre	空白会被浏览器保留。其行为方式类似 HTML 中的 `<pre>` 标签。
* nowrap	文本不会换行，文本会在在同一行上继续，直到遇到 `<br>` 标签为止。
* pre-wrap	保留空白符序列，但是正常地进行换行。
* pre-line	合并空白符序列，但是保留换行符。
* inherit	规定应该从父元素继承 white-space 属性的值。




### scoped属性

<strong>从当前STYLE元素所在的容器开始选择后代。</strong>

style标记上新出现的这个scoped属性可以让CSS样式只对局部元素生效，具体说，就是存放这段style样式的元素的子元素生效，下面来看看它的效果。


```
<style scoped>
    /* styles go here */
</style>
```


### font-size:0作用


[网页链接](http://www.cnblogs.com/guagnxu/p/6382163.html)

```
html：
<div class="box">
  <div>1</div>
  <div>2</div>
  <div>3</div>
</div>
```


```
css：
.box{
  width: 90px;
  height: 60px;
  border: 1px solid #ccc;
}
.box div{
  display: inline-block;
  box-sizing: border-box;
  font-size: 14px;
  width: 30px;
  border: 1px solid ;
}
```


理论上box下面的三个div都是30px，刚好在一行显示，但是实际效果是这样：

![enter description here][3]

这就是上文说到的原因，我们在box下添加font-size:0;再看看效果

![enter description here][4]

可以看到这才是我们想要的结果，因此在实际开发中，为了更好的还原设计稿，在父元素很有必要设置font-size:0，避免莫名其妙的间距。















### MutationObserver监听dom树

[ https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver ]( https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver )

```js
// Select the node that will be observed for mutations
var targetNode = document.getElementById('some-id');

// Options for the observer (which mutations to observe)
var config = { attributes: true, childList: true, subtree: true };

// Callback function to execute when mutations are observed
var callback = function(mutationsList) {
    for(var mutation of mutationsList) {
        if (mutation.type == 'childList') {
            console.log('A child node has been added or removed.');
        }
        else if (mutation.type == 'attributes') {
            console.log('The ' + mutation.attributeName + ' attribute was modified.');
        }
    }
};

// Create an observer instance linked to the callback function
var observer = new MutationObserver(callback);

// Start observing the target node for configured mutations
observer.observe(targetNode, config);

// Later, you can stop observing
observer.disconnect();
```







### 垂直居中线



![](https://s1.ax1x.com/2020/04/08/Gf1FGd.png)



```html
      <div class="dialog__header">
        <div class="header__divider--horizon"></div>
        <span class="tag">
          <span class="text">活动课</span>
        </span>
        <div class="header__divider--horizon"></div>
      </div>
```



```css
  .dialog__header {
    position: relative;
    margin: 64px auto 0 auto;
    width: 516px;
    height: 42px;
    line-height: 42px;
    // 垂直居中 关键是下面这一行 与text中的文字fontSize一致
    font-size: 28px;
    .header__divider--horizon {
      position: relative;
      display: inline-block;
      vertical-align: middle;
      background: rgba(216, 216, 216, 1);
      width: 200px;
      height: 1PX;
    }
    .tag {
      position: relative;
      margin: 0 8px;
      display: inline-block;
      width: 100px;
      height: 42px;
      line-height: 42px;
      border-radius: 8px;
      box-sizing: border-box;;
      .text {
        display: inline-block;
        width: 100%;
        height: 42px;
        text-align: center;
        font-size: 28px;
        line-height: 42px;
      }
    }
  }
```





### 原生变量 var

[https://www.zhangxinxu.com/wordpress/2016/11/css-css3-variables-var](https://www.zhangxinxu.com/wordpress/2016/11/css-css3-variables-var/)

CSS中原生的变量定义语法是：`--*`，变量使用语法是：`var(--*)`，其中`*`表示我们的变量名称。关于命名这个东西，各种语言都有些显示，例如CSS选择器不能是数字开头，JS中的变量是不能直接数值的，但是，在CSS变量中，这些限制通通没有，例如：

```css
:root {
  --1: #369;
}
body {
  background-color: var(--1);
}
```





### overscroll-behavior

CSS `overscroll-behavior`属性可以设置DOM元素滚动到边缘时候的行为。

#### 语法

```css
overscroll-behavior: [ contain | none | auto ]{1,2}
```

#### 参数

**auto**

默认值。就是我们默认看到的滚动行为表现，滚动到边缘后继续滚动外部的可滚动容器。

**contain**

默认的滚动溢出行为只会表现在当前元素的内部（例如“反弹”效果或刷新），不会对相邻的滚动区域进行滚动。例如创建了一个浮层，浮层滚动（带弹性效果），但是底层元素不会滚动。

**none**

相邻的滚动区域不会发生滚动，并且会阻止默认的滚动溢出行为。





#### 子元素滚动外面不滚动



![](https://image.zhangxinxu.com/image/blog/202001/overscroll-demo.gif)



 就一行简简单单普普通通的CSS代码——`overscroll-behavior:contain` 



```css
zxx-scroll {
    display: block;
    width: 280px; height: 200px;
    padding: .5em 1em;
    margin: 5em auto;
    border: solid deepskyblue;
    overflow: auto;
    overscroll-behavior: contain;
    -ms-scroll-chaining: contain;
}
```











### Object标签

`<object>`标签是一个HTML标签，用于在网页中显示音频，视频，图像，PDF和Flash等多媒体；它通常用于嵌入由浏览器插件处理的Flash页面元素，如Flash和Java项目。它还可以用于在HTML页面内显示另一个网页。

```html
  <object classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" id="elnFlvPlayer" width="100%" height="100%"
    codebase="http://fpdownload.macromedia.com/get/flashplayer/current/swflash.cab">
    <param name="wmode" value="transparent">
    <param name="FlashVars"
      value="videoPath=https://file5-sdgh.21tb.com/202006291656/d1c2cccd2f5abc5fa720a17c7c3a22f0/sf-server/file/getFile/bd00a5451dacaa76b3b28be8bc46b4d8-C_1523333333333/xiao100/210fe65098f5454da7a7e903a617e75f_0100/video/mp4/210fe65098f5454da7a7e903a617e75f.mp4&amp;isComplete=false&amp;watermarkUser=&amp;watermarkId=&amp;courseId=210fe65098f5454da7a7e903a617e75f&amp;scoId=test&amp;serviceUrl=/html/study/study.saveProgress.do&amp;viewMode=browsed&amp;locationTime=0&amp;rate=0">
    <param name="movie" value="/els/flash/NewCourseElnFlvPlayer.swf?v=0206">
    <param name="quality" value="high">
    <param name="bgcolor" value="#869ca7">
    <param name="allowFullScreen" value="true">
    <param name="allowScriptAccess" value="sameDomain"><embed swliveconnect="true" name="elnFlvPlayer"
      wmode="transparent" src="/els/flash/NewCourseElnFlvPlayer.swf?v=0206"
      flashvars="videoPath=https://file5-sdgh.21tb.com/202006291656/d1c2cccd2f5abc5fa720a17c7c3a22f0/sf-server/file/getFile/bd00a5451dacaa76b3b28be8bc46b4d8-C_1523333333333/xiao100/210fe65098f5454da7a7e903a617e75f_0100/video/mp4/210fe65098f5454da7a7e903a617e75f.mp4&amp;courseId=210fe65098f5454da7a7e903a617e75f&amp;scoId=test&amp;isComplete=false&amp;watermarkUser=&amp;watermarkId=&amp;serviceUrl=/html/study/study.saveProgress.do&amp;viewMode=browsed&amp;locationTime=0&amp;rate=0"
      quality="high" bgcolor="#869ca7" width="100%" height="100%" align="middle" play="true" loop="false"
      allowscriptaccess="sameDomain" allowfullscreen="true" type="application/x-shockwave-flash"
      pluginspage="http://www.adobe.com/go/getflashplayer"></object>
```





### display和visibility的区别

[网页链接](http://www.cnblogs.com/Chenshuai7/p/5188068.html)

* display 影响文档流，visibility 不影响；
* display 改变的是DOM的显示形式，即以何种方式来计算它的大小；visibility 只是控制可见性，DOM 的大小不会改变。



visibility属性

确定元素显示还是隐藏；
visibility="visible|hidden"，visible显示，hidden隐藏。

当visibility被设置为"hidden"的时候，元素虽然被隐藏了，但它仍然占据它原来所在的位置。



<strong>注意到，当元素被隐藏之后，就不能再接收到其它事件了</strong>

---



display属性

就有一点不同了。visibility属性是隐藏元素但保持元素的浮动位置，而display实际上是设置元素的浮动特征。

* block:
  当display被设置为block(块)时，容器中所有的元素将会被当作一个单独的块，就像`<DIV>`元素一样，它会在那个点被放入到页面中。(实际上你可以设置`<span>`的display:block，使其可以像`<DIV>`一样工作。

* inline:
  将display设置为inline，将使其行为和元素inline一样---即使它是普通的块元素如`<DIV>`，它也将会被组合成像`<span>`那样的输出流。

* none:
  最后是display被设置：none,这时元素实际上就从页面中被移走，它下面所在的元素就会被自动跟上填充。







### table-cell实现多行文字垂直居中

``` css
  <style>
    * {
      padding: 0;
      margin: 0;
    }

    .parent {
      display: table;
      width: 500px;
      height: 500px;
      background:red;
    }

    .son{
      display: table-cell; 
      /* 这里的height已经不起作用了 */
      height: 150px;
      background-color: yellow;
      vertical-align: middle;
    }
  </style>
```





```html
<div class="parent">
    <p class="son">会议认为，党的十八大以来，我国经济发展取得历史性成就、
      发生历史性变革，为其他领域改革发展提供了重要物质条件。经济实力
      再上新台阶，经济年均增长7.1%，成为世界经济增长的主要动力源和稳定器。       </p>
</div>
```



这里我们只需要将容器设为display:table然他成为一个块级表格元素，子元素display:table-cell使子元素成为表格单元格，然后就像在表格里一样，给子元素加个vertical-align: middle就行了,多行文字垂直居中啦。



提醒：

* table-cell不感知margin
* 设置float或某些position(如:absolute)会对table-cell布局造成破坏，可以考虑为之增加一个父div定义float等属性







### pointer-events

[https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events](https://developer.mozilla.org/zh-CN/docs/Web/CSS/pointer-events)

#### none

[https://www.zhangxinxu.com/wordpress/2011/12/css3-pointer-events-none-javascript/](https://www.zhangxinxu.com/wordpress/2011/12/css3-pointer-events-none-javascript/)

pointer-events:none

元素永远不会成为鼠标事件的[target](https://developer.mozilla.org/en-US/docs/Web/API/event.target)。但是，当其后代元素的`pointer-events`属性指定其他值时，鼠标事件可以指向后代元素，在这种情况下，鼠标事件将在捕获或冒泡阶段触发父元素的事件侦听器。



举个真实使用例子：

禁用a标签链接或按钮的完美组合是：`pointer-events:none & without href`

[https://www.zhangxinxu.com/study/201112/pointer-events-none-tab-no-href.html](https://www.zhangxinxu.com/study/201112/pointer-events-none-tab-no-href.html)







### getComputedStyle

[网页链接](http://blog.csdn.net/s110902/article/details/73312802?locationNum=12&fps=1)


getComputedStyle是一个可以获取当前元素所有最终使用的CSS属性值。

window.getComputedStyle(element[,pseudo-element]); 

首先是有两个参数，元素和伪类。第二个参数不是必须的，当不查询伪类元素的时候可以忽略或者传入 null。

使用示例

```
let style = window.getComputedStyle(my_div, null);
```

返回值

getComputedStyle 返回的对象是 CSSStyleDeclaration 类型的对象。取数据的时候可以直接按照属性的取法去取数据，例如 style.backgroundColor。

**需要注意的是，返回的对象的键名是 css 的驼峰式写法，background-color -> backgroundColor。** 

需要注意的是 float 属性，根据 《JavaScript 高级程序》所描述的情况 ，float 是 JS 的保留关键字。根据 DOM2 级的规范，**取元素的 float 的时候应该使用 cssFloat。**



例子：

```tsx
let imageWrapper = document.getElementsByClassName('imageWrapper')[0];
let wTransform =getComputedStyle(imageWrapper)['transform'];
```





#### 和 style 的异同

相同点：

getComputedStyle 和 element.style 的相同点就是二者返回的都是 CSSStyleDeclaration 对象，取相应属性值得时候都是采用的 CSS 驼峰式写法，均需要注意 float 属性。

不同点：

* element.style 读取的只是元素的“内联样式”，即 写在元素的 style 属性上的样式；而 getComputedStyle 读取的样式是**最终样式**，包括了“内联样式”、“嵌入样式”和“外部样式”。
* element.style 既支持读也支持写，我们通过 element.style 即可改写元素的样式。而 getComputedStyle 仅支持读并不支持写入。



#### currentStyle

[https://www.huaweicloud.com/articles/a33c8dae11b47cb2ad32238c77beac5f.html](https://www.huaweicloud.com/articles/a33c8dae11b47cb2ad32238c77beac5f.html)

**（尽量不要用这个）**

currentStyle是只兼容各种IE的，但是不兼容火狐，谷歌的，而getComputeStyle的话是可以兼容火狐，谷歌，和IE9+的。





### 字体

#### Serif 和 Sans-serif

Serif和Sans-serif是两种不同的字体类型。区别主要体现在字母的笔画形状和是否有衬线（serif）。

**衬线字体，意思是在字的笔画开始、结束的地方有额外的装饰，而且笔画的粗细会有所不同。**

**无衬线体是无衬线字体，没有这些额外的装饰，而且笔画的粗细差不多。**

---

1. 字母形状：Serif字体的字母笔画末端有装饰性的小横线，这些横线称为“衬线”。Sans-serif字体的字母笔画末端没有衬线，笔画粗细均匀。
2. 可读性：由于有衬线的装饰性，Serif字体在纸质印刷文本中通常更容易阅读，因为衬线有助于将字母与其他字母区分开。Sans-serif字体在电子屏幕上更易读，尤其是较小的字号，因为没有衬线使得字母更加清晰。
3. 风格：Serif字体通常被认为更传统、正式和优雅，适合于印刷品和长文本。Sans-serif字体则更现代、简洁和直接，适合于电子屏幕以及需要强调简洁性的设计风格。

总的来说，Serif和Sans-serif字体的选择取决于使用场景和个人喜好。在网页设计中，Sans-serif字体通常被用于主标题和大段文字，而Serif字体则用于副标题和短文本。



###  **will-change** 

`will-change`是`CSS3`新增的标准属性，它的作用很单纯，就是`"增强页面渲染性能"`，当我们在通过某些行为触发页面进行大面积绘制的时候，浏览器往往是没有准备，只能被动的使用CUP去计算和重绘，由于事先没有准备，对于一些复杂的渲染可能会出现掉帧、卡顿等情况。

而`will-change`则是在真正的行为触发之前告诉浏览器可能要进行重绘了，相当于浏览器把CUP拉上了，能从容的面对接下来的变形。

常用的语法主要有：

- `whil-change: scroll-position;` 即将开始滚动
- `will-change: contents;` 内容要动画或者变化了
- `will-transform;` transform相关的属性要变化了(常用)

注意：

- `will-change`虽然可以开启加速，但是一定要适度使用
- 开启加速的代价为手机的耗电量会增加
- 使用时遵循最小化影响原则，可以对伪元素开启加速，独立渲染
- 可以写在伪类中，例如`hover`中，这样移出元素的时候就会自动`remove`掉`will-change`了
- 如果使用`JS`添加了`will-change`，注意要及时`remove`掉，方式就是`style.willChange = 'auto'`









### Data URL

使用Data URL技术，图片数据以base64字符串格式嵌入到了页面中，与HTML成为一体

```html
 <img src="data:image/gif;base64,R0...">
```

一个完整的 dataURI 应该是这样的：

`data:[<mediatype>][;base64],<data>`

其中mediatype声明了文件类型，遵循MIME规则，如“image/png”、“text/plain”；之后是编码类型，这里我们只涉及 base64

优点：

* 减少请求

缺点：


* Base64编码的数据体积通常是原数据的体积4/3，也就是Data URL形式的图片会比二进制格式的图片体积大1/3。
* Data URL形式的图片一般情况下不会被浏览器缓存，这意味着每次访问这样页面时都被下载一次。这是一个使用效率方面的问题——尤其当这个图片被整个网站大量使用的时候。

---

Data URL如何浏览器缓存中？

**答案是：通过CSS样式文件。CSS中的url操作符是用来指定网页元素的背景图片的，而浏览器并不在意URL里写的是什么——只要能通过它获取需要的数据。所以，我们就有了可以将Data URL形式的图片存储在CSS样式表中的可能。而所有浏览器都会积极的缓存CSS文件来提高页面加载效率。**

```css
.test
  {
  width: 100px;
  height: 100px;
  background-image: url("data:image/gif;base64,R0lGODlhAwADAIAAAP///8zMzCH5BAAAAAAALAAAAAADAAMAAAIEBHIJBQA7");
  }
```





### create-keyframe-animation

[网页链接](http://blog.csdn.net/sunhangye/article/details/78731621)


>使用JavaScript在浏览器中动态生成CSS关键帧动画。

[github地址](https://github.com/HenrikJoreteg/create-keyframe-animation)


在项目中npm下载


```tsx
var animations = require('create-keyframe-animation')
```

这里配合vue transition js钩子函数 enter after-enter

```tsx
enter(el, done) {
    // 设置动画帧数
    let animation = {
            0: {
              transform: `translate3d(${x}px, ${y}px, 0) scale(${scale})`
            },
            60: {
              transform: 'translate3d(0, 0, 0) scale(1.2)'
            },
            100: {
              transform: 'translate3d(0, 0, 0) scale(1)'
            }
          }

    // 注册动画
    animations.registerAnimation({
      name: 'move',
      // 插入自定义的动画
      animation,
      // 参数配置
      presets: {
        duration: 1000, // 持续时间
        easing: 'linear', // 过度效果
        delay: 500 // 延迟时间
        terations: 1, // 实现动画的次数
    　　　delay: 0, // 延迟 
    　　　direction: ‘normal‘, // 方向
    　　　resetWhenDone: false, // if true ：将最后动画状态应用为“变换”属性
    　　　clearTransformsBeforeStart: false // 是否在动画开始之前清除现有的转换
      }
    })

    animations.runAnimation(el, 'move', function () {
        // callback gets called when its done
    })
},
  
afterEnter() {
    // 取消动画
   animations.unregisterAnimation('move')
   this.$refs.cdWrapper.style.animation = ''
}
```










[1]: http://www.w3chtml.com/css3/properties/user-interface/images/content-box.png
[2]: http://www.w3chtml.com/css3/properties/user-interface/images/border-box.png
[3]: http://images2015.cnblogs.com/blog/1098079/201702/1098079-20170209145030213-330247522.png
[4]: http://images2015.cnblogs.com/blog/1098079/201702/1098079-20170209145334791-2090940407.png























