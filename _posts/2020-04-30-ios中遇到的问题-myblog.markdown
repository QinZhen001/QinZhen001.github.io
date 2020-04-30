---
layout:     post
title:      "ios中遇到的问题"
date:       2020-04-30 11:36:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>








# 正文







### ios弹出键盘再收起点击事件无效



[ https://juejin.im/post/5c07442f51882528c4469769 ]( https://juejin.im/post/5c07442f51882528c4469769 )



我们都知道在H5端是没法监控键盘的弹出与收起的，resize事件触发的机型极其有限，何况我在ios中实测没有触发，安卓反而可以。因为安卓弹起键盘时会修改视窗的大小，但是ios并不会，如果你在ios上设置一个100%高度的body，弹起键盘后你会发现这个body是可以上下滚动的，即100%高度的body超出了视窗。





 ios上一直有个很🐂的优化，弹出键盘时会自动把当前输入框滚动到可视区域，在安卓中会出现键盘遮挡输入框的问题，需要手动调整 



----





ios弹出键盘再收起点击事件无效,Why？



推测是body没有正确重新渲染，导致点击事件不处于body内而无法触发。



那么怎么解决呢，是不是只要把body‘推’会来就行了？ 方向有了，现在是如何‘推’的问题。

上文有说过，ios下弹出/收起键盘是没有触发resize事件的，那么在什么节点触发‘推’的操作就成了问题。





----



 最终解决方案 



```js
    onBlur = (e) => {
        const { onBlur } = this.props;
        document.body && (document.body.scrollTop = document.body.scrollTop);
        onBlur && onBlur(e);
    }
```





 在input输入框失去焦点的钩子中设置滚动到原有位置`(document.body.scrollTop = document.body.scrollTop)`，触发浏览器的重绘，使的错误的渲染回复正常，滚动位置也不会有改变，没有影响体验。 





更为通用的方法



```js
/**
 * 处理iOS 微信客户端6.7.4 键盘收起页面未下移bug
 */
;(/iphone|ipod|ipad/i.test(navigator.appVersion)) && document.addEventListener('blur', (e) => {
    // 这里加了个类型判断，因为a等元素也会触发blur事件
    ['input', 'textarea'].includes(e.target.localName) && document.body.scrollIntoView(false)
}, true)

```





 要在包个定时器才能彻底解决，点击页面其他区域关闭输入法的情况还是会错位

```js
setTimeout(function(){         
    document.body.scrollIntoView(false);    
},200); 
```

















