---
layout:     post
title:      "byte[]和string相互转换"
date:       2019-08-12 17:11:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”


## 正文

最近在用qqtea算法进行数据的加密解密，在和后端的对接中，要使用到byte[]和string相互转换，这里简单探究一下。


>Byte数组的例子:[13, 60, 70, 27, 75, 186, 159, 221, 206, 96, 25, 89, 165, 149, 238, 90, 92, 13, 57, 38, 56, 201] 





### stringToBytes

字符串转Byte数组


**注意高性能版本：stringToBytes1测试时会有问题，并未上生产环境，尽量勿用**

```javascript
 // 高性能版本

  function stringToBytes1(str) {
    var bytes = new Array();
    var len, c;
    len = str.length;
    for (var i = 0; i < len; i++) {
      c = str.charCodeAt(i);
      if (c >= 0x010000 && c <= 0x10FFFF) {
        bytes.push(((c >> 18) & 0x07) | 0xF0);
        bytes.push(((c >> 12) & 0x3F) | 0x80);
        bytes.push(((c >> 6) & 0x3F) | 0x80);
        bytes.push((c & 0x3F) | 0x80);
      } else if (c >= 0x000800 && c <= 0x00FFFF) {
        bytes.push(((c >> 12) & 0x0F) | 0xE0);
        bytes.push(((c >> 6) & 0x3F) | 0x80);
        bytes.push((c & 0x3F) | 0x80);
      } else if (c >= 0x000080 && c <= 0x0007FF) {
        bytes.push(((c >> 6) & 0x1F) | 0xC0);
        bytes.push((c & 0x3F) | 0x80);
      } else {
        bytes.push(c & 0xFF);
      }
    }
    return bytes;
  }
```

```javascript
  // 低性能版本
  function stringToBytes2(str) {
    let ch;
    let st;
    let re = [];
    for (let i = 0; i < str.length; i++) {
      ch = str.charCodeAt(i); // get char
      st = []; // set up "stack"
      do {
        st.push(ch & 0xFF); // push byte to stack
        ch = ch >> 8; // shift value down by 1 byte
      }
      while (ch);
      re = re.concat(st.reverse());
    }
    return re;
  }
```


1000次测试：

* stringToBytes1 耗时10ms左右
* stringToBytes2 耗时260ms左右



### bytesToString

Byte数组转字符串转

```javascript
function bytesToString(arr) {
  let str = ""
  for (let i = 0; i < arr.length; i++) {
    str += String.fromCharCode(arr[i]);
  }
  return str;
}
```


1000次测试：

* bytesToString 耗时10ms左右




