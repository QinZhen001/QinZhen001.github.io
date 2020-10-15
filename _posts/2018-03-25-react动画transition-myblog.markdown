---
layout:     post
title:      "rc-queue-anim"
date:       2018-03-25 10:41:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


# 正文


## rc-queue-anim



[网页链接](https://www.npmjs.com/package/rc-queue-anim)


Animate React Component in queue, thanks to rc-animate and enter-animation.


Usage
```tsx
import QueueAnim from 'rc-queue-anim';
import React from 'react';
import ReactDom from 'react-dom';
 
ReactDom.render(
  <QueueAnim>
    <div key="1">enter in queue</div>
    <div key="2">enter in queue</div>
    <div key="3">enter in queue</div>
  </QueueAnim>
, mountNode);
```





## react-transition-group

[http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit](http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit)





### [`unmountOnExit`](http://reactcommunity.org/react-transition-group/transition#Transition-prop-unmountOnExit)

By default the child component stays mounted after it reaches the `'exited'` state. Set `unmountOnExit` if you'd prefer to unmount the component after it finishes exiting.

* type: `boolean`
* default: `false`





### [`appear`](http://reactcommunity.org/react-transition-group/transition#Transition-prop-appear)

By default the child component does not perform the enter transition when it first mounts, regardless of the value of `in`. If you want this behavior, set both `appear` and `in` to `true`.

> **Note**: there are no special appear states like `appearing`/`appeared`, this prop only adds an additional enter transition. However, in the `<CSSTransition>` component that first enter transition does result in additional `.appear-*` classes, that way you can choose to style it differently.

* type: `boolean`
* default: `false`







### 测试

[https://reactcommunity.org/react-transition-group/testing](https://reactcommunity.org/react-transition-group/testing)



> 当我们使用测试框架事关闭react-transition-group的动画过渡效果

In some situations, like visual snapshot testing, it's helpful to disable transitions so they don't complicate the test, or introduce abitrary waits. To make this easier `react-transition-group` exposes a way to globally toggle transitions. When set, **all** transitions, when toggled, will immediately switch to their entered or exited states as appropriate.



```js
import { config } from 'react-transition-group

config.disabled = true
```



