---
layout:     post
title:      "Vue开发中的坑"
date:       2017-07-29 10:56:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”


## 正文
[网页链接]()

### Error:Cannot find module 'stylus'
在webpack 里面用了 stylus-loader，但npm instatll 没有正确安装，出现error： Cannot find module ‘stylus’。

解决办法： 
重新npm install stylus 和 stylus-loader

npm install stylus –save-dev 
npm install stylus-loader –save-dev

或:
npm install stylus-loader css-loader style-loader --save-dev


<strong>非要单独再装一次才好！</strong>



### 事件及时销毁

Vue组件销毁时，会自动清理它与其它实例的连接，解绑它的全部指令及事件监听器，但是仅限于组件本身的事件。


也就是说，在js内使用addEventListener等方式是不会自动销毁的，我们需要在组件销毁时手动移除这些事件的监听，以免造成内存泄露，如：

```javascript
created() {
  addEventListener('touchmove', this.touchmove, false)
},
beforeDestroy() {
  removeEventListener('touchmove', this.touchmove, false)
}
```







### 在vue的style标签里面使用变量



[ https://segmentfault.com/q/1010000015375955 ]( https://segmentfault.com/q/1010000015375955 )







在data里面设置一个值，用来存储这个变换的样式

```
var color = newvalue;
data() {
    return {
        changeStyle:'background:'+color
    }
}
html中直接这样就可以了
<span  style="margin-top:5px;" :style="changeStyle"></span>
```

这两个style会自己合并到一起的，后面你去修改changeStyle这个变量就可以改变样式了





### wraning： multiple modules with names

[ https://www.cnblogs.com/chaoyong/p/8340681.html ]( https://www.cnblogs.com/chaoyong/p/8340681.html )



```
WARNING in ./src/views/account/teacher/admin/AddGroupDialog.vue?vue&type=script&lang=js&

There are multiple modules with names that only differ in casing.

This can lead to unexpected behavior when compiling on a filesystem with other case-semantic.

Use equal casing. Compare these module identifiers:
```



这是引用组件时，路径大小写不对导致的。比如：

如果Paginate组件是大写的，那么这样用会错： 



```js
import Paginate from '~/components/paginate.vue';
需要这样写：
import Paginate from '~/components/Paginate.vue';
```





### 改变数组下标不能响应式



```js
  data() {
    return {
      list2: [2, 3, 4, 5]
    };
  },

......

setTimeout(() => {
      // 错误的做法 
      this.list2[2] = 2222
      // 正确的做法 
      this.$set(this.list2, 2, 2222)
}, 2000)
```







###  did you register the component correctly 

[ https://blog.csdn.net/qq_34164814/article/details/88788892 ]( https://blog.csdn.net/qq_34164814/article/details/88788892 )



报错：

Unknown custom element: `<select-teacher-with-group>` - did you register the component correctly? For recursive components, make sure to provide the "name" option.





 components多写了一个 ，很坑的，Orz。。。





###  vue-router报错 NavigationDuplicated

[https://blog.csdn.net/gxdvip/article/details/101016946](https://blog.csdn.net/gxdvip/article/details/101016946)

vue router 升级到3.1.x 后，重复点击导航时（连续点击相同路由链接）控制台出现报错 {_name: "NavigationDuplicated", name: "NavigationDuplicated"}，虽然不影响功能使用，但也不能视而不见。



```js

// 捕获 router.push 异常
 
this.$router.push(route).catch(err => {
    console.log('输出报错',err)
})

```





### 给组件绑定的事件为什么无法触发？

在 Vue 2.0 中，为**自定义**组件绑定**原生**事件必须使用 `.native` 修饰符：



```html
<my-component @click.native="handleClick">Click Me</my-component>
```



从易用性的角度出发，对 `el-button` 组件进行了处理，使它可以监听 `click` 事件：

```html
<el-button @click="handleButtonClick">Click Me</el-button>
```



### 无法监听scroll事件

[https://www.jb51.net/article/171961.htm](https://www.jb51.net/article/171961.htm)

vue中监听滚动条滚动事件，清一色的使用`document.addEventListener('scroll',function(){})`



如果,我们不想在document监听事件，该怎么做？ **(特别是当你并不是整页滚动，而是页面中拥有一个fixed固定的头部时,这个时候document.addEventListener scroll是不准确的，除非减去fixed的header高度)**



```html
 <div class="body-container" @scroll="scrollEvent">
 <ul>
 <li></li>
 ……
 <li></li>
 </ul>
 </div>
```



上面的写法，我发现即使我的li标签足够多，撑满一页，页面滚动的时候并不能触发到scrollEvent，是什么原因呢？



经过仔细思考，猜想应该是滚动事件并不是在我`<div class="body-container" @scroll="scrollEvent">`这个div上触发的，因为滚动条并没有出现在我这个div上。这个div完全被li标签撑起来了，产生滚动事件的就是document了。



```html
<div style="height: 300px" @scroll="scrollEvent">
 <div style="height: 200px"></div>
 <div style="height: 200px"></div>
 <div style="height: 200px"></div>
</div>
```



当我在这个300px固定高度的div中滚动的时候，果然触发了scrollEvent，问题原因找到了，接下来就是解决了。



**只要外面的包裹层拥有固定高度，就能触发滚动事件了**



**如果还不能触发scroll事件，包裹层加上 overflow: scroll;那就无敌问稳了**



至此，功能完美的实现了，而且没有使用`document.addEventListener`破坏vue的完整性



## 补充



### 每个页面的包裹布局



```css

.container {
  display: flex;
  box-sizing: border-box;
  min-width: 820px;
  margin: 20px;
  background: #FFF;
  border: 1px solid #EDEDED;
  border-radius: 6px;
  overflow: auto;
  * {
    box-sizing: inherit;
  }
}
```









## vue-cli3



### CSS minification error

[https://blog.csdn.net/zzaixiao/article/details/107485269](https://blog.csdn.net/zzaixiao/article/details/107485269)

在开发时没有报错

开打包时出现：

```
Error: CSS minification error: Lexical error on line 1: Unrecognized text.
```



定位到错误代码：

```
calc(50% - 270PX)
```





