---
layout:     post
title:      "animation"
date:       2017-11-30 19:49:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”


## 正文


[网页链接](http://www.w3school.com.cn/cssref/pr_animation.asp)

animation 属性是一个简写属性，用于设置六个动画属性：
* animation-name
* animation-duration
* animation-timing-function
* animation-delay
* animation-iteration-count
* animation-direction



>animation: doUpDown .5s ease-in-out infinite alternate both


### animation-direction 属性
animation-direction 属性定义是否应该轮流反向播放动画。

如果 animation-direction 值是 "alternate"，则动画会在奇数次数（1、3、5 等等）正常播放，而在偶数次数（2、4、6 等等）向后播放。

animation-direction: normal | alternate;
* normal 	默认值。动画应该正常播放。 	测试
* alternate 	动画应该轮流反向播放。

### animation-fill-mode
animation-fill-mode : none | forwards | backwards | both;

* none 	不改变默认行为。
* forwards 	当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）。
* backwards 	在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）。
* both 	向前和向后填充模式都被应用。



动画按执行时间来划分，它分为三个过程，或者说一次动画过程可以将元素划分为三个状态：**动画等待、动画进行和动画结束**。默认情况之下，只有在动画进行状态，才会应用@keyframes所声明的动画；而在动画等待和动画结束状态，对元素样式并不会产生任何的影响。

### 解释animation-fill-mode的每个值

取值为none时，使用得动画不会对动画等待和动画完成的元素样式产生改变。




### animation-play-state 属性
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


### 小例子
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


## 补充


### animation-timing-function
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


#### steps第一个参数的错误的理解
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


![enter description here][1] 

 

总结：
steps函数，它可以传入两个参数，第一个是一个大于0的整数，他是将间隔动画等分成指定数目的小间隔动画，然后根据第二个参数来决定显示效果。

第二个参数设置后其实和step-start，step-end同义，在分成的小间隔动画中判断显示效果。可以看出：steps(1, start) 等于step-start，steps(1,end)等于step-end

最核心的一点就是：timing-function **作用于每两个关键帧之间，而不是整个动画** 

 


### animation-delay负值

animation-delay:-2s

**允许负值，-2s 使动画马上开始，但跳过 2 秒进入动画。**



[https://www.w3school.com.cn/cssref/pr_animation-delay.asp](https://www.w3school.com.cn/cssref/pr_animation-delay.asp)

 

**被设置了 animation-dealy 为负值的动画会立刻执行，开始的位置是其动画阶段中的一个阶段**

 

[https://juejin.im/post/5d4b8707f265da03a65302bd?utm_source=gold_browser_extension](https://juejin.im/post/5d4b8707f265da03a65302bd?utm_source=gold_browser_extension) 

 


[1]: https://www.w3.org/TR/2012/WD-css3-transitions-20120403/step.png
