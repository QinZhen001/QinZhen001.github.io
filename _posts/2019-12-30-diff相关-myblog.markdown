---
layout:     post
title:      "diff相关"
date:       2019-12-30 20:31:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - JavaScript
---

> “Yeah It's on. ”


## 正文

[https://github.com/mattphillips/deep-object-diff/blob/master/src/diff/index.js](https://github.com/mattphillips/deep-object-diff/blob/master/src/diff/index.js)



最近，在写一个小程序框架，由于小程序原生setData所做的diff并不够高效，所以打算自己来搞diff


### deep-object-diff


找到了一个diff库

```javascript
export const properObject = o => isObject(o) && !o.hasOwnProperty ? { ...o } : o;
```






```javascript
import { isDate, isEmpty, isObject, properObject } from '../utils';


  const diff = (lhs, rhs) => {
    if (lhs === rhs) return {}; // equal return no diff

    if (!isObject(lhs) || !isObject(rhs)) return rhs; // return updated rhs

    const l = properObject(lhs);
    const r = properObject(rhs);

    // 找到删除了的属性  (循环l)
    const deletedValues = Object.keys(l).reduce((acc, key) => {
      // 找到l有r没有的属性
      return r.hasOwnProperty(key) ? acc : {...acc, [key]: undefined};
    }, {});

    // 存在日期
    if (isDate(l) || isDate(r)) {
      if (l.valueOf() == r.valueOf()) return {};
      return r;
    }

    // (循环r)
    return Object.keys(r).reduce((acc, key) => {
      // return added r key (找到r有l没有的属性)
      if (!l.hasOwnProperty(key)) return {...acc, [key]: r[key]};
      // l有r也有的属性 递归diff
      const difference = diff(l[key], r[key]);
      // return no diff
      if (isObject(difference) && isEmpty(difference) && !isDate(difference)) return acc;

      return {...acc, [key]: difference}; // return updated key
    }, deletedValues);
  };

```


wxa框架中的diff就是采用这个diff


[https://github.com/wxajs/wxa/blob/dev/packages/wxa-core/src/diff/diff.js](https://github.com/wxajs/wxa/blob/dev/packages/wxa-core/src/diff/diff.js)




### flat

[https://github.com/hughsk/flat](https://github.com/hughsk/flat)



Take a nested Javascript object and flatten it, or unflatten an object with delimited keys.




取一个嵌套的Javascript对象并将其展平，或使用分隔键取消展平一个对象。




### is-buffer

[https://www.npmjs.com/package/is-buffer](https://www.npmjs.com/package/is-buffer)


Determine if an object is a Buffer (including the browserify Buffer)


-----

Why not use Buffer.isBuffer?


This module lets you check if an object is a Buffer without using Buffer.isBuffer (which includes the whole buffer module in browserify). It's future-proof and works in node too








