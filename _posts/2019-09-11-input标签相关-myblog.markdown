---
layout:     post
title:      "input标签相关"
date:       2019-09-11 11:59:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Html
---

> “Yeah It's on. ”

# 正文



## 上传文件



[https://juejin.im/post/5aacc64e6fb9a028c6757bab](https://juejin.im/post/5aacc64e6fb9a028c6757bab)


```html
<input class="addPicInput" type="file" ref="uploadFile"
       @change="fileChange" accept="image/*" multiple>
```


>multiple


在实际开发过程中，会遇到上传文件的一些需求。但是使用原生的`<input type="file" />`在使用中存在一些问题



* 在未上传文件时，显示"no file choosen"，用户界面不友好，不可配置
* 上传同一个文件，不会触发change事件，即使该文件做过修改
* 用户如果在上传过程中点击了“取消”，已经上传的文件会被移除



### 解决思路


在阅读了一些源码之后，总结了如下的解决方案。有点偷梁换柱的意思:



* 将真正的`<input type="file" />`隐藏，使用自定义的button通过$refs去触发文件上传，实现自定义显示
* 文件上传之后，处理完文件，将`<input type="file" />`的value设置为null，这样下次即使上传的是同一个文件，仍然会触发change事件
* 使用上述方法，点击取消文件被移除，但是不影响页面展示











## 监听中文输入

[https://juejin.cn/post/6844903950634713096](https://juejin.cn/post/6844903950634713096)

compositionstart和compositionend

> The compositionstart event is fired when a text composition system such as an `input method editor starts a new composition session`.For example, this event could be fired after a user starts entering a Chinese character using a Pinyin IME.

> The compositionend event is fired when a text composition system such as an `input method editor completes or cancels the current composition session`.For example, this event could be fired after a user finishes entering a Chinese character using a Pinyin IME.

- `positionstart` 事件,当用户使用拼音输入法开始输入汉字时，这个事件就会被触发。
- `compositionend` 事件, 当文中文输入完成时, compositionend 事件将被触发。

















