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


## 正文


### 选择器

```html
element>element(子选择期) 	div>p 	选择父级是 <div> 元素的 <p> 元素

element+element(相邻兄弟选择器) 	div+p 	选择紧接着<div>元素之后的<p>元素

element+element(通用兄弟选择器) 	div+p 	选择<div>元素之后的所有<p>元素

```

>这三个老是忘记 Orz 做个笔记 记录一下。。。



#### 交集选择器



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



#### 为何CSS不支持父选择器

这个问题的答案和“为何CSS相邻兄弟选择器只支持后面的元素，而不支持前面的兄弟元素？”是一样的。

浏览器解析HTML文档，是从前往后，由外及里的。所以，我们时常会看到页面先出现头部然后主体内容再出现的加载情况。



但是，如果CSS支持了父选择器，那就必须要页面所有子元素加载完毕才能渲染HTML文档，因为所谓“父选择器”，就是后代元素影响祖先元素，如果后代元素还没加载处理，如何影响祖先元素的样式？于是，网页渲染呈现速度就会大大减慢，浏览器会出现长时间的白板。加载多少HTML就可以渲染多少HTML，在网速不是很快的时候，就显得尤为的必要。比方说你现在看的这篇文章，只要文章内容加载出来就可以了，就算后面的广告脚本阻塞了后续HTML文档的加载，我们也是可以阅读和体验。但是，如果支持父选择器，则整个文档不能有阻塞，页面的可访问性则要大大降低。






### 浮动 


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








### @keyframes

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


### box-sizing

[网页链接](http://www.w3chtml.com/css3/properties/user-interface/box-sizing.html)

box-sizing：content-box | border-box
默认值：content-box
适用于：所有接受width和height的元素
继承性：无

#### content-box：
padding和border不被包含在定义的width和height之内。对象的实际宽度等于设置的width值和border、padding之和，即 ( Element width = width + border + padding )

此属性表现为标准模式下的盒模型。


#### border-box：
padding和border被包含在定义的width和height之内。对象的实际宽度就等于设置的width值，即使定义有border和padding也不会改变对象的实际宽度，即 ( Element width = width )

此属性表现为怪异模式下的盒模型。

**示例：**

```css
content-box:
.test1{ box-sizing:content-box; width:200px; padding:10px; border:15px solid #eee; }
```

![enter description here][1]

```css
border-box:
.test2{ box-sizing:border-box; width:200px; padding:10px; border:15px solid #eee; }
```

![enter description here][2]




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




### 权重


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

----------


**属性选择器** 
a[href] {color:red;}


**伪元素选择器**
伪元素的效果是需要通过添加一个实际的元素才能达到的。
```css
/* 为某个元素的第一行文字使用样式。 */
:first-line
/* 为某个元素中的文字的首字母或第一个字使用样式。 */
:first-letter
/* 在某个元素之前插入一些内容。 */
:before
/* 在某个元素之后插入一些内容。 */
:after
```

>Css3之后伪元素 要用::

----------


1. 如果样式上加有!important标记，例如：
`p{ color: gray !important}`
那么始终采用这个标记的样式。
2. 匹配的内容按照CSS权重排序，权重大的优先。
3. 如果权重也一样，按照它在CSS样式表里声明的顺序，后声明的优先，例如：
```css
h1 {color: blue}
h1 {color: red}

最终胜出的是color: red。
```

#### 定义合适的选择符
　明白了CSS选择符的权重后，我们如何依照选择符的权重定义合适的选择符？

　　定义选择符的原则是：尽量使选择付的权重低，目的是保证样式在应用于多个元素时容易被覆盖，这可提高样式代码的重用性和可维护性。

具体的原则如下：

1. CSS样式中尽量不要使用ID选择器
ID选择器有很高的权重，如果要覆盖使用了ID选择器的样式，就必须在原先使用ID选择器的基础上添加新的选择符（类选择器或者标签类型选择器或者额使用个!important,但这样做的结果是无法重用的样式代码会越来越多）。
2. 减少子选择器的层级
减少选择器的层级的过程也是降低选择符整体权重的过程。
3. 使用组合的CSS类选择器
使用CSS选择器组合的方式，开发者可以不用考虑CSS样式覆盖的问题，避开了计算选择符权重的过程，同时也提高了代码的重用性。




#### !important修改权重

[网页链接](https://www.cnblogs.com/tianma3798/p/6202008.html)


!important为开发者提供了一个增加样式权重的方法。应当注意的是!important是对整条样式的声明，包括这个样式的属性和属性值。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
    .div1 {
        color: red !important;
        /*color: red;*/
    }
    
    #div1 {
        color: blue;
        font-weight: bold;
    }
    </style>
</head>

<body>
    <div class="div1" id='div1' style='color:green;'>
        <p>div1内容</p>
        <span>span中的内容</span>
    </div>
</body>

</html>
```

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









### transform-origin



[网页链接](http://blog.csdn.net/xu_ya_fei/article/details/51711968)

transform-origin 属性允许您改变被转换元素的位置。
默认值： 50% 50% 0
2D 转换元素能够改变元素 x 和 y 轴。3D 转换元素还能改变其 Z 轴。

#### 语法
transform-origin: x-axis y-axis z-axis;

x-axis
定义视图被置于 X 轴的何处。可能的值：
left center right length %


y-axis	
定义视图被置于 Y 轴的何处。可能的值：
top center bottom length %

z-axis
定义视图被置于 Z 轴的何处。可能的值：
length

```
/*只设置一个值的语法*/
transform-origin: x-offset
transform-origin: offset-keyword
```

----------


```
/*设置两个值的语法*/
transform-origin：x-offset  y-offset
transform-origin：y-offset  x-offset-keyword
transform-origin：x-offset-keyword  y-offset
transform-origin：x-offset-keyword  y-offset-keyword
transform-origin：y-offset-keyword  x-offset-keyword
```

----------


```
/*设置三个值的语法*/
transform-origin：x-offset  y-offset  z-offset
transform-origin：y-offset  x-offset-keyword  z-offset
transform-origin：x-offset-keyword  y-offset  z-offset
transform-origin：x-offset-keyword  y-offset-keyword  z-offset
transform-origin：y-offset-keyword  x-offset-keyword  z-offset
```

* x-offset：用来设置transform-origin水平方向Ｘ轴的偏移量，可以使用和值，同时也可以是正值（从中心点沿水平方向Ｘ轴向右偏移量），也可以是负值（从中心点沿水平方向Ｘ轴向左偏移量）。
* y-offset：用来设置transform-origin属性在垂直方向Ｙ轴的偏移量，可以使用和值，同时可以是正值（从中心点沿垂直方向Ｙ轴向下的偏移量），也可以是负值（从中心点沿垂直方向Ｙ轴向上的偏移量）。
* z-offset：用来设置3D变形中transform-origin远离用户眼睛视点的距离，默认值z=0，其取值可以，不过在这里将无效。

----------


* offset-keyword：是top、right、bottom、left或center中的一个关键词，可以用来设置transform-origin的偏移量。
* x-offset-keyword：是left、right或center中的一个关键词，可以用来设置transform-origin属性值在水平Ｘ轴的偏移量。
* y-offset-keyword：是top、bottom或center中的一个关键词，可以用来设置transform-origin属性值在垂直方向Ｙ轴的偏移量。

----------


* top = top center = center top = 50% 0
* right = right center = center right = 100%或(100% 50%)
* bottom = bottom center = center bottom = 50% 100%
* left = left center = center left = 0或(0 50%)
* center = center center = 50%或（50% 50%）
* top left = left top = 0 0
* right top = top right = 100% 0
* bottom right = right bottom = 100% 100%
* bottom left = left bottom = 0 100%

  





### background-attachment

**网页背景图固定不动**


背景附着(background-attachment)属性有两个值。一个是scroll，表示随内容滚动而动；一个是fixed，表示固定不动，不受内容滚动影响。缺省值是scroll。

* **background-repeat:no-repeat**
* **background-attachment:fixed**


```html
<html>
<head>
<title>背景附着属性 background-attachment</title>
<style type="text/css">
body {background-image:url(../images/css_tutorials/background.jpg); background-repeat:no-repeat; background-attachment:fixed} </style> 
</head>
<body> 
<p>这个HTML使用了CSS的background-attachment属性，将背景图片固定，不随内容滚动而滚动。<p>
<p>背景附着(background-attachment)属性有两个值。一个是scroll，表示随内容滚动而动；一个是fixed，表示固定不动，不受内容滚动影响。缺省值是scroll。</p>
<p>background-attachment要和background-image一起用。</p>
</body>
</html>
```






### clearfix清除浮动





[网页链接](http://www.jianshu.com/p/9d6a6fc3e398)

通常我们在写html+css的时候，如果一个父级元素内部的子元素是浮动的（float），那么常会发生父元素不能被子元素正常撑开的情况

要解决此问题，我们可以给包裹层(包裹了float元素)添加一个类，叫做clearfix，下面是clearfix的实现形式（之一）：


**正是由于“块级元素”具有换行特性，因此理论上它都可以配合 clear 属性来清除浮动 带来的影响**


**最好的方式**
```
.clearfix:after {
   content:""; 
   display: table;  // 也可以是 block，或者是 list-item 
   clear:both; 
}
```
上述代码通过伪类 :after 在container后添加内容（content），来实现清除浮动。


```
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



作者：Wenliang
[链接](http://www.jianshu.com/p/9d6a6fc3e398)
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。







### @supports



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



-----




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










[1]: https://gss0.baidu.com/-Po3dSag_xI4khGko9WTAnF6hhy/zhidao/wh=600,800/sign=4a0826fc9d22720e7b9beafc4bfb267e/b219ebc4b74543a9c1d979521b178a82b8011469.jpg











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









### 伪类before/after图片不能设置宽高？

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





---





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













### app内嵌h5顶部隔开状态栏



![](https://s1.ax1x.com/2020/04/14/GzQhkT.gif)





其实就是app自带tabbar 和 手机自带状态栏 



我们做内嵌app的h5要隔离开这两个距离



----





实现：



```html
  <div class="schedule-wrapper" :style="{paddingTop: headerHeight + 'px'}">
    <div class="schedule-scroll">
        // 内容
    </div>
  </div>  
```



headerHeight 这里是计算出来的



一般调用移动提供的方法 拿到tabbar和状态栏这两个的高度，headerHeight 就是这两个高度的和。





```css
.schedule-wrapper {		
  position: relative;
  width:100%
  height: 100vh;
  overflow: hidden;
    .schedule-scroll {
        position: relative;
        width: 100%;
        height: 100%;
        overflow: scroll;
    }
 }   
```











### 在ios中触发伪类active



 :active伪类常用于设定点击状态下或其他被激活状态下一个链接的样式。最常用于锚点`<a href="#">`这种情况，一般主流浏览器下也支持其他元素 



 **值得注意的是：**伪类是一种比较方便的实现方式，但在ios中，需要在相关的元素或者`body`上绑定`touchstart`事件才能使元素的`:active`生效。 



```js
// 在main.js

// iOS 必须绑定 touch 事件才能触发 :active 伪类的 CSS 效果
document.body.addEventListener('touchstart', function noop() { })

```





### position:sticky



[ https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/ ]( https://www.zhangxinxu.com/wordpress/2018/12/css-position-sticky/ )



[ https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/ ]( https://www.zhangxinxu.com/wordpress/2020/03/position-sticky-rules/ )



 **sticky元素效果完全受制于父级元素们** 



这和`position:fixed`定位有着根本性的不同，fixed元素直抵页面根元素，其他父元素对其left/top定位无法限制。



 sticky元素以下一些特性表现： 



* 父级元素不能有任何`overflow:visible`以外的overflow设置，否则没有粘滞效果。因为改变了滚动容器（即使没有出现滚动条）。因此，如果你的`position:sticky`无效，看看是不是某一个祖先元素设置了`overflow:hidden`，移除之即可。
*  父级元素设置和粘性定位元素等高的固定的`height`高度值，或者高度计算值和粘性定位元素高度一样，也没有粘滞效果。 
*  同一个父容器中的sticky元素，如果定位值相等，则会重叠；如果属于不同父元素，且这些父元素正好紧密相连，则会鸠占鹊巢，挤开原来的元素，形成依次占位的效果 
*  sticky定位，不仅可以设置`top`，基于滚动容器上边缘定位；还可以设置`bottom`，也就是相对底部粘滞。如果是水平滚动，也可以设置`left`和`right`值。 
* 定位用的`bottom`，效果和`top`正好是对立的。设置`top`粘滞的元素随着往下滚动，是先滚动后固定；而设置`bottom`粘滞的元素则是先固定，后滚动；











### nth-of-type 和 nth-child



 div：nth-of-type（n） 

 代表他是第n个类型为div的子元素，不管在他之前有多少 个同级元素，只要不是div类型就都不算数，就是说这个div元素不一定在父元素中是第n个子元素，但他一定是第n个div元素 



 div：nth- child（n） 

 就代表他是父元素的div元素，并且！并且他是爸爸元素的第n个孩子元素，他之前的所有同级元素都算数 

 如果第n个子元素不是div元素，那对不起，爸爸要的不是你，爸爸宁可没有儿子也不会乱认儿子 



 **总结 ：nth- child的要求比较高，可以多用nth-of-type**



补充

nth-of-type(2n)   =>  设置偶数元素样式

nth-of-type(2n+1)  => 设置奇数元素样式





### 去除type:number时input的箭头



```css
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
}
input[type="number"]{
    -moz-appearance: textfield;
}

```



另外补充一点  即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 `parseFloat()` 解析，则会返回原始的值。 



如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```html
<input v-model.number="age" type="number">
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





### 父元素padding子元素absolute相对于父元素哪里定位

[https://blog.csdn.net/qq_41064446/article/details/78623429](https://blog.csdn.net/qq_41064446/article/details/78623429)



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
      /* 父元素的padding-top不起作用 */
      top:0;
      width:100px;
      height: 100px;
      background: rebeccapurple;   
    }
```





![](https://s1.ax1x.com/2020/08/07/ahF7UP.png)









### 宽高1:1切割图片

我们有一张长方形的图片，希望切出长宽相等的正方形出来，同时这个的边长为长方形的宽。



我们可以使用css中background属性实现



```css
 .test{
      width: 160px;
      height: 160px;
      background-image: url("./touxiang.png");
      background-position: 0 0;
      // 宽度100% 高度自适应
      background-size: 100% auto;  
      background-repeat: no-repeat;
    }
```







![](https://s1.ax1x.com/2020/08/10/aHmsUK.png)





## css3





###  @supports 



@supports是CSS3新引入的规则之一，主要用于检测当前浏览器是否支持某个CSS属性并加载具体样式.



```css
@supports (display: grid) {
    .container {
        color: red;
    }
}
```



类似@media媒体查询，当浏览器支持`display:grid`这个CSS属性时才应用其中的样式



此外，还支持逻辑运算符；not, and, or



```css
@supports not(display: grid){...}
@supports (display: grid) and (position: sticky){...}
@supports (display: grid) or (display: flex){...}
```





括号内不一定都要是“关键字”，只要是CSS语法都可以，比如：

```css
@supports (border-radius: 4px) or (--btn-color: red){...}
```







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














































[1]: http://www.w3chtml.com/css3/properties/user-interface/images/content-box.png
[2]: http://www.w3chtml.com/css3/properties/user-interface/images/border-box.png
[3]: http://images2015.cnblogs.com/blog/1098079/201702/1098079-20170209145030213-330247522.png
[4]: http://images2015.cnblogs.com/blog/1098079/201702/1098079-20170209145334791-2090940407.png
























