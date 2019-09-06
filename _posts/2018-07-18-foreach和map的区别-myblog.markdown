---
layout:     post
title:      "foreach和map的区别"
date:       2018-07-18 14:56:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”


## 正文
[网页链接](https://segmentfault.com/q/1010000013170900?utm_source=index-hottest)


map方法体现的是数据不可变的思想。该思想认为所有的数据都是不能改变的，只能通过生成新的数据来达到修改的目的，因此直接对数组元素或对象属性进行操作的行为都是不可取的。这种思想其实有很多好处，最直接的就是避免了数据的隐式修改。immutable.js是实现数据不可变的一个库，可通过专属的API对引用类型进行操作，每次形成一个新的对象。


forEach和map还存在一个编程思想的区别，前者是命令式编程，后者是声明式编程，如果项目的风格是声明式的，比如React，那么后者显然更统一。


共同点：
1. 都是循环遍历数组中的每一项。
2. forEach()和map()里面每一次执行匿名函数都支持3个参数：数组中的当前项item,当前项的索引index,原始数组input。
3. 匿名函数中的this都是指Window。
4. 只能遍历数组。





### forEach()

没有返回值
```javascript
arr[].forEach(function(value,index,array){

　　//do something

})
```

理论上这个方法是没有返回值的**，仅仅是遍历数组中的每一项，不对原来数组进行修改；但是可以自己通过数组的索引来修改原来的数组**

```javascript
var ary = [12,23,24,42,1];  
var res = ary.forEach(function (item,index,input) {  
       input[index] = item*10;  
})  
console.log(res);//--> undefined;  
console.log(ary);//--> 通过数组索引改变了原数组；  
```



### map()

有返回值，可以return 出来。


```javascript
arr[].map(function(value,index,array){

　　//do something

　　return XXX

})
```
参数：value数组中的当前项,index当前项的索引,array原始数组；



>区别：map的回调函数中支持return返回值；return的是啥，相当于把数组中的这一项变为啥（并不影响原来的数组，只是相当于把原数组克隆一份，把克隆的这一份的数组中的对应项改变了）；

```javascript
var ary = [12,23,24,42,1];  
var res = ary.map(function (item,index,input) {  
    return item*10;  
})  
console.log(res);//-->[120,230,240,420,10];  原数组拷贝了一份，并进行了修改
console.log(ary);//-->[12,23,24,42,1]；  原数组并未发生变化
```



## 重要

### forEach()中无法return和break

**我们都知道for循环里要跳出整个循环是使用break，但在数组中用forEach循环如要退出整个循环使用break会报错，使用return也不能跳出循环。**





针对这个问题可以使用数组的另外两个方法some()与every()：


```javascript
var arr = [1,2,3,4,5];
var num = 3;
arr.some(function(v){
   if(v == num) {
       return true;
   }
   console.log(v);
});
```


之所以不能break, return

是因为它不是简单的for循环。内部实现类似callback(context, arg1, arg2)。


在forEach中return，不执行下面的代码。但是回调callback，在它内部实现中还是要执行下一个callback


并且break，会出现如下错误

**Illegal break statement**


**因为break是用在循环内部的，不是回调函数执行内部**





### forEach避免循环原型


javascript中for in 会把自定义到原型上的属性和函数遍历出来。感觉怪怪的。自然可以通过 arr.hasOwnProperty(key) 这个方法过滤

用forEach可以避免这样的问题；








## 补充

### async/await 与 forEach 问题

[https://www.jianshu.com/p/18a6d889769b](https://www.jianshu.com/p/18a6d889769b)


方法一：没问题
```javascript
    (async function () {
        for (let i = 0; i < triggerArr.length; ++i) {
            await sleep();
            triggerArr[i]();
        }
    })();
```    
    
方法二：是一起输出来的，为什么？(没有等待)

```javascript
    const test = async function (item) {
            await sleep();
            item();
    };

    triggerArr.forEach(test);
```





**forEach 的回调函数是一个异步函数，异步函数中包含一个 await 等待 Promise 返回结果，我们期望数组元素串行执行这个异步操作，但是实际却是并行执行了。**






------------------------


forEach 的实现：


```javascript
Array.prototype.forEach = function (callback) {
  // this represents our array
  for (let index = 0; index < this.length; index++) {
    // We call the callback for each entry
    callback(this[index], index, this);
  }
}

```



相当于 for 循环执行了这个异步函数，所以是并行执行，导致了一次性全部输出结果：






```javascript
async function test () {
  var nums = [1, 2, 3];
//   nums.forEach(async x => {
//     var res = await multi(x);
//     console.log(res);
//   })
  for(let index = 0; index < nums.length; index++) {
    (async x => {
      var res = await multi(x);
      console.log(res);
    })(nums[index]);
  }
}
```












