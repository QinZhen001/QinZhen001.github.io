---
layout:     post
title:      "clientHeight,scrollHeight,offsetHeight之间区别"
date:       2017-10-27 20:59:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文

[网页链接](http://www.cnblogs.com/nanshanlaoyao/p/5964730.html)


clientHeight , scrollHeight , offsetHeight相信每个人都用过，可是每次用都要查一下到底哪个是文档大小哪个是视口大小，还有头疼的兼容问题。

* clientHeight：元素客户区的大小，指的是元素内容及其边框所占据的空间大小（经过实践取出来的大多是视口大小）
* scrollHeight: 滚动大小，指的是包含滚动内容的元素大小（元素内容的总高度）
* offsetHeight: 偏移量，包含元素在屏幕上所用的所有可见空间（包括所有的内边距滚动条和边框大小，不包括外边距



一、clientHeight
内容可视区域的高度，也就是说页面浏览器中可以看到内容的这个区域的高度，一般是最后一个工具条以下到状态栏以上的这个区域，与页面内容无关。不包括boder的宽度,如果区域内带有滚动条,还应该减去横向滚动条不可用的高度。

二、offsetHeight（与offsetWidth同理）
offsetHeight = 内容可视区域的高度+ 滚动条 + 边框。
当前对象的高度与style.height属性的区别在于:如对象的宽度设定值为百分比高度,则无论页面变大还是变小,style.height都返回此百分比,而offsetHeight则返回在不同页面中对象的高度值而不是百分比值

三、scrollHeight
scrollHeight返回元素的完整的高度，以像素为单位.

> 一般来说一个元素 clientHeight<offsetHeight<scrollHeight

四、具体
* window.screen.availWidth     返回当前屏幕宽度(空白空间)  
* window.screen.availHeight     返回当前屏幕高度(空白空间)  
* window.screen.width     返回当前屏幕宽度(分辨率值)  
* window.screen.height     返回当前屏幕高度(分辨率值)  


----------


* 网页可见区域宽： document.body.clientWidth
* 网页可见区域高： document.body.clientHeight
* 网页可见区域宽： document.body.offsetWidth (包括边线的宽)
* 网页可见区域高： document.body.offsetHeight (包括边线的高)
* 网页正文全文宽： document.body.scrollWidth
* 网页正文全文高： document.body.scrollHeight
* 网页被卷去的高： document.body.scrollTop
* 网页被卷去的左： document.body.scrollLeft

----------

对应的dom元素的宽高有以下几个常用的：

* 元素的实际高度：document.getElementById("div").offsetHeight
* 元素的实际宽度：document.getElementById("div").offsetWidth
* 元素的实际距离左边界(**HTMLElement.offsetParent 节点**)的距离：document.getElementById("div").offsetLeft
* 元素的实际距离上边界(**HTMLElement.offsetParent 节点**)的距离：document.getElementById("div").offsetTop


----------


**documentElement 和 body 相关说明**

body是DOM对象里的body子节点，即 `<body>` 标签；

documentElement 是整个节点树的根节点root，即`<html>` 标签；


----------

最后下面两个函数解决了这个问题，兼容了不同的浏览器。
```
/*视口的大小，部分移动设备浏览器对innerWidth的兼容性不好，需要
 *document.documentElement.clientWidth或者document.body.clientWidth
 *来兼容（混杂模式下对document.documentElement.clientWidth不支持）。
 *使用方法 ： getViewPort().width;
 */
function getViewPort () {
    if(document.compatMode == "BackCompat") {   //浏览器嗅探，混杂模式
        return {
            width: document.body.clientWidth,
            height: document.body.clientHeight
        };
    } else {
        return {
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        };
    }
}
```


```
//获得文档的大小（区别与视口）,与上面获取视口大小的方法如出一辙
function getDocumentPort () {
    if(document.compatMode == "BackCompat") {
        return {
            width: document.body.scrollWidth,
            height: document.body.scrollHeight
        };
    } else {
        return {
            width: Math.max(document.documentElement.scrollWidth,document.documentElement.clientWidth),
            height: Math.max(document.documentElement.scrollHeight,document.documentElement.clientHeight)
        }
    }
}
```



## 补充
element.style.width 获取的是元素内容的宽度 **只能取到元素行内样式的宽度值，内嵌样式和外部样式取不到**

element.offsetWidth 获取的是整个元素的宽度**包括边框和内边距**


扩展：那么offsetLeft和offsetTop呢?

答： offsetLeft = left + marginLeft
     offsetTop = top +marginTop
     
     
     

### jquery中的offset()

offset() 方法返回或设置匹配元素相**对于文档的偏移**（位置）。
     
>这个跟document.getElementById("xxx").offsetLeft不一样

**offset()其实相当于原生js的getBoundingClientRect()**


### js 获取对象相对于页面四边的距离

getBoundingClientRect()
这个方法返回一个矩形对象，包含四个属性：left、top、right和bottom。分别表示元素各边与页面上边和左边的距离。

* var box=document.getElementById('box');         // 获取元素
* alert(box.getBoundingClientRect().top);         // 元素上边距离页面上边的距离
* alert(box.getBoundingClientRect().right);       // 元素右边距离页面左边的距离
* alert(box.getBoundingClientRect().bottom);      // 元素下边距离页面上边的距离
* alert(box.getBoundingClientRect().left);        // 元素左边距离页面左边的距离





### 获取滚动高度 

一般滚动条都是覆盖在在body上的，所以我们采用兼用写法

```js
document.documentElement.scrollTop || document.body.scrollTop || window.pageYOffset
```





## 问题 



### scrollTop一直为0 

[https://blog.csdn.net/kouryoushine/article/details/99745904](https://blog.csdn.net/kouryoushine/article/details/99745904)

> 如果想设置滚动条属性，一定要知道滚动条是哪个div产生的。

**什么时候存在滚动条？**

页面布局我们可以简单理解为 div盒子套div盒子。当内侧div盒子的高度大于外部时，外部的div就会出现滚动条。此时外部的div才具备scrollTop属性，否则你无论如何设置都为0.

这里隐藏了一些条件：

1. 外部的div一定有高度，否则外部div将和内部div高度一致，不满足大于条件，所以不会出现滚动条。
2. 外部div的 overflow：auto。hidden的情况下超出的部分被隐藏，无法出现滚动条。

```css
   #outer {
      margin: 100px 50px;
      background: url(http://images.cnblogs.com/cnblogs_com/wenruo/873448/o_esdese.jpg);
      height: 100px;
      width: 50px;
      padding: 10px 50px;
      overflow: scroll;
    }

    #inner {
      height: 200px;
      width: 50px;
      background-color: #d0ffe3;
    }
```

```html
<body>
  <div id="outer">
    <div id="inner"></div>
  </div>
</body>
```

```js
  const outer = document.getElementById('outer')
  const inner = document.getElementById('inner')
  
  setInterval(() => {
    console.log(outer.scrollTop)  // 一直为0
  }, 2000);
```

为什么输出的scrollTop一直为0呢？

因为此处的滚动条覆盖到了body上了，而不是outer

```css
 body{overflow:hidden;}
```

当我们加上这一个时，可以正确获取outer.scrollTop