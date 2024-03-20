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



### 扩大点击区域

常用于移动端

```css
.xxx::before {
    content: '';
    position: absolute;
    top: -5px;
    right: -5px;
    bottom: -5px;
    left: -5px;
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



### AspectRatio 宽高比

```tsx
const AspectRatio: FC<{
  aspectRatio: number;  // 宽高比
  children: ReactNode;
  className?: string;
  style?: CSSProperties;
  ref?: ForwardedRef<any>;
}> = forwardRef(({ aspectRatio, children, className, style }, ref) => {
  return (
    <div
      style={{
        width: "100%",
        height: 0,
        position: "relative",
        paddingBottom: 100 / aspectRatio + "%",
      }}
    >
      <div
        ref={ref}
        style={{
          position: "absolute",
          top: 0,
          right: 0,
          bottom: 0,
          left: 0,
          ...style,
        }}
        className={className}
      >
        {children}
      </div>
    </div>
  );
});
```



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











### 高度自适应

[网页链接](https://segmentfault.com/a/1190000004231995)

**当margin/padding取形式为百分比的值时，无论是left/right，还是top/bottom，都是以父元素的width为参照物的！**

> margin/padding-top/bottom 的百分比之所以按照 width 计算，其实理由很简单，就是要匹配主要的 use cases。那就是——要构建在纵横两个方向上相同的 margin/padding。如果两个百分比的相对方式不同，那用百分比就无法得到垂直和水平一致的留白。



有人也许会问，为什么不是垂直方向上的 height 而是水平方向的 width？

这其实容易理解。因为 CSS 的基本模型是着重于“排版”的需求，因此水平和垂直方向其实并不是同等权重的，更精确的说，是文字书写方向决定的。常见的横排文字时，我们排版的出发点是水平宽度一定，而垂直方向上是可以无限延展的。竖排文字则相反。所以在竖排文字时，margin/padding-* 其实就都按照 height 而不是 width 计算了。类似的且大家更熟悉的是 margin-top/bottom 在垂直方向上的 collapse（或者当竖排文字时是 margin-left/right 水平方向上的 collapse）。

---

为什么只有垂直方向 collapse 而水平就不呢？

因为在典型的排版中，段落间的空白进行 collapse 是常见和方便的。而反过来水平方向上就几乎没有那样的需求（只有表格在两个方向上有对称的 border collapse 的需求）。**同样的，在排版中，横向百分比控制是常见的需求，但是纵向其实很少这样的需求**,有这样需求的其实是GUI界面布局

作者：贺师俊 

链接：https://www.zhihu.com/question/20983035/answer/16801491 

来源：知乎 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

---

宽高不一致的自适应怎么做？

其实自适应的重点在于，元素的宽高必须维持一个固定的比例，比如说宽高一致比例就是1:1，宽是高的两倍那就是2:1，只要这个比例是明确而且固定的，那么只需要相应地修改margin/padding的百分比值即可适应不同的宽高比例。



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



### 列表超出滚动

结构： content  =>  list =>  litem 

```html
  <div class="content">
    <div class="list">
      <div class="item">1</div>
      <div class="item">2</div>
      <div class="item">3</div>
      <div class="item">4</div>
      <div class="item">4</div>
    </div>
  </div>
```

```css
    .content {
      position: relative;
      height: calc(100vh - 500px);
      overflow: hidden;
    }

    .list{
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      overflow: auto;
    }

    .item{
      height: 100px;
      width: 100%;
    }
```



