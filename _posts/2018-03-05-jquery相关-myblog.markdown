---
layout:     post
title:      "jquery相关"
date:       2018-03-05 15:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Jquery
---

> “Yeah It's on. ”


## 正文


### hover相关

[网页链接](http://blog.csdn.net/ss1106404013/article/details/48544213)

按照平时写jquery的思路，写鼠标滑入滑出效果都习惯性的使用hover()方法，但是今天脑子发热，想试试mouseover()，mouseout()。

补充：jquery源码中有这么一段：
```javascript
hover: function( fnOver, fnOut ) {
return this.mouseenter( fnOver ).mouseleave( fnOut || fnOver );
}
```
也就是说hover！= mouseover+mouseout。但hover=mouseenter + mouseleave


mouseover()方法触发mouseover事件，这是不用多说的，mouseover事件是在鼠标进入**指定元素时或者任意子元素**的时候触发，而mouseenter事件只有在鼠标**进入被选元素时触发**；

mouseout()方法触发mouseout事件，当鼠标离开**被选元素或者任意子级元素的时候触发**，而mouseleave事件只有在鼠标**离开被选元素时触发**。


>mouseleave()和mouseenter()经常配合一起使用，
>mouseover()和mouseout()经常配合一起使用。


经过自己的测试，mousedown，mousemove，mouseup都会进行事件冒泡。
```javascript
    jQuery(document).ready(function ($) {
        $(".content").mousedown(e => {
            console.log("content mousedown")
        }).mousemove(e => {
            console.log("content mousemove")
        }).mouseup(e => {
            console.log("content mouseup")
        })

        $(".container").mousedown(e => {
            console.log("container mousedown")
        }).mousemove(e => {
            console.log("container mousemove")
        }).mouseup(e => {
            console.log("container mouseup")
        })
    });
```

鼠标在子节点触发事件，父节点也会触发事件。





### each


[网页链接](http://www.imooc.com/code/10181)

jQuery中有个很重要的核心方法each，大部分jQuery方法在内部都会调用each，其主要的原因的就是jQuery的实例是一个元素合集

如下：找到所有的div，并且都设置样式，css只是一个方法，所以内部会调用each处理这个div的合集，给每个div都设置style属性

>$('div').css(...)

jQuery的大部分方法都是针元素合集的操作，所以jQuery会提供$(selector).each()来遍历jQuery对象
.each只是处理jQuery对象的方法，jQuery还提供了一个通用的jQuery.each方法，用来处理对象和数组的遍历

#### 语法
```javascript
jQuery.each(array, callback )
jQuery.each( object, callback )
```
第一个参数传递的就是一个对象或者数组，第二个是回调函数
```javascript
$.each(["Aaron", "慕课网"], function(index, value) {
   //index是索引,也就是数组的索引
   //value就是数组中的值了
});
```

each就是for循环方法的一个包装，内部就是通过for遍历数组与对象，通过回调函数返回内部迭代的一些参数，第一个参数是当前迭代成员在对象或数组中的索引值(从0开始计数)，第二个参数是当前迭代成员(与this的引用相同
jQuery.each()函数还会根据每次调用函数callback的返回值来决定后续动作。如果返回值为false，则停止循环(相当于普通循环中的break)；如果返回其他任何值，均表示继续执行下一个循环。

```javascript
$.each(["Aaron", "慕课网"], function(index, value) {
    return false; //停止迭代
});
```

#### 例子
```javascript
  $("#exec").click(function() {
        var v = $("#animation").val();
        var $aaron = $("#aaron");
        $aaron.empty();
        if (v == "1") {

            // 遍历数组元素
            $.each(['Aaron', '慕课网'], function(i, item) {
                $aaron.append("索引=" + i + "; 元素=" + item);
            });
        } else if (v == "2") {
            // 遍历对象属性
            $.each({
                name: "张三",
                age: 18
            }, function(property, value) {
                $aaron.append("属性名=" + property + "; 属性值=" + value);
            });
        } 
    });
```



### scrollTop()返回顶部特效

[http://blog.csdn.net/dexing07/article/details/52463241](http://blog.csdn.net/dexing07/article/details/52463241)





scrollTop()
获取第一段相对滚动条顶部的偏移 
你可以在方法参数上设置你的滚动条相对顶部的位置


----------


可以设置$(window).scrollTop（0）;返回最顶部

如果你想设置缓慢的上升的话，可以使用jq的动画效果

`$('body,html').animate({scrollTop:0},500)`

可以设置body跟html作为对象，返回到最顶部





### toggle与slideToggle以及fadeToggle的比较




操作元素的显示和隐藏可以有几种方法。

* 改变样式display为none
* 设置位置高度为0
* 设置透明度为0

都能达到这个目的，并且针对这样的处理jQuery都提供了各自的方法。show/hide、sildeDown/sildeUp、fadeIn/fadeOut。除此之外，还引入了toggle、sildeToggle以及fadeToggle切换方法

### toggle、sildeToggle以及fadeToggle的区别：

* toggle：切换显示与隐藏效果
* sildeToggle：切换上下拉卷滚效果
* fadeToggle：切换淡入淡出效果

### toggle与slideToggle细节区别：

* toggle：动态效果为从右至左。横向动作，toggle通过display来判断切换所有匹配元素的可见性
* slideToggle：动态效果从下至上。竖向动作，slideToggle 通过高度变化来切换所有匹配元素的可见性

### fadeToggle方法

* fadeToggle() 方法在 fadeIn() 和 fadeOut() 方法之间切换。
* 元素是淡出显示的，fadeToggle() 会使用淡入效果显示它们。
* 元素是淡入显示的，fadeToggle() 会使用淡出效果显示它们。

**注释：隐藏的元素不会被完全显示（不再影响页面的布局）**





```html
    <h2>toggle与slideToggle以及fadeToggle的比较</h2>
    <p>测试文字淡入效果</p>
    <p>慕课网,专注分享</p>
    动画切换：
    <select id="animation">
        <option value="1">toggle</option>
        <option value="2">slideToggle</option>
        <option value="3">fadeToggle</option>
    </select>
    <input id="btnShow" type="button" value="点击切换" />
```

```javascript
    <script type="text/javascript">

    $("#btnShow").click(function() {
        var v = $("#animation").val();
        if (v == "1") {
            $("p").toggle();
        } else if (v == "2") {
            $("p").slideToggle("slow");
        } else if (v == "3") {
            $("p").fadeToggle(1000, "linear");
        }
    });
    </script>
```








### detach()方法
[网页链接](http://www.w3school.com.cn/jquery/manipulation_detach.asp)

```javascript
$("button").click(function(){
  $("p").detach();
});
```



detach() 方法移除被选元素，包括所有文本和子节点。

这个方法会保留 jQuery 对象中的匹配的元素，因而可以在将来再使用这些匹配的元素。

**detach() 会保留所有绑定的事件、附加的数据，这一点与 remove() 不同。**


```html
<html>
<head>
<script type="text/javascript" src="/jquery/jquery.js"></script>
<script type="text/javascript">
$(document).ready(function(){
  var x;
  $("#btn1").click(function(){
    x=$("p").detach();
  });
  $("#btn2").click(function(){
    $("body").prepend(x);
  });
});
</script>
</head>
<body>
<p>这是一个段落。</p>
<button id="btn1">删除 p 元素</button>
<button id="btn2">恢复 p 元素</button>
</body>
</html>
```




### focus()和focusin()
[网页链接](http://www.w3school.com.cn/jquery/event_focus.asp)

#### focus()方法

```
$("input").focus(function(){
  $("input").css("background-color","#FFFFCC");
});
```

当元素获得焦点时，发生 focus 事件。**(只对当前元素有效)**

当通过鼠标点击选中元素或通过 tab 键定位到元素时，该元素就会获得焦点。

focus() 方法触发 focus 事件，或规定当发生 focus 事件时运行的函数。


#### focusin() 方法

当元素（或在其内的任意元素）获得焦点时发生 focusin 事件。

当在元素或在其内的任意元素上发生 focus 事件时，focusin() 方法添加要运行的函数。

**与 focus() 方法不同的是，focusin() 方法在任意子元素获得焦点时也会触发。**


>同理适用于blur()和focusout()





### css()与addClass()

* addClass()的本质是通过定义个class类的样式规则，给元素添加一个或多个类。
* css方法是通过JavaScript大量代码进行改变元素的样式



通过.addClass()我们可以批量的给相同的元素设置统一规则，变动起来比较方便，可以统一修改删除。如果通过.css()方法就需要指定每一个元素是一一的修改，日后维护也要一一的修改，比较麻烦



通过.css()方式可以很容易动态的去改变一个样式的属性，不需要在去繁琐的定义个class类的规则。一般来说在不确定开始布局规则，通过动态生成的HTML代码结构中，都是通过.css()方法处理的



* s()方法是通过增加class名的方式，那么这个样式是在外部文件或者内部样式中先定义好的，等到需要的时候在附加到元素上
* 通过.css()方法处理的是内联样式，直接通过元素的style属性附加到元素上的

**通过.css方法设置的样式属性优先级要高于.addClass方法**



### animate

[网页链接](http://www.imooc.com/code/10177)

有些复杂的动画通过之前学到的几个动画函数是不能够实现，这时候就需要强大的animate方法了

操作一个元素执行3秒的淡入动画，对比一下2组动画设置的区别

```javascript
$(elem).fadeOut(3000)  
$(elem).animate({   
    opacity:0
},3000)
```

显而易见，animate方法更加灵活了，可以精确的控制样式属性从而执行动画

#### 语法：

.animate( properties ,[ duration ], [ easing ], [ complete ] )
.animate( properties, options )

.animate()方法允许我们在任意的数值的CSS属性上创建动画。2种语法使用，几乎差不多了，唯一必要的属性就是一组CSS属性键值对。这组属性和用于设置.css()方法的属性键值对类似，除了属性范围做了更多限制。第二个参数开始可以单独传递多个实参也可以合并成一个对象传递了

.animate()方法允许我们在任意的数值的CSS属性上创建动画。2种语法使用，几乎差不多了，唯一必要的属性就是一组CSS属性键值对。这组属性和用于设置.css()方法的属性键值对类似，除了属性范围做了更多限制。第二个参数开始可以单独传递多个实参也可以合并成一个对象传递了

#### 参数分解：

properties：一个或多个css属性的键值对所构成的Object对象。要特别注意所有用于动画的属性必须是数字的，除非另有说明；这些属性如果不是数字的将不能使用基本的jQuery功能。比如常见的，border、margin、padding、width、height、font、left、top、right、bottom、wordSpacing等等这些都是能产生动画效果的。background-color很明显不可以，因为参数是red或者GBG这样的值，非常用插件，否则正常情况下是不能只用动画效果的。注意，CSS 样式使用 DOM 名称（比如 "fontSize"）来设置，而非 CSS 名称（比如 "font-size"）。

特别注意单位，属性值的单位像素（px）,除非另有说明。单位em 和 %需要指定使用

```javascript
.animate({
    left: 50, 
    width: '50px'   
    opacity: 'show',  
    fontSize: "10em",
}, 500);
```

除了定义数值，每个属性能使用'show', 'hide', 和 'toggle'。这些快捷方式允许定制隐藏和显示动画用来控制元素的显示或隐藏

```javascript
.animate({
    width: "toggle"
});
```

如果提供一个以+= 或 -=开始的值，那么目标值就是以这个属性的当前值加上或者减去给定的数字来计算的

```javascript
.animate({ 
    left: '+=50px'
}, "slow");
```



duration时间

动画执行的时间，持续时间是以毫秒为单位的；值越大表示动画执行的越慢，不是越快。还可以提供'fast' 和 'slow'字符串，分别表示持续时间为200 和 600毫秒。



easing动画运动的算法

jQuery库中默认调用 swing。如果需要其他的动画算法，请查找相关的插件



complete回调

动画完成时执行的函数，这个可以保证当前动画确定完成后发会触发

#### 例子

```html
    <h2>animate(上)</h2>
    <p>慕课网,专注分享</p>
    <div id="aaron">内部动画</div>
    点击观察动画效果：
    <select id="animation">
        <option value="1">动画1</option>
        <option value="2">动画2</option>
        <option value="3">动画3</option>
        <option value="4">动画4</option>
    </select>
    <input id="exec" type="button" value="执行动画">
```

```javascript
  <script type="text/javascript">
    $("#exec").click(function() {
        var v = $("#animation").val();
        var $aaron = $("#aaron");
        if (v == "1") {
            // 数值的单位默认是px
            $aaron.animate({
                width  :300,
                height :300
            });
        } else if (v == "2") {
            // 在现有高度的基础上增加100px
            $aaron.animate({
                 width  : "+=100px",
                 height : "+=100px"
            });
        } else if (v == "3") {
            $aaron.animate({
                fontSize: "5em"
            }, 2000, function() {
                alert("动画 fontSize执行完毕!");
            });
        } else if (v == "4") {
            //通过toggle参数切换高度
            $aaron.animate({
                width: "toggle"
            });
        } 
    });
    </script>
```

.animate( properties, options )

animate在执行动画中，如果需要观察动画的一些执行情况，或者在动画进行中的某一时刻进行一些其他处理，我们可以通过animate提供的第二种设置语法，传递一个对象参数，可以拿到动画执行状态一些通知

options参数

* duration - 设置动画执行的时间
* easing - 规定要使用的 easing 函数，过渡使用哪种缓动函数
* step：规定每个动画的每一步完成之后要执行的函数
* progress：每一次动画调用的时候会执行这个回调，就是一个进度的概念
* complete：动画完成回调

**如果多个元素执行动画，回调将在每个匹配的元素上执行一次，不是作为整个动画执行一次**

```javascript
$('#elem').animate({
    width: 'toggle',  
    height: 'toggle'
  }, {
    duration: 5000,
    specialEasing: {
      width: 'linear',
      height: 'easeOutBounce'
    },
    complete: function() {
      $(this).after('<div>Animation complete.</div>');
    }
  });
```



```javascript
 <script type="text/javascript">
    $("#exec").click(function() {
        var v = $("#animation").val();
        var $aaron = $("#aaron");
        if (v == "1") {
            //观察每一次动画的改变
            $aaron.animate({
                height: '50'
            }, {
                duration :2000,
                //每一个动画都会调用
                step: function(now, fx) {
                   $aaron.text('高度的改变值:'+now)
                }
            })
        } else if (v == "2") {
            //观察每一次进度的变化
            $aaron.animate({
                height: '50'
            }, {
                duration :2000,
                //每一步动画完成后调用的一个函数，
                //无论动画属性有多少，每个动画元素都执行单独的函数
                progress: function(now, fx) {
                   $aaron.text('进度:'+arguments[1])
        
                }
            })
        } 
    });
    </script>
```

停止动画stop

语法：

.stop( [clearQueue ], [ jumpToEnd ] )
.stop( [queue ], [ clearQueue ] ,[ jumpToEnd ] )


stop还有几个可选的参数，简单来说可以这3种情况

* .stop(); 停止当前动画，点击在暂停处继续开始。 **停止当前动画**
* .stop(true); 如果同一元素调用多个动画方法，尚未被执行的动画被放置在元素的效果队列中。这些动画不会开始，直到第一个完成。当调用.stop()的时候，队列中的下一个动画立即开始。如果clearQueue参数提供true值,那么在队列中的动画其余被删除并永远不会运行。 **真正的停止,当前动画停止,队列里后续动画也不执行**
* .stop(true,true); 当前动画将停止，但该元素上的 CSS 属性会被立刻修改成动画的目标值。
  **当前动画动作停止，并且直接展示所有动画完成后的最终状态**






## 补充



### $('#a.b')是低效做法

[网页链接](https://segmentfault.com/q/1010000002939093/a-1020000006707782)


jQuery选择器优化问题，使用$('#a>.b')，IDE会提示这是低效的用法

It suggests to split descendant selectors which are prefaced with ID selector and warns about duplicated selectors which could be cached.

```jsx
$("#property [data-role='content'] .container");

Changing it to this makes PhpStorm happy and can evidently be more than twice as fast:

$("#property").find("[data-role='content'] .container");
```











