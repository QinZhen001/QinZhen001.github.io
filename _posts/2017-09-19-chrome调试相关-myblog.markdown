---
layout:     post
title:      "chrome调试相关"
date:       2017-09-19 22:48:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 调试
---

> “Yeah It's on. ”


# 正文


## 手机调试模式下鼠标小圆点消失

[网页链接](https://segmentfault.com/q/1010000006084696)

ctrl+shift+c会toggle选择dom的状态， 此时光标会变为非模拟touch


### 最终解决方案

HDMI线下部分显卡驱动的造成的,更改设置

以Intel核显为例，在显卡控制面板里选择「显示-量化范围」（在nvidia那边叫动态范围），里面有三项：

* 默认范围 Default Range
* 有限范围 Limited Range
* 全范围 Full Range

勾上「全范围」，保存看chrome里那个小圆点就出来了




> Tip：

**其实选 有限范围 也可以**







## 显示需要鼠标focus才出现元素

一开始我在触发元素上设置Force state，发现气泡弹窗并未弹出

![](https://s3.ax1x.com/2021/01/20/sWfv6K.png)

最终解决：

使用这个 Break on => 



或者:

找到这个元素 （一般直接覆在body上）

```html
<div role="tooltip" id="el-popover-9073" aria-hidden="true" class="el-popover el-popper" tabindex="0" style="transform-origin: left center; z-index: 2011; display: none;"><!----><div data-v-7b616fd4=""><div data-v-7b616fd4="">
    ....
</div>
```

改成

```html
<div role="tooltip" id="el-popover-9073" aria-hidden="true" class="el-popover el-popper" tabindex="0" style="transform-origin: left center; z-index: 2011; display: none;"><!----><div data-v-7b616fd4=""><div data-v-7b616fd4="">
    ....
</div>
```



