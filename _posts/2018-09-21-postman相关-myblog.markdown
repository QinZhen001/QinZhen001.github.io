---
layout:     post
title:      "postman相关"
date:       2018-09-21 12:08:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Network
---

> “Yeah It's on. ”


## 正文
[网页链接](https://blog.csdn.net/ye1992/article/details/49998511)

用户在开发或者调试网络程序或者是网页B/S模式的程序的时候是需要一些方法来跟踪网页请求的，用户可以使用一些网络的监视工具比如著名的Firebug等网页调试工具。今天给大家介绍的这款网页调试工具不仅可以调试简单的css、html、脚本等简单的网页基本信息，**它还可以发送几乎所有类型的HTTP请求**！Postman在发送网络HTTP请求方面可以说是Chrome插件类产品中的代表产品之一。


### post请求


#### form-data

就是http请求中的**multipart/form-data**,它会将表单的数据处理为一条消息，以标签为单元，用分隔符分开。既可以上传键值对，也可以上传文件。当上传的字段是文件时，会有Content-Type来表名文件类型；content-disposition，用来说明字段的一些信息；

由于有boundary隔离，所以multipart/form-data既可以上传文件，也可以上传键值对，它采用了键值对的方式，所以可以上传多个文件。

For multipart/form-data we use the form-data library by @felixge. For the most cases, you can pass your upload form data via the formData option.

```javascript
var formData = {
  // Pass a simple key-value pair
  my_field: 'my_value',
  // Pass data via Buffers
  my_buffer: Buffer.from([1, 2, 3]),
  // Pass data via Streams
  my_file: fs.createReadStream(__dirname + '/unicycle.jpg'),
  // Pass multiple values /w an Array
  attachments: [
    fs.createReadStream(__dirname + '/attachment1.jpg'),
    fs.createReadStream(__dirname + '/attachment2.jpg')
  ],
  // Pass optional meta-data with an 'options' object with style: {value: DATA, options: OPTIONS}
  // Use case: for some types of streams, you'll need to provide "file"-related information manually.
  // See the `form-data` README for more information about options: https://github.com/form-data/form-data
  custom_file: {
    value:  fs.createReadStream('/dev/urandom'),
    options: {
      filename: 'topsecret.jpg',
      contentType: 'image/jpeg'
    }
  }
};
request.post({url:'http://service.com/upload', formData: formData}, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('upload failed:', err);
  }
  console.log('Upload successful!  Server responded with:', body);
});
```


#### x-www-form-urlencoded
就是application/x-www-from-urlencoded,会将表单内的数据转换为键值对，比如,name=java&age = 23


#### raw
可以上传任意格式的文本，可以上传text、json、xml、html等


#### binary
相当于Content-Type:application/octet-stream,从字面意思得知，只可以上传二进制数据，通常用来上传文件，由于没有键值，所以，一次只能上传一个文件。




### multipart/form-data与x-www-form-urlencoded区别
 * multipart/form-data：既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息；
* x-www-form-urlencoded：只能上传键值对，并且键值对都是间隔分开的。