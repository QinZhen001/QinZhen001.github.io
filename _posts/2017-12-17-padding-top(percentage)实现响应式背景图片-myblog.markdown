---
layout:     post
title:      "padding-top(percentage)实现响应式背景图片"
date:       2017-11-29 23:12:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文


[网页链接](http://ju.outofmemory.cn/entry/22712)


处理响应式布局中背景图片的简单方法是等比例缩放背景图片。我们知道宽度设为百分比的  `<img>` 元素，其高度会随着宽度的变化自动调整，且其宽高比不变。如果想在背景图片中实现同样的效果，我们必须先解决如何保持HTML元素的宽高比。

### 固定宽高比

我们将用到一个保持元素宽高比的技巧：将图片设置成背景图片时，需要保持宽高比，通过设置height:0，然后padding-bottom为原始图片的宽高比(即百分比)，主要还是利用**padding属性为百分比时，参考的是父类容器的宽度**，margin属性设置成百分比也是参考的是父类容器的宽度，只是margin不能撑开子类容器本身




### 例子
假设我们有一张800*450px的图片，我们需要创建一个元素在其宽度变化时，它的宽高比仍保持16:9。代码如下：
```html
<div class="column">
  <div class="figure"></div>
</div>

.column{
  max-width: 800px;
}
.figure{
  padding-top: 56.25%;   // 9/16 = 0.5652
}
```



### 添加背景图片 
上面我们实现了元素缩放并保持宽高比。但是此时如果我们添加了背景图片，它并不能跟随元素一起自动缩放。还需要添加**background-size:cover**。

```css
div.column {
  /* The background image must be 800px wide */
  max-width: 800px;
}
figure.fixedratio {
  padding-top: 56.25%;  /* 450px/800px = 0.5625 */
  background-image: url(http://domain.com/img/sample.jpg);
  background-size: cover;
}
```

[background-size](http://www.topcss.org/demo/background-size-cover-contain.html)

### 流动宽高比
我们可以更深入一步。假设我们有一张在桌面浏览器下显式很好的宽屏图片，在移动设备上我们不想使用相同的宽高比，要不然图片会很小。又或者是我们不想使用相同的高度，因为图片可能会过高。

![enter description here][1]


这个效果可以通过**较少padding的百分比值和为元素设置一个高度**来实现。假设我们的大图是800*200px，我们打算在元素的宽度减少到300px的时候，背景图片的高度为150px。现在我们计算下height和padding-top属性值。

![enter description here][2]


上图显式了两个尺寸的关系。坡度线(slop)对应于padding-top属性，开始高度(start height)对应于height属性，它表示元素在宽度为零时的高度。调整样式如下：
```
div.column {
  /* The background image must be 800px wide */
  max-width: 800px;
}
figure.fluidratio {
  padding-top: 10%;  /* slope */
  height: 120px;  /* start height */
  background-image: url(http://domain.com/img/sample.jpg);
  background-size: cover;
}
```


[1]: http://www.topcss.org/wp-content/uploads/2012/11/fluid-ratio.png
[2]: http://www.topcss.org/wp-content/uploads/2012/11/ratio-calculation.png