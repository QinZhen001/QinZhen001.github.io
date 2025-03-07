---
layout:     post
title:      "MongoDB相关"
date:       2018-01-05 22:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 数据库
---

> “Yeah It's on. ”


## 正文

[网页链接](http://www.runoob.com/mongodb/mongodb-tutorial.html)

### NoSQL 简介
NoSQL(NoSQL = Not Only SQL )，意即"不仅仅是SQL"。
在现代的计算系统上每天网络上都会产生庞大的数据量。
这些数据有很大一部分是由关系数据库管理系统（RDMBS）来处理。
NoSQL 是一项全新的数据库革命性运动，早期就有人提出，发展至2009年趋势越发高涨。NoSQL的拥护者们提倡运用非关系型的数据存储，相对于铺天盖地的关系型数据库运用，这一概念无疑是一种全新的思维的注入。

NoSQL用于超大规模数据的存储。（例如谷歌或Facebook每天为他们的用户收集万亿比特的数据）。这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。

### 为什么使用NoSQL
今天我们可以通过第三方平台（如：Google,Facebook等）可以很容易的访问和抓取数据。用户的个人信息，社交网络，地理位置，用户生成的数据和用户操作日志已经成倍的增加。我们如果要对这些用户数据进行挖掘，那SQL数据库已经不适合这些应用了, NoSQL数据库的发展也却能很好的处理这些大的数据。

### NoSQL的优点
* 高可扩展性
* 分布式计算
* 低成本
* 架构的灵活性，半结构化数据
* 没有复杂的关系

### NoSQL的缺点
* 没有标准化
* 有限的查询功能（到目前为止）
* 最终一致是不直观的程序

### 什么是MongoDB
MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
在高负载的情况下，添加更多的节点，可以保证服务器性能。
MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。
MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

![enter description here][1]

### 主要特点
* MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。
* 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
* 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
* 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
* Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
* MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
* Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
* Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。
* Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
* GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
* MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
* MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
* MongoDB安装简单。



### 启动

以后台服务形式启动 MongoDB

```bash
brew services start mongodb-community
```

访问 `http://localhost:27017`

默认主机： 127.0.0.1

默认端口：27017

默认账号：admin

默认密码：admin





### 停止

```bash
brew services stop mongodb-community
```



### 检查服务状态

```bash
brew services list
```





### MongoDB 工具

有几种可用于MongoDB的管理工具。

#### 监控
MongoDB提供了网络和系统监控工具Munin，它作为一个插件应用于MongoDB中。
Gangila是MongoDB高性能的系统监视的工具，它作为一个插件应用于MongoDB中。
基于图形界面的开源工具 Cacti, 用于查看CPU负载, 网络带宽利用率,它也提供了一个应用于监控 MongoDB 的插件。

#### GUI
* Fang of Mongo – 网页式,由Django和jQuery所构成。
* Futon4Mongo – 一个CouchDB Futon web的mongodb山寨版。
* Mongo3 – Ruby写成。
* MongoHub – 适用于OSX的应用程序。
* Opricot – 一个基于浏览器的MongoDB控制台, 由PHP撰写而成。
* Database Master — Windows的mongodb管理工具
* RockMongo — 最好的PHP语言的MongoDB管理工具，轻量级, 支持多国语言.

### MongoDB 概念解析

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| ------------ | ---------------- | ----------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |



![enter description here][2]

一个mongodb中可以建立多个数据库。
MongoDB的默认数据库为"db"，该数据库存储在data目录中。
MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

数据库也通过名字来标识。数据库名可以是满足以下条件的任意UTF-8字符串。
* 不能是空字符串（"")。
* 不得含有' '（空格)、.、$、/、\和\0 (空字符)。
* 应全部小写。
* 最多64字节。

有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。
* admin： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
* local: 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
* config: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

#### 文档
文档是一组键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。
一个简单的文档例子如下：
```
{"site":"www.runoob.com", "name":"菜鸟教程"}
```

* 文档中的键/值对是有序的。
* 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
* MongoDB区分类型和大小写。
* MongoDB的文档不能有重复的键。
* 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：
* 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
* .和$有特别的意义，只有在特定环境下才能使用。
* 以下划线"_"开头的键是保留的(不是严格要求的)。

#### 集合
集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。
集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。
比如，我们可以将以下不同数据结构的文档插入到集合中：
```
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
```
当第一个文档插入时，集合就会被创建。

合法的集合名
* 集合名不能是空字符串""。
* 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
* 集合名不能以"system."开头，这是为系统集合保留的前缀。
* 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。　

### MongoDB 数据类型
下表为MongoDB中常用的几种数据类型。
* String	字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。
* Integer	整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。
* Boolean	布尔值。用于存储布尔值（真/假）。
* Double	双精度浮点值。用于存储浮点值。
Min/Max keys	将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。
* Array	用于将数组或列表或多个值存储为一个键。
* Timestamp	时间戳。记录文档修改或添加的具体时间。
* Object	用于内嵌文档。
* Null	用于创建空值。
* Symbol	符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。
* Date	日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。
* Object ID	对象 ID。用于创建文档的 ID。
* Binary Data	二进制数据。用于存储二进制数据。
* Code	代码类型。用于在文档中存储 JavaScript 代码。
* Regular expression	正则表达式类型。用于存储正则表达式。





## Mongoose

[网页链接](https://www.cnblogs.com/xiaohuochai/p/7215067.html?utm_source=itdadao&utm_medium=referral)

Mongoose是在node.js异步环境下对mongodb进行便捷操作的对象模型工具。本文将详细介绍如何使用Mongoose来操作MongoDB


### NodeJS驱动

首先，安装mongodb

`npm install mongodb`

接着，使用require()方法引入mongodb数据库；然后使用MongoClient对象的connect()方法连接mongodb；最后通过node来对mongodb进行异步的增删改查

在mongodb数据库中建立db1数据库，然后通过以下代码，建立col集合，并插入{"a":1}文档

```
var mongodb = require('mongodb');
mongodb.MongoClient.connect("mongodb://localhost/db1",function(err,db){
    if(!err){
        db.collection("col").insert({"a":1},function(err,result){
            if(!err){
                console.log(result);
            }
        })
    }
})
```

最后返回结果如下

```
{ result: { ok: 1, n: 1 },
  ops: [ { a: 1, _id: 597077dc271d092728caa362 } ],
  insertedCount: 1,
  insertedIds: [ 597077dc271d092728caa362 ] }
```


### 概述

Mongoose是NodeJS的驱动，不能作为其他语言的驱动。Mongoose有两个特点

1. 通过关系型数据库的思想来设计非关系型数据库
2. 基于mongodb驱动，简化操作



![enter description here][1]

　Mongooose中，有三个比较重要的概念，分别是Schema、Model、Entity。**它们的关系是：Schema生成Model，Model创造Document，Model和Document都可对数据库操作造成影响，但Model比Document更具操作性**

* Schema用于定义数据库的结构。类似创建表时的数据定义(不仅仅可以定义文档的结构和属性，还可以定义文档的实例方法、静态模型方法、复合索引等)，每个Schema会映射到mongodb中的一个collection，Schema不具备操作数据库的能力
* Model是由Schema编译而成的构造器，具有抽象属性和行为，可以对数据库进行增删查改。Model的每一个实例（instance）就是一个文档document
* Document是由Model创建的实体，它的操作也会影响数据库


### 安装

安装nodejs和mongodb之后 ，使用npm来安装mongoose

`npm install mongoose`

安装成功后，就可以通过 require('mongoose') 来使用


### 连接数据库

使用require()方法在项目中包含mongoose后，接下来使用connect()方法连接到MongoDB数据库
　
mongoose.connect(url);
connect()最简单的使用方式，就是只要传入url参数即可，如下所示。连接到本地localhost的db1服务器　
`mongoose.connect('mongodb://localhost/db1');`

如果还需要传递用户名、密码，则可以使用如下方式

```
mongoose.connect('mongodb://username:password@host:port/database?options...');
```

　connect()方法还接受一个选项对象options，该对象将传递给底层驱动程序。这里所包含的所有选项优先于连接字符串中传递的选项
　
mongoose.connect(uri, options);
db            -数据库设置
 server        -服务器设置
 replset       -副本集设置
 user          -用户名
 pass          -密码
 auth          -鉴权选项
 mongos        -连接多个数据库
 promiseLibrary

```
var options = {
  db: { native_parser: true },
  server: { poolSize: 5 },
  replset: { rs_name: 'myReplicaSetName' },
  user: 'myUserName',
  pass: 'myPassword'
}
mongoose.connect(uri, options);
```

如果要连接多个数据库，只需要设置多个url以,隔开，同时设置mongos为true

```
mongoose.connect('urlA,urlB,...', {
   mongos : true 
})
```


　connect()函数还接受一个回调参数

```
mongoose.connect(uri, options, function(error) {

});
```

执行下列代码后，控制台输出“连接成功”

```
var mongoose = require('mongoose');
mongoose.connect("mongodb://localhost/test", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
    }
});
```

 　　如果开启鉴权控制，以用户名"u1"，密码"123456"登录'db1'数据库。执行代码后，控制台输出“连接成功”

```
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
    }
});
```


### mongoose.disconnect()

使用disconnect()方法可以断开连接

```
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
    }
});
setTimeout(function(){
    mongoose.disconnect(function(){
        console.log("断开连接");
    })
}, 2000);
```

### Schema

Schema主要用于定义MongoDB中集合Collection里文档document的结构　　
定义Schema非常简单，指定字段名和类型即可，支持的类型包括以下8种

```
String      字符串
Number      数字    
Date        日期
Buffer      二进制
Boolean     布尔值
Mixed       混合类型
ObjectId    对象ID    
Array       数组
```

通过mongoose.Schema来调用Schema，然后使用new方法来创建schema对象

```
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var mySchema = new Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```

创建Schema对象时，声明字段类型有两种方法，一种是首字母大写的字段类型，另一种是引号包含的小写字段类型

```
var mySchema = new Schema({title:String, author:String});
//或者 
var mySchema = new Schema({title:'string', author:'string'});
```


　如果需要在Schema定义后添加其他字段，可以使用add()方法
　
　

```
var MySchema = new Schema;
MySchema.add({ name: 'string', color: 'string', price: 'number' });
```


### Model

模型Model是根据Schema编译出的构造器，或者称为类，通过Model可以实例化出文档对象document

文档document的创建和检索都需要通过模型Model来处理

使用model()方法，将Schema编译为Model。model()方法的第一个参数是模型名称

>一定要将model()方法的第一个参数和其返回值设置为相同的值，否则会出现不可预知的结果

　　Mongoose会将集合名称设置为模型名称的小写版。如果名称的最后一个字符是字母，则会变成复数；如果名称的最后一个字符是数字，则不变；如果模型名称为"MyModel"，则集合名称为"mymodels"；如果模型名称为"Model1"，则集合名称为"model1"
　　

### 实例化文档document　　

通过对原型Model1使用new方法，实例化出文档document对象　


```
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(err){
        console.log('连接失败');
    }else{
        console.log('连接成功');
        var schema = new mongoose.Schema({ num:Number, name: String, size: String});
        var MyModel = mongoose.model('MyModel', schema);
        var doc1 = new MyModel({ size: 'small' });
        console.log(doc1.size);//'small'
    }
});
```

### 文档保存　　

通过new Model1()创建的文档doc1，必须通过save()方法，才能将创建的文档保存到数据库的集合中，集合名称为模型名称的小写复数版　　
　　
回调函数是可选项，第一个参数为err，第二个参数为保存的文档对象
save(function (err, doc) {})`
　　
　　
　　

```
var mongoose = require('mongoose');
mongoose.connect("mongodb://u1:123456@localhost/db1", function(err) {
    if(!err){
        var schema = new mongoose.Schema({ num:Number, name: String, size: String });
        var MyModel = mongoose.model('MyModel', schema);
        var doc1 = new MyModel({ size: 'small' });
        doc1.save(function (err,doc) {
        //{ __v: 0, size: 'small', _id: 5970daba61162662b45a24a1 }
          console.log(doc);
        })
    }
});
```

























[1]: http://www.runoob.com/wp-content/uploads/2013/10/crud-annotated-document.png
[2]: http://www.runoob.com/wp-content/uploads/2013/10/Figure-1-Mapping-Table-to-Collection-1.png