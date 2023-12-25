---
layout:     post
title:      "clientWidth和getBoundingClientRect"
date:       2017-11-29 23:12:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”

[网页链接](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Object_Model/Determining_the_dimensions_of_elements)


## 二者的区别

>document.documentElement.clientWidth
document.documentElement.getBoundingClientRect().width;


getBoundingClientRect().width获取到的其实是父级的右边距离浏览器原点(0,0)减去左边距离浏览器原点(0,0)的距离,
**即宽度+2padding+2border**

**clientWidth等于宽度+2padding,不包括边框的宽度**


## offsetWidth和offsetHeight

**If you need to know the total amount of space an element occupies, including the width of the visible content, scrollbars (if any), padding, and border**

一般情况 getBoundingClientRect等于对应offset属性

例如：
**getBoundingClientRect().width === offsetWidth**

但是
In case of transforms, the `offsetWidth` and `offsetHeight` returns the element's layout width and height, while `getBoundingClientRect()` returns the rendering width and height.








