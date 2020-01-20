---
layout:     post
title:      "content-type相关相关"
date:       2018-05-21 13:32:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Network
---

> “Yeah It's on. ”


## 正文
[网页链接](https://www.cnblogs.com/wushifeng/p/6707248.html)


* application/x-www-form-urlencoded：数据被编码为名称/值对。这是标准的编码格式。
* multipart/form-data： 数据被编码为一条消息，页上的每个控件对应消息中的一个部分
* text/plain： 数据以纯文本形式(text/json/xml/html)进行编码，其中不含任何控件或格式字符。postman软件里标的是RAW。
* application/json



>form的enctype属性为编码方式，常用有两种：application/x-www-form-urlencoded和multipart/form-data，**默认为application/x-www-form-urlencoded**

当action为get时候，浏览器用x-www-form-urlencoded的编码方式把form数据转换成一个字串（name1=value1&name2=value2...），然后把这个字串追加到url后面，用?分割，加载这个新的url。

当action为post时候，浏览器把form数据封装到http body中，然后发送到server。 如果没有type=file的控件，用默认的application/x-www-form-urlencoded就可以了。 但是如果有type=file的话，就要用到multipart/form-data了。


当action为post且Content-Type类型是multipart/form-data，浏览器会把整个表单以控件为单位分割，并为每个部分加上Content-Disposition(form-data或者file),Content-Type(默认为text/plain),name(控件name)等信息，并加上分割符(boundary)。

**application/x-www-form-urlencoded方式是Jquery的Ajax请求默认方式**


application/json，随着json规范的越来越流行，并且浏览器支持程度原来越好，许多开发人员易application/json作为请求content-type，告诉服务器请求的主题内容是json格式的字符串，服务器端会对json字符串进行解析，这种方式的好处就是前端人员不需要关心数据结构的复杂度，只要是标准的json格式就能提交成功，application/json数据格式越来越得到开发人员的青睐。









