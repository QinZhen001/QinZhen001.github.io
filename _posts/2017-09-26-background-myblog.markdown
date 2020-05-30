---
layout:     post
title:      "background"
date:       2017-09-26 15:50:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.w3school.com.cn/cssref/pr_background-size.asp)

### background-size

background-size 属性规定背景图像的尺寸。
**background-size: length|percentage|cover|contain;**

* length	
设置背景图像的高度和宽度。
第一个值设置宽度，第二个值设置高度。
如果只设置一个值，则第二个值会被设置为 "auto"。

* percentage	
以父元素的百分比来设置背景图像的宽度和高度。
第一个值设置宽度，第二个值设置高度。
如果只设置一个值，则第二个值会被设置为 "auto"。

* cover	
把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。
背景图像的某些部分也许无法显示在背景定位区域中。

* contain	
  把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。

[例子](http://www.topcss.org/demo/background-size-cover-contain.html)

####  background-size:cover
背景铺满元素

1. 调整背景图片的宽度或高度（较小者），以铺满整个元素
2. 保持背景图片的宽高比

####  background-size:contain
元素包含整个背景图片

1. 调整背景图片的宽度或高度（较大者），使背景图片完全包含在元素中
2. 保持背景图片的宽高比





### background-attachment 

[http://www.w3school.com.cn/cssref/pr_background-attachment.asp](http://www.w3school.com.cn/cssref/pr_background-attachment.asp)

[ https://juejin.im/post/5b6d0756e51d4562b31ad23c ]( https://juejin.im/post/5b6d0756e51d4562b31ad23c )



[background-attachment 效果查看]( https://codepen.io/Chokcoco/pen/xJJorg )



```css
body 
  { 
      background-image: url(bgimage.gif); 
      background-attachment: fixed;
  }
```





* scroll 	默认值。 此关键字表示背景相对于元素本身固定   **(看起来效果就是背景静止 )**
* fixed 	 此关键字表示背景相对于视口固定。即使一个元素拥有滚动机制，背景也不会随着元素的内容滚动。 **(看起来效果就是背景运动)**



> 注意一下 scroll 与 fixed，一个是相对元素本身固定，一个是相对视口固定，有点类似 `position` 定位的 `absolute` 和 `fixed`。 





### background-position 
[http://www.w3school.com.cn/cssref/pr_background-position.asp](http://www.w3school.com.cn/cssref/pr_background-position.asp)

```
body
{ 
background-image:url('bgimage.gif');
background-repeat:no-repeat;
background-attachment:fixed;
background-position:center;
}
```

**background-position负值定位**


[http://www.php230.com/1411189681.html](http://www.php230.com/1411189681.html)


1. 原点位置，即外层块元素的左上角
2. background-position 位置设定是指图片与坐标原点的偏移量
3. 原点是不会动的，移动的是图片 X坐标为正则图片左上角向右平移，为负则图片左上角向左平移
4. Y坐标为正则图片左上角向下平移，为负则左上角向上平移
5. 百分比的计算是有公式的：X轴( container宽度 – 图片宽度 )*含符号百分比
6. Y轴( container高度 – 图片高度)*含符号百分比

**所以在雪碧图或者扣图时background-position都用负值**


### background-clip 
background-clip: border-box|padding-box|content-box;

| border-box  | 背景被裁剪到边框盒。   |
| ----------- | ---------------------- |
| padding-box | 背景被裁剪到内边距框。(默认值) |
| content-box | 背景被裁剪到内容框。   |



## 补充

### background-size设置为百分比
有时候我们需要利用background-size 让精灵图实现自适应缩放。
```css
 background-size: 300% 100%;
```


### background-position设置为百分比
[http://www.zhangxinxu.com/wordpress/2015/03/background-object-position-value-percent/](http://www.zhangxinxu.com/wordpress/2015/03/background-object-position-value-percent/)

background-position中的百分比单位是个很有意思的东西。其表现与CSS中其他的百分比单位表现都不一样。


**这就是为何background-position:100% 100%是定位在容器右下角的原因。**


那position值对应的容器坐标位置该如何计算呢？


实际上是有一个公式的：
```
positionX = (容器的宽度-图片的宽度) * percentX;
positionY = (容器的高度-图片的高度) * percentY;
```


因此，当background-position:100% 100%时候，实际定位值就是容器尺寸和图片尺寸的差异，于是，就有了右下角定位效果。



有个这个公式，我们也能理解百分比负值的一些表现了，比方说你觉得下面两行CSS对应图片的表现是？

```css
background-position: -50% -50%;

object-position: -50% -50%
```

深受传统百分比定位迷惑的我们可能一时间会想不通，明明是个负值百分比定位，怎么会是一个正值效果呢？这不科学啊！

因为:
(容器的宽度-图片的宽度) * -50% 的结果是个正值；
(容器的高度-图片的高度) * -50% 的结果也是个正值;



### background-color和background-image一起用的问题
可以写成background：color url();

合并起来写 只有只有这样的顺序才有效，分开写也可以，不过color必须写在前面，image必须写在后面才生效。








