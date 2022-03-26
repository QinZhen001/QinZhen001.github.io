---
layout:     post
title:      "fastClick原理分析"
date:       2020-10-21 16:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”



 [click 浏览器兼容性](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/click_event#浏览器兼容性)



# 正文



移动端，当用户点击屏幕时，会依次触发 `touchstart`，`touchmove`(0 次或多次)，`touchend`，`mousemove`，`mousedown`，`mouseup`，`click`。 `touchmove` 。只有当手指在屏幕发生移动的时候才会触发 `touchmove` 事件。在 `touchstart` ，`touchmove` 或者 `touchend` 事件中的任意一个调用 `event.preventDefault`，`mouse` 事件 以及 `click` 事件将不会触发



**fastClick 在 `touchend` 阶段 调用 `event.preventDefault`，然后通过 `document.createEvent` 创建一个 `MouseEvents`，然后 通过 `eventTarget.dispatchEvent` 触发对应目标元素上绑定的 `click` 事件**



### notNeeded

首先，我们需要明确一个问题，300ms 的延迟只有在移动端才会出现，PC 端是没有的。fastClick 中又有个一 `notNeeded` 的函数是用来判断有没有必要使用 fastClick



```js
// Devices that don't support touch don't need FastClick
if (typeof window.ontouchstart === 'undefined') {
  return true;
}
```





### onTouchEnd

我们去看FastClick源码中的onTouchEnd实现：



```js
FastClick.prototype.onTouchEnd = function(event) {
    ......
    
    	// 阻止实际的单击通过
		// Prevent the actual click from going though - unless the target node is marked as requiring
		// real clicks or if it is in the allowlist in which case only non-programmatic clicks are permitted.
   	if (!this.needsClick(targetElement)) {
			event.preventDefault();
			this.sendClick(targetElement, event);
	} 
}

```

继续看sendClick源码

创建一个MouseEvents，在targetElement派发

```js
FastClick.prototype.sendClick = function(targetElement, event) {
    
    	// 一些处理
    	// On some Android devices activeElement needs to be blurred otherwise the synthetic click will have no effect (#24)
		if (document.activeElement && document.activeElement !== targetElement) {
			document.activeElement.blur();
		}
    
    
    	touch = event.changedTouches[0];

		// Synthesise a click event, with an extra attribute so it can be tracked
		clickEvent = document.createEvent('MouseEvents');
		clickEvent.initMouseEvent(this.determineEventType(targetElement), 
		true, true, window, 1, 
		touch.screenX, touch.screenY, touch.clientX, touch.clientY, 
		false, false, false, false, 0, null);
		// 给这个事件加的标签，方便跟踪
		clickEvent.forwardedTouchEvent = true; 
		targetElement.dispatchEvent(clickEvent);
}
```





[initMouseEvent 文档](https://developer.mozilla.org/zh-CN/docs/Web/API/MouseEvent/initMouseEvent)







## 补充



### event.stopPropagation

`event.stopPropagation` 只会阻止相同类型(event.type 相同)事件传播，上面有提到过 移动端 触摸事件触发的顺序问题，假如 我在 `touchstart` 中调用了 `event.stopPropagation` 只会 阻止后续 event flow 上其他 `touchstart` 事件，并不会阻止 `touchmove`，`touchend` 等 mouseEvent 事件的发生



#### stopPropagation 和 stopImmediatePropagation 的区别

`event.stopPropagation` 阻止捕获和冒泡阶段中当前事件的进一步传播。如果有多个相同类型事件的事件监听函数绑定到同一个元素，当该类型的事件触发时，它们会按照被添加的顺序执行。如果其中某个监听函数执行 `event.stopImmediatePropagation` 方法，则当前元素剩下的监听函数将不会被执行

