---
layout:     post
title:      "CSS 的overflow:hidden"
date:       2017-07-22 14:44:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文
[网页链接](http://jingyan.baidu.com/article/d45ad148e2a7f969552b80ae.html)

overflow:hidden这个CSS样式是大家常用到的CSS样式，但是大多数人对这个样式的理解仅仅局限于隐藏溢出，而对于清除浮动这个含义不是很了解。一提到清除浮动，我们就会想到另外一个CSS样式：clear:both，我相信对于这个属性的理解大家都不成问题的。但是对于“浮动”这个词到底包含什么样的含义呢？我们下面来详细的阐述一下。 

这是一个常用的div写法，下面我们来书写样式。大家可以在DMX中自己做试验

 #box{ 

          width:500px; 
    
          background:#000; 
    
          height:500px;

 } 

#content { 

          float:left; 
    
          width:600px; 
    
          height:600px; 
    
          background:red;

 } 

　　给box这个div加了一个overflow:hidden这个属性解决了这个问题。我们直到overflow:hidden这个属性的作用是隐藏溢出，给box加上这个属性后，我们的content
　　的宽高自动的被隐藏掉了。另外，我们再做一个试验，将box这个div的高度值删除后，我们发现，box的高度自动的被content
　　这个div的高度值给撑开了。说到这里，我们再来理解一下“浮动”这个词的含义。我们原先的理解是，在一个平面上的浮动，但是通过这个试验，我们发现，这不仅仅是一个平面上的浮动，而是一个立体的浮动！也就是说，当content
　　这个div加上浮动这个属性的时候，在显示器的侧面，它已经脱离了box这个div，也就是说，此时的content 的宽高是多少，对于已经脱离了的box来说，都是不起作用的。当我们全面的理解了浮动这个词的含义的时候，我们就理解overflow:hidden这个属性中的解释，清除浮动是什么意思了。也就是说，当我们给box这个div加上overflow:hidden这个属性的时候，其中的content等等带浮动属性的div的在这个立体的浮动已经被清除了。这就是overflow:hidden这个属性清除浮动的准确含义。当我们没有给box这个div设置高度的时候，content
　　这个div的高度，就会撑开box这个div，而在另一个方面，我们要注意到的是，当我们给box这个div加上一个高度值，那么无论content 这个div的高度是多少，box这个高度都是我们设定的值。而当content
　　的高度超过box的高度的时候，超出的部分就会被隐藏。这就是隐藏溢出的含义！
　　

---

## 后记

在IE6下，当子容器的宽高超出父容器时，父容器就会被撑开来。
要想解决这个问题，在父容器中除定义宽和高的值以外，还必须写overflow:hidden，这样就能把子容器的其它内容隐藏。

---

overflow:hidden属性相当于是让父级紧贴内容，这样即可紧贴其对象内内容（包括使用float的div盒子），从而实现了清除浮动。而clear:both则是采用的是在子级清除浮动。你看看使用的位置就清楚了。


``` html
<div style="overflow:hidden">
   <div style="float:left"></div>
</div>
```




### overflow:hidden 剪裁界线 
[http://demo.cssworld.cn/6/4-1.php](http://demo.cssworld.cn/6/4-1.php)




一个设置了overflow:hidden声明的元素，假设同时存在border属性和padding属 性，类似于下面的 CSS 代码


```css
 .box { 
     width: 200px;
     height: 80px;   
     padding: 10px;  
     border: 10px solid; 
     overflow: hidden; 
 } 
```


**则当子元素内容超出容器宽度高度限制的时候，剪裁的边界是border box的内边缘，而非padding box 的内边缘**





如果想实现元素剪裁同时四周留有间隙的效果的话，可以试试使用透明边框，此时内间距 padding 属性是无能为力的


，深入探讨一下overflow属性的一个很经典的不兼容问题，即 Chrome 浏览器下，如果容 器可滚动（假设是垂直滚动），则 padding-bottom也算在滚动尺寸之内，IE 和 Firefox 浏览器 忽略padding-bottom。例如，上面的.box，我们把 overflow属性值改成auto（亦可点击 实例页面图片），滚动到底部会发现，Chrome 浏览器下面是有 10 像素的空白的,Firefox 和 IE 却没有



所以我们在实际项目开发的时候，要尽量避免滚动容器设置 padding-bottom 值，除 了样式表现不一致外，还会导致 scrollHeight 值不一样，这往往会给开发带来难以察觉的 麻烦，需要引起注意。 




### 关于滚动条


----------




假设一个元素的宽度是400px，CSS 代码如下：

``` 
.box {  
    width: 400px; 
    height: 100px;  
    overflow: auto; 
} 
```


当子元素高度超过100px出现滚动条的时候，子元素可用的实际宽度实际上要小于400px，因为滚 动条（准确地说应该是滚动栏）占据了一定的宽度


在移动端就不会 有这样的问题，因为移动端的屏幕尺寸本身就有限，滚动条一般都是悬浮模式，不会占据可用宽度， 但是在 PC 端，尤其 Windows 操作系统下，几乎所有浏览器的滚动栏都会占据宽度，而且这个宽度大 小是固定的。


IE7 及以上版本 IE、Chrome、Firefox 浏览器滚动栏所占据的宽度均是**17px**


### 自定义滚动条

滚动条是可以自定义的。因为 IE 浏览器的自定义效果实在是比原生的还要难看，就不浪费 大家时间了，就此打住。 


倒是



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
























