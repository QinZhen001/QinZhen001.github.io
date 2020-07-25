---
layout:     post
title:      "Property和Attribute的区别"
date:       2017-12-17 19:37:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”


## 正文


[网页链接](http://blog.csdn.net/u013510614/article/details/51923959)

property 和 attribute非常容易混淆，两个单词的中文翻译也都非常相近（property：属性，attribute：特性），但实际上，二者是不同的东西，属于不同的范畴。

* property是DOM中的属性，是JavaScript里的对象；
* Attribute就是dom节点自带的属性，例如html中常用的id、class、title、align等，它的值只能够是字符串；





**只要是DOM标签中出现的属性（html代码），都是Attribute**




 html中有这样一段代码：

`<input id="in_1" value="1" sth="whatever">`

简单的在html页面上创建一个input输入栏（注意在这个标签中添加了一个DOM中不存在的属性“sth”），此时在JS执行如下语句
```js
var in1 = document.getElementById('in_1');
```
执行语句
```js
console.log(in1);
```
从console的打印结果，可以看到in1含有一个名为“attributes”的属性，它的类型是NamedNodeMap，同时还有“id”和“value”两个基本的属性，但没有“sth”这个自定义的属性。
```
attributes: NamedNodeMap
value: "1"
id: "in_1"
```
有些console可能不会打印in1上的属性，那么可以执行以下命令打印要观察的属性：
```
console.log(in1.id);		// 'in_1'
console.log(in1.value);		// 1
console.log(in1.sth);		// undefined
```
可以发现，标签中的三个属性，只有“id”和“value”会在in1上创建，而“sth”不会被创建。这是由于，每一个DOM对象都会有它默认的基本属性，而在创建的时候，它只会创建这些基本属性，**我们在TAG标签中自定义的属性是不会直接放到DOM中的**。

* DOM有其默认的基本属性，而这些属性就是所谓的“property”，无论如何，它们都会在初始化的时候再DOM对象上创建。
* 如果在TAG对这些属性进行赋值，那么这些值就会作为初始值赋给DOM的同名property。


回到第一个input（“#in_1”），我们就会问，“sth”去哪里了？别急，我们把attributes这个属性打印出来看看

```
NamedNodeMap {0: id, 1: value, 2: sth, length: 3}
length:3
0:id
1:value
2:sth
__proto__:NamedNodeMap
```


原来“sth”被放到了attributes这个对象里面，这个对象按顺序记录了我们在TAG中定义的属性和属性的数量

**attributes是属于property的一个子集，它保存了HTML标签上定义属性**

可以得出：
* HTML标签中定义的属性和值会保存该DOM对象的attributes属性里面；
* 这些attribute属性的JavaScript中的类型是Attr，而不仅仅是保存属性名和值这么简单；


----------


如果我们更改property和attribute的值会出现什么效果呢？
```
in1.value = 'new value of prop';
console.log(in1.value);				// 'new value of prop'
console.log(in1.attributes.value);	// 'value="1"'
```

**propety中的value也变成了新的值，但attributes却仍然是“1”。从这里可以推断，property和attribute的同名属性的值并不是双向绑定的。**

如果反过来，设置attitudes中的值，效果会怎样呢？

**in1.attributes.value.value**

```
in1.attributes.value.value = 'new value of attr';
console.log(in1.value);				// 'new value of attr'
console.log(in1.attributes.value);	// 'new value of attr'
```
都发生了改变

执行下面语句也会得到一样的结果
in1.attributes.value.nodeValue = 'new value of attr';

由此，可得出结论：
* property能够从attribute中得到同步；
* attribute不会同步property上的值；
* attribute和property之间的数据绑定是单向的，attribute->property；
* 更改property和attribute上的任意值，都会将更新反映到HTML页面中；


### 基于jQuery分析attribute和property
首先利用jQuery.prop来测试
```
$(in1).prop('value', 'new prop form $');

console.log(in1.value);				// 'new prop form $'
console.log(in1.attributes.value);	// '1'
```
输入栏的值更新了，但attribute并未更新。

然后用jQuery.attr来测试
```
$(in1).attr('value', 'new attr form $');

console.log(in1.value);				// 'new attr form $'
console.log(in1.attributes.value);	// 'new attr form $'
```
输入栏的值更新了，同时property和attribute都更新了。

### 特殊的例子

#### href
下面我们看看href这个属性/特性。

```
<a href='page_1.html' id='a_1'></a>
```
在JS脚本中执行如下代码：
```
var a1 = document.getElementById('a_1');
console.log(a1.href);	// 'file:///D:/GitHub/JS/html/test_01/page_1.html'
console.log(a1.getAttribute('href'));	// 'page_1.html'
```

更改attribute：
```
a1.setAttribute('href', 'page_2.html');		// 相对路径
console.log(a1.href);	// 'file:///D:/GitHub/JS/html/test_01/page_2.html'
console.log(a1.getAttribute('href'));	// 'page_2.html'

a1.setAttribute('href', '/page_3.html');	// 根目录路径
console.log(a1.href);						// 'file:///D:/page_3.html'
console.log(a1.getAttribute('href'));		// '/page_3.html'
```
更改property：
```
a1.href = 'home.html';	// 相对路径
console.log(a1.href);	// 'file:///D:/GitHub/JS/html/test_01/home.html'
console.log(a1.getAttribute('href'));	// 'home.html'

a1.href = '/home.html';	// 根目录路径
console.log(a1.href);	// 'file:///D:/home.html'
console.log(a1.getAttribute('href'));	// '/home.html'
```

从这里可以发现，href是特殊的属性/特性，二者是双向绑定的，更改任意一方，都会导致另一方的的值发生改变。而且，这并不是简单的双向绑定，**property中的href永远保存绝对路径，而attribute中的href则是保存相对路径。**

#### id
尝试改变property中的id：
```
	a1.id = 'new_id';
	console.log(a1.id);						// 'new_id'
	console.log(a1.getAttribute('id'));		// 'new_id'
```
attribute中的id从property中的id发生了同步，数据方向变成了property <=> attribute；


##  总结

### 创建
* DOM对象初始化时会在创建默认的基本property；
* 只有在HTML标签中定义的attribute才会被保存在property的attributes属性中；
* attribute会初始化property中的同名属性，但自定义的attribute不会出现在property中；
* **attribute的值都是字符串**；


### 数据绑定
* attributes的数据会同步到property上，然而property的更改不会改变attribute；
* 对于value，class这样的属性/特性，数据绑定的方向是单向的，attribute->property；
* 对于id而言，数据绑定是双向的，attribute<=>property；
* 对于disabled而言，property上的disabled为false时，attribute上的disabled必定会并存在，此时数据绑定可以认为是双向的；

### 使用
* 可以使用DOM的setAttribute方法来同时更改attribute；
* 直接访问attributes上的值会得到一个Attr对象，而通过getAttribute方法访问则会直接得到attribute的值；
* 大多数情况（除非有浏览器兼容性问题），jQuery.attr是通过setAttribute实现，而jQuery.prop则会直接访问DOM对象的property；


----------


* **attribute（特性），是我们赋予某个事物的特质或对象。**
* **property（属性），是早已存在的不需要外界赋予的特质。**


----------

* **特性和属性两者的存储方式不同；**
* **“脚踏两只船”要了解；**
* **DOM属性可能会导致循环引用内存泄漏。**




## 补充

### Attribute取值&赋值

getAttribute()可以取得任何特性，不管是标准的还是自定义的。

但是这个方法的浏览器兼容性有问题，有些浏览器可能会获取属性Property的值，因此jQuery要做一个测试，看getAttribute()是否是绝对获取特性Attribute的值。
```
div1.className = 'a';
var judge = div1.getAttribute("className") === 'a';
```

**如果以上代码成立，说明getAttribute()方法出现了问题，将不再使用。**




用setAttrbute()赋值，任何Attribute都可以，包括自定义的。而且，赋值的Attribute会立刻表现到DOM元素上。


**最后注意，setAttribute()的两个参数，都必须是字符串**。即对特性Attribute职能赋值字符串，而对属性Property就可以赋任何类型的值了。


### Property取值&赋值
属性取值很简单。取任何属性的只，用“.”就可以：
```
var id = div1.id;
var className = div1.className;
var childNodes = div1.childNodes;
var attrs = div1.attributes;
```
赋值和基本的js对象属性赋值一样，用“.”即可：
```
div1.className = 'a';
div1.align = 'center';
div1.AAAAA = true;
div1.BBBBB = [1, 2, 3];
```



再次强调：
**class特性在变成属性时，名字改成了“className”，因此div1.className和div1.getAttrbute('class')相同。**


**上面代码中的div1.attributes是取的attributes这一属性，取出来保存到attrs变量中，attrs就成了一个NamedNodeList类型的对象，里面存储了若干个Attr类型。**


>对于属性Property的赋值在IE中可能会引起循环引用，内存泄漏。为了防止这个问题，jQuery.data()做了特殊处理，解耦了数据和DOM对象


### style和onclick
用“.”获取Style：
```
<div id="div1" class="divClass" style="width:100%; padding:10px;">100</div>

console.log(div1.style);
```
以上代码中，返回了一个CSSStyleDeclaration对象，这个对象中包含着样式的所有信息



用getAttribute()获取style
```
<div id="div1" class="divClass" style="width:100%; padding:10px;">100</div>

console.log(div1.getAttribute("style"));
```
以上代码返回的就是一个简单的字符串：“width:100%; padding:10px;”






















