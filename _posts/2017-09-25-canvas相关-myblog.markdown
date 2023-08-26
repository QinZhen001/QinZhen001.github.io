---
layout:     post
title:      "canvas相关"
date:       2017-09-25 12:56:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Canvas
---

> “Yeah It's on. ”


## 基础
[网页链接](http://www.w3school.com.cn/tags/html_ref_canvas.asp)


[https://www.canvasapi.cn/CanvasRenderingContext2D/](https://www.canvasapi.cn/CanvasRenderingContext2D/)


HTML5 `<canvas>` 标签用于绘制图像（通过脚本，通常是 JavaScript）。
不过，`<canvas>` 元素本身并没有绘制能力（它仅仅是图形的容器）-您必须使用脚本来完成实际的绘图任务。
getContext() 方法可返回一个对象，该对象提供了用于在画布上绘图的方法和属。


**getContext("2d") 对象属性和方法，可用于在画布上绘制文本、线条、矩形、圆形等等。**

### 属性
* fillStyle	设置或返回用于填充绘画的颜色、渐变或模式
* strokeStyle	设置或返回用于笔触的颜色、渐变或模式
* shadowColor	设置或返回用于阴影的颜色
* shadowBlur	设置或返回用于阴影的模糊级别
* shadowOffsetX	设置或返回阴影距形状的水平距离
* shadowOffsetY	设置或返回阴影距形状的垂直距离


### 方法
* createLinearGradient()	创建线性渐变（用在画布内容上）
* createPattern()	在指定的方向上重复指定的元素
* createRadialGradient()	创建放射状/环形的渐变（用在画布内容上）
* addColorStop()	规定渐变对象中的颜色和停止位置

### 线条样式
* lineCap	设置或返回线条的结束端点样式
* lineJoin	设置或返回两条线相交时，所创建的拐角类型
* lineWidth	设置或返回当前的线条宽度
* miterLimit	设置或返回最大斜接长度

### 矩形
* rect()	创建矩形
* fillRect()	绘制“被填充”的矩形
* strokeRect()	绘制矩形（无填充）
* clearRect()	在给定的矩形内清除指定的像素

### 路径
* fill()	填充当前绘图（路径）
* stroke()	绘制已定义的路径
* beginPath()	起始一条路径，或重置当前路径
* moveTo()	把路径移动到画布中的指定点，不创建线条
* closePath()	创建从当前点回到起始点的路径
* lineTo()	添加一个新点，然后在画布中创建从该点到最后指定点的线条
* clip()	从原始画布剪切任意形状和尺寸的区域
* quadraticCurveTo()	创建二次贝塞尔曲线
* bezierCurveTo()	创建三次方贝塞尔曲线
* arc()	创建弧/曲线（用于创建圆形或部分圆）
* arcTo()	创建两切线之间的弧/曲线
* isPointInPath()	如果指定的点位于当前路径中，则返回 true，否则返回 false

### 转换
* scale()	缩放当前绘图至更大或更小
* rotate()	旋转当前绘图
* translate()	重新映射画布上的 (0,0) 位置
* transform()	替换绘图的当前转换矩阵
* setTransform()	将当前转换重置为单位矩阵。然后运行 transform()

### 其他
* save()	保存当前环境的状态
* restore()	返回之前保存过的路径状态和属性
* createEvent()	 
* getContext()	 
* toDataURL()	 


### arc()
arc() 方法创建弧/曲线（用于创建圆或部分圆）。

context.arc(x,y,r,sAngle,eAngle,counterclockwise);


* x	圆的中心的 x 坐标。
* y	圆的中心的 y 坐标。
* r	圆的半径。
* sAngle	起始角，以弧度计。（弧的圆形的三点钟位置是 0 度）。
* eAngle	结束角，以弧度计。
* counterclockwise	可选。规定应该逆时针还是顺时针绘图。False = 顺时针，true = 逆时针。


![enter description here][1]

>如需通过 arc() 来创建圆，请把起始角设置为 0，结束角设置为 2*Math.PI。



### createRadialGradient
[网页链接](http://www.360doc.com/content/15/1014/15/28180908_505594108.shtml)

createRadialGradient是canvas创建发射渐变的一个方法


createRadialGradient(xStart, yStart, radiusStart, xEnd, yEnd, radiusEnd)


| 参数           | 描述               |
| -------------- | ------------------ |
| xStart, yStart | 开始圆的圆心的坐标 |
| radiusStart    | 开始圆的半径       |
| xEnd, yEnd     | 结束圆的圆心的坐标 |
| radiusEnd      | 结束圆的半径    |

```
canvas = document.getElementById('canvas');
ctx = canvas.getContext('2d');
var cycle1 = {
    x: 150,
    y: 150,
    r: 25
}
var cycle2 = {
    x: 250,
    y: 250,
    r: 50
}
ctx.beginPath();
ctx.arc(cycle1.x, cycle1.y, cycle1.r, 0, Math.PI*2, false);
ctx.stroke();
ctx.beginPath();
ctx.arc(cycle2.x, cycle2.y, cycle2.r, 0, Math.PI*2, false);
ctx.stroke();
 
var gr = ctx.createRadialGradient(cycle1.x, cycle1.y, cycle1.r, cycle2.x, cycle2.y, cycle2.r);
gr.addColorStop(0, 'rgba(255, 0, 0, 0.75)');
gr.addColorStop(0.5, 'rgba(0, 255, 0, 0.75)');
gr.addColorStop(1, 'rgba(0, 0, 255, 0.75)');
 
ctx.fillStyle = gr;
ctx.fillRect(0, 0, 500, 500);
```


**其中两个圆相离的情况得到的渐变图案很神奇**。createRadialGradient实现的线性渐变的原理是：**发生渐变的区域是，cycle1上的点到cycle2上的点所连成区域**，所以当两个圆相离的时候，会形成放射状的扇形。可能还是有点难理解，把两个圆放到三维的场景下，就容易看懂了。

### save()和restore()

[网页链接](http://blog.csdn.net/oney139/article/details/8143281)


canvas.save();和canvas.restore();是两个相互匹配出现的，作用是用来保存画布的状态和取出保存的状态的。这里稍微解释一下，
当我们对画布进行旋转，缩放，平移等操作的时候其实我们是想对特定的元素进行操作，比如图片，一个矩形等，但是当你用canvas的方法来进行这些操作的时候，其实是对整个画布进行了操作，那么之后在画布上的元素都会受到影响，所以我们在操作之前调用canvas.save()来保存画布当前的状态，当操作之后取出之前保存过的状态，这样就不会对其他的元素进行影响

####  代码段1
```
    public void draw() {   
      Canvas canvas = sfh.lockCanvas();    
      canvas.drawColor(Color.BLACK);  
      canvas.drawBitmap(bmp1, 0,0,paint);  
      canvas.save();   
      canvas.scale(5f, 5f);  
      canvas.restore();   
      canvas.drawBitmap(bmp2, 0,0,paint);  
      sfh.unlockCanvasAndPost(canvas);    
    }  
```

#### 代码段2
```
    public void draw() {   
      Canvas canvas = sfh.lockCanvas();    
      canvas.drawColor(Color.BLACK);  
      canvas.drawBitmap(bmp1, 0,0,paint);  
      canvas.scale(5f, 5f);  
      canvas.drawBitmap(bmp2, 0,0,paint);  
      sfh.unlockCanvasAndPost(canvas);    
    }  
```

上面这两个代码片段中我们都假设有两张图片bmp1和bmp2，并且都画在画布上!

那么代码段1和代码段2的不同:

代码段1中我们进行画布缩放的之前保存了画布状态，做了缩放操作之后又取出之前保存的状态，这样做是为了保证bmp2正常画出来不受到缩放的影响！

代码段2里，画了bmp1后就执行了缩放操作，并且没有保存状态！紧接着画了bmp2，那么bmp2也会一样受到缩放的影响！！





### **fill and stroke**

The stroke and fill determines how the shape is drawn. 

**The stroke is the outline of a shape.** 

**The fill is the contents inside the shape.**



## html2canvas

[http://html2canvas.hertzen.com/](http://html2canvas.hertzen.com/)



```js
  html2canvas(infoCard.$el, {
              allowTaint: true, // 允许污染
              useCORS: true, // 使用跨域
              width: width,
              height: height,
              scrollY: 0,
              scrollX: 0,
       }).then(canvas => {
              const dataUrl = canvas.toDataURL()
              console.log('dataUrl', dataUrl)
              document.body.append(canvas)
       })
```





### html2canvas截图不全的问题



### 资源未加载

**在点击保存图片时，此时要保存的资源较多，造成模块并没有完全加载完毕，就已经生成了截图**



解决方案：(加上一个延时操作)



```js
      setTimeout(() => {
        html2canvas(img, { canvas: canvas }).then(function(canvas) {
          _this.photoUrl = canvas.toDataURL();
        });
      }, 500);
```



### 滚动造成



**滚轮滑动造成的，主要是html2canvas是根据body进行截图，若内容高度高于body时，就会出现这样的问题(大概意思就是有滚动条时造成的)**



解决方案：(在生成截图前，先把滚动条置顶)



```js
window.pageYOffset = 0;
document.documentElement.scrollTop = 0
document.body.scrollTop = 0

window.pageXOffset = 0
document.documentElement.scrollLeft = 0
document.body.scrollLeft = 0
```





### 滚动条造成

[http://html2canvas.hertzen.com/configuration](http://html2canvas.hertzen.com/configuration)



html2canvas 默认参数

| windowWidth  | `Window.innerWidth`  | Window width to use when rendering `Element`, which may affect things like Media queries (渲染元素时使用的窗口宽度，这可能会影响媒体查询等内容) |
| ------------ | -------------------- | ------------------------------------------------------------ |
| windowHeight | `Window.innerHeight` | Window height to use when rendering `Element`, which may affect things like Media queries (渲染元素时使用的窗口高度，这可能会影响媒体查询等内容) |



但是随着我们缩放窗口页面会出现滚动条，导致html2canvas截图不全。



解决方案：



```js
   html2canvas(infoCard.$el, {
          width: width,
          height: height,
          scrollX: 0,
          scrollY: 0,
          windowWidth: document.body.scrollWidth,
          windowHeight: document.body.scrollHeight,
        })
```





### 部分白屏



**html2canvas绘制完图片后会有部分白屏**



因为在不设置scrollY的情况下，canvas绘制页面时会根据全局页面的滚动情况自动向下偏移。当然了，scrollX也是一样的道理。



scrollY: 0, 其他的参数根据自己情况配置，这个参数一定不能少

```js
html2canvas(htmlDom, {
    scrollY: 0, 
    scrollX: 0,
}).
```



### 绘制图片偏移

**用html2canvas绘制完图片后，始终会有个偏移距离**



观察绘制的box，是不是存在类似transform:translateX(-50%)这种样式



解决方案：用户绘图的区域不用transform来定位，换一种没有偏移的方式，比如设置百分比或者固定宽高。





### 图片出现模糊

dpr的问题,配置scale



```js
html2canvas(infoCard.$el, {
   scale: 2
})
```



还可能是background-image属性的问题，html2canvas对background-image的支持不太好，会出现图片模糊



解决方法：使用img标签，设置src属性



```html
<!-- 有问题的写法 -->
<div :style="{backgroundImage:'url(' + info.career_photo + ')'}" class="avatar">
</div>
```



```scss
.avatar {
   position: relative;
   width: 160px;
   height: 160px;
   background-position: 0 0;
   // 宽度100% 高度自适应
   background-size: 100% auto;
   background-repeat: no-repeat;
   overflow: hidden;
}
```



改成正确的写法



```html
<div v-if="info.career_photo" class="avatar">
   <img class="avatar-img" :src="info.career_photo" alt>
</div>
```



```scss
     .avatar {
        position: relative;
        width: 160px;
        height: 160px;
        overflow: hidden;
        .avatar-img {
          position: absolute;
          top: 0;
          left: 50%;
          transform: translateX(-50%);
          width: 160px;
          height: 240px;
        }
      }
```










## 补充

### canvas.width和canvas.style.width区别

[https://segmentfault.com/a/1190000016819776?utm_source=tag-newest](https://segmentfault.com/a/1190000016819776?utm_source=tag-newest)



canvas本身是一个画布，我们怎么理解画布，决定了我们是否能正确的理解canvas.width和canvas.style.width的区别。**canvas.width就是画布真实的大小**，这个画布不是我们能看到的画布，我们能看见的画布，已经是在浏览器处理canvas.style.width/canvas.style.height之后，加工处理后的画布。



而cavnas.style.width/canvas.style.height决定了画布以怎样的形式进行缩放展示给页面



- canvas.width / canvas.height 表示画布真实大小，其实我们并不可见
- canvas.style.width / canvas.style.height 表示画布输出到浏览器我们可见的/最终的大小
- 不提供canvas真实大小时，默认按`300*150`处理，如果canvas.style也没提供，那么style.width为空，注意并不是`300*150`



### 图片纯前端JS压缩的实现

[https://juejin.im/post/5bec3c6cf265da614312a0fa](https://juejin.im/post/5bec3c6cf265da614312a0fa)



### **toDataURL**

此方法可以返回Canvas图像对应的data URI，也就是平常我们所说的base64地址。格式如下：



[https://www.canvasapi.cn/HTMLCanvasElement/toDataURL](https://www.canvasapi.cn/HTMLCanvasElement/toDataURL)


**这个方法非常重要，当我们需要在canvas上画网络图片时就用到这个，无需把图片下载下来**



例子

```javascript
  let url = "http://oalbumres.heywoodsminiprogram.com/uploads/photo/dev/1568107740423.png"
      var img = new Image()
      img.src = url;
      var canvas = document.getElementById("canvas");
      var context = canvas.getContext("2d");
      context.drawImage(img, 0, 0, 800, 800);
      let rrr = canvas.toDataURL('image/png');
      console.log("111", rrr)
```


>如果不调用canvas.toDataURL，这张网络图片就无法绘制


canvas.toDataURL(mimeType, quality);





* mimeType（可选）String
mimeType表示需要转换的图像的mimeType类型。默认值是image/png，还可以是image/jpeg，甚至image/webp（前提浏览器支持）等。
* quality（可选）Number
quality表示转换的图片质量。范围是0到1。此参数要想有效，图片的mimeType需要是image/jpeg或者image/webp，其他mimeType值无效。默认压缩质量是0.92。
根据自己的肉眼分辨，如果使用toDataURL()的quality参数对图片进行压缩，同样的压缩百分比呈现效果要比Adobe Photoshop差一些。


返回值
返回base64 data图片数据。


#### 跨域的问题

如果Canvas中绘制的图片资源跨域，则浏览器会报跨域相关的错误，而导致toDataURL()方法执行失败。解决方法是：首先，图片服务器需要配置Access-Control-Allow-Origin，然后借助HTML crossOrigin属性设置图片匿名。


```javascript
var img = new Image();
img.crossOrigin = '';
img.onload = function () {};
img.src = 'https://www.otherdomain.com/example.jpg';

```


#### 同步与异步


toBlob()方法是异步的，而toDataURL()方法是同步的，这就有个问题，如果需要转换的Canvas尺寸很大，则会阻塞脚本的运行，因此主要注意控制Canvas的尺寸。



### toBlob


toBlob()方法可以Canvas图像对应的Blob对象（binary large object）。此方法可以把Canvas图像缓存在磁盘上，或者存储在内存中，这个往往由浏览器决定。


void canvas.toBlob(callback, mimeType, quality);



这里的void表示无返回值。



* callback Function
toBlob()方法执行成功后的回调方法，支持一个参数，表示当前转换的Blob对象。
* mimeType（可选）String
mimeType表示需要转换的图像的mimeType类型。默认值是image/png，还可以是image/jpeg，甚至image/webp（前提浏览器支持）等。
* quality（可选）Number
quality表示转换的图片质量。范围是0到1。由于Canvas的toBlob()方法转PNG是无损的，因此，此参数默认是没有效的，除非，指定图片mimeType是image/jpeg或者image/webp，此时默认压缩值是0.92。

```javascript
var canvas = document.querySelector('canvas');
// canvas转为blob并上传
canvas.toBlob(function (blob) {
    var data = new FormData();
    // 装载图片数据
    data.append('image', blob);
    // 图片ajax上传，字段名是image
    var xhr = new XMLHttpRequest();
    // 文件上传成功
    xhr.onload = function() {
        // xhr.responseText就是返回的数据
    };
    // 开始上传
    xhr.open('POST', 'upload.php', true);
    xhr.send(data);    
});

```


#### IMG图像数据为Blob


如果我们希望把`<canvas>`元素图像使用`<img>`元素显示，toBlob()和toDataURL()方法都是可以的，但个人推荐使用toBlob()方法（如果不用顾及兼容性）。

blob数据对象是无法直接作为`<img>`的src属性值呈现的，需要URL.createObjectURL()方法处理下。





### setLineDash

[网页链接](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/setLineDash)

CanvasRenderingContext2D.setLineDash() 是 Canvas 2D API 设置虚线样式的方法。



参数：

* 一个Array数组。一组描述交替绘制线段和间距（坐标空间单位）长度的数字。 如果数组元素的数量是奇数， 数组的元素会被复制并重复。例如， [5, 15, 25] 会变成 [5, 15, 25, 5, 15, 25]。



```js
var canvas = document.getElementById("canvas");
var ctx = canvas.getContext("2d");

ctx.setLineDash([5, 15]);

ctx.beginPath();
ctx.moveTo(0,100);
ctx.lineTo(400, 100);
ctx.stroke();
```





### 实现图片镜像

[https://www.zhangxinxu.com/wordpress/2018/03/canvas-image-element-mirror-animation/](https://www.zhangxinxu.com/wordpress/2018/03/canvas-image-element-mirror-animation/)

Canvas的坐标变换系和CSS不一样，因此，如果我们想实现居中翻转效果，需要在翻转之前将目标元素的中心点移动到变换轴上。

举个例子：

```tsx
const canvas = document.querySelector("#my-canvas");
const context = canvas.getContext("2d");
const img = document.createElement("img");
img.src = "http://www.htmq.com/canvas/images/drawImage002.png";
img.onload = (e) => {
      // 缩放比例 （镜面翻转）
      const value = -1;
      // 清除画布内容
      context.clearRect(0, 0, canvas.width, canvas.height);
      // 调整坐标
      context.translate((canvas.width - canvas.width * value) / 2, 0);
      // 调整缩放
      context.scale(value, 1);
      // 绘制此时图片
      context.drawImage(img, 0, 0, canvas.width, canvas.height);
      // 坐标参考还原
      context.setTransform(1, 0, 0, 1, 0, 0);
    };
```





### Canvas 渲染的性能更高

1. 硬件加速：Canvas 使用 GPU 进行渲染，因此可以充分利用 GPU 的并行处理能力，大大提高渲染性能。
2. 低级别的绘制API：Canvas 提供了较为底层的绘制 API，开发者可以直接操作像素进行绘制，避免了复杂的 DOM 元素布局和渲染过程，减少了性能开销。
3. 高效的存储和渲染机制：Canvas 使用位图来存储绘制内容，相比于使用 DOM 的方式，可以减少内存的使用，并且绘制操作更加高效，减少了渲染的时间消耗。
4. 可定制性强：Canvas 提供了丰富的绘制 API，可以根据需要进行高度定制，使得绘制过程更加灵活和高效。
5. 不影响页面结构：Canvas 是一个独立于 DOM 的元素，不会对页面的结构和布局产生影响，因此在动画等需要频繁重绘的场景下相对更为高效。

需要注意的是，虽然 Canvas 渲染性能较高，但对于复杂的交互和动画效果来说，使用专门的动画库或者使用 CSS 动画可能更加高效。





## 问题

[https://juejin.cn/post/6844903989444608014](Canvas使用及常见问题)

[网页链接](https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D/setLineDash)

CanvasRenderingContext2D.setLineDash() 是 Canvas 2D API 设置虚线样式的方法。








### **绘制模糊问题**

canvas 绘图时，会从两个物理像素的中间位置开始绘制并向两边扩散 0.5 个物理像素。当设备像素比为 1 时，一个 1px 的线条实际上占据了两个物理像素（每个像素实际上只占一半），由于不存在 0.5 个像素，所以这两个像素本来不应该被绘制的部分也被绘制了，于是 1 物理像素的线条变成了 2 物理像素，视觉上就造成了模糊


上面说了那么多弯弯绕，可能理解起来比较晕，但是解决的方法通过上面的内容却已经诞生了：



首先分别声明 canvas 的 css 宽高和上下文宽高，同时上下文宽高应该是 css 宽高的 devicePixelRatio 倍。在上面的例子中就是：


```javascript
// devicePixelRatio = 2
<style>
canvas {
    width: 200px;
    height: 200px;
}
</style>
<canvas id="canvas" width="400" height="400"></canvas>
```

>width="400" height="400" 也可以在js中声明



```tsx
// 获取dpr
const dpr = window.devicePixelRatio; 
const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');
// 获取Canvas容器的宽高
const { width: cssWidth, height: cssHeight } = canvas.getBoundingClientRect();
// 根据dpr，设置Canvas的宽高，使1个canvas像素和1个物理像素相等
canvas.width = dpr * cssWidth;
canvas.height = dpr * cssHeight;
// 根据dpr，设置canvas元素的宽高属性
ctx.scale(dpr,dpr);
```





### getImageData 跨域问题

1. 是页面与服务端开启允许跨域；
2. 给图片设置允许跨域，`**img**.setAttribute('crossOrigin', 'anonymous');`



### 反锯齿使得drawImage图片更清晰

[https://www.twle.cn/l/yufei/canvas/canvas-basic-imagesmooth.html](https://www.twle.cn/l/yufei/canvas/canvas-basic-imagesmooth.html)



`drawImage()` 默认情况下是抗锯齿的，也就是平滑化了



**而设置这个抗锯齿的属性是 `imageSmoothingEnabled` ， 默认值为 `true`**



`ctx.imageSmoothingEnabled` 用于设置图片是否平滑，也就是是否抗锯齿

`true` 表示图片平滑（默认值），`false` 表示图片不平滑



## 性能优化



### **减少绘图指令的调用**

把stoke调用放到循环外，减少stoke的调用





### **分层渲染**

为什么需要分层渲染， 在游戏中，假设人物的不停地在移动，但是呢背景可能加了很多花里呼哨的元素，但是我在每一次更新的时候，场景本身是不变的，变的只有人物不停的移动，如果每一帧再去重绘不就造成了性能浪费， 这时候分层canvas就出现了

一个背景层一个运动层，再抽象一点，我们什么时候应该去做分层 ，如果画布纯是静态的就没有必要去做分层了， 如果当前有静态有东动态的，你可以逻辑层放在最上面，然后展示层放在最底下就可以实现所谓的分层渲染了，但是最好保持在3～5个。





### **局部渲染**

局部渲染的话其实就是调用canvas 的 clip方法。

正确的做法其实就是我们要做局部刷新：

1. 确定改变的元素的包围盒（是否存在相交）
2. 画出路径 然后 clip
3. 最后重新绘制绘制改变的图形

**「** **clip()** **」** 确定绘制的的裁剪区域，区域之外的图形不能绘制，详情查看 CanvasRenderingContext2D.clip() **「** **clearRect(x, y, width, height)** **」** 擦除指定矩形内的颜色，查看 CanvasRenderingContext2D.clearRect()



### **离屏canvas**

**「** **OffscreenCanvas提供了一个可以脱离屏幕渲染的canvas对象。它在窗口环境和web worker环境均有效。** **」**

由于浏览器是单线程，canvas的计算和渲染其实是在同一个线程的。这就会导致在动画中（有时候很耗时）的计算操作将会导致App卡顿，降低用户体验。

到目前为止，canvas的绘制功能都与 标签绑定在一起，这意味着canvas API和DOM是耦合的。而OffscreenCanvas，正如它的名字一样，通过将Canvas移出屏幕来解耦了DOM和canvas API。

但更重要的是，将两者分离后，canvas将可以在Web Worker中使用，即使在Web Worker中没有DOM。这给canvas提供了更多的可能性。

```tsx
// 离屏canvas
const offscreen = new OffscreenCanvas(200, 200);
```

```tsx
const canvas = document.getElementById('canvas');
const offscreen = canvas.transferControlToOffscreen();
```





[1]: http://www.w3school.com.cn/i/arc.gif
[2]: http://img.alicdn.com/tps/TB1RgLULpXXXXatXVXXXXXXXXXX-667-309.png