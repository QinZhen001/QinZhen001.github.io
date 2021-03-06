---
layout:     post
title:      "在小程序请求中进行qqtea加密解密"
date:       2019-11-20 15:13:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 微信小程序
---

> “Yeah It's on. ”


## 正文

最近爬了一个大坑，为了防止别人爬取数据，在小程序请求中进行qqtea加密解密，以二进制流的形式和后端进行交互



### 核心代码



```javascript
 if (needEncrypt) {
    // 当前环境是需要的环境 且 此请求需要加密
    console.log(`${name} 请求的参数: \n`, data)
    data = qqtea.encrypt(JSON.stringify(data), TEA_KEY)
    data = str2ab(data)
    options.header = {'content-type': 'application/octet-stream'}
    options.responseType = "arraybuffer"
  }
  wx.request({
    responseType: options.responseType || "text",
    url: options.url,
    data: data,
    method: options.method || 'POST',
    header: options.header || {'content-type': 'application/x-www-form-urlencoded'},
    success: (res) => {
      if (res.statusCode >= 200 && res.statusCode < 300) {
        if (needEncrypt) {
          let byteArr = new Uint8Array(res.data)
          res.data = qqtea.decrypt(uint8ArrayToString(byteArr), TEA_KEY);
          res.data = JSON.parse(res.data)
          debugger
        }
        resolve({...res, code: 0});
      } else {
        reject({...res, code: 0});
      }
    },
    fail: (res) => {
      reject({...res, code: 1});
    },
  });
```

```javascript
/**
 * string => arrayBuffer
 * @param str
 * @returns {ArrayBuffer}
 */
function str2ab(str) {
  let strLen = str.length
  let buf = new ArrayBuffer(strLen);
  let bufView = new Uint8Array(buf);
  for (let i = 0; i < strLen; i++) {
    bufView[i] = str.charCodeAt(i);
  }
  return buf;
}
```



```javascript
/**
 * @return {string}
 */
function uint8ArrayToString(arr) {
  let dataString = "";
  for (let i = 0; i < arr.length; i++) {
    dataString += String.fromCharCode(arr[i]);
  }
  return dataString
}
```



## 补充 


这里就用到了大量arraybuffer的知识


### arraybuffer

[http://es6.ruanyifeng.com/#docs/arraybuffer](http://es6.ruanyifeng.com/#docs/arraybuffer)


ArrayBuffer对象、TypedArray视图和DataView视图是 JavaScript 操作二进制数据的一个接口

----


ArrayBuffer对象：代表内存之中的一段二进制数据，可以通过“视图”进行操作。“视图”部署了数组接口，这意味着，可以用数组的方法操作内存。


TypedArray视图：共包括 9 种类型的视图，比如**Uint8Array**（无符号 8 位整数）数组视图, Int16Array（16 位整数）数组视图, Float32Array（32 位浮点数）数组视图等等。


DataView视图：可以自定义复合格式的视图，比如第一个字节是 Uint8（无符号 8 位整数）、第二、三个字节是 Int16（16 位整数）、第四个字节开始是 Float32（32 位浮点数）等等，此外还可以自定义字节序。




**简单说，ArrayBuffer对象代表原始的二进制数据，TypedArray视图用来读写简单类型的二进制数据，DataView视图用来读写复杂类型的二进制数据。**



注意，二进制数组并不是真正的数组，而是类似数组的对象。









