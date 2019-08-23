---
layout:     post
title:      "jest单元测试"
date:       2019-08-22 16:04:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 测试
---

> “Yeah It's on. ”


## 正文


[https://jestjs.io/docs/en/getting-started](https://jestjs.io/docs/en/getting-started)


```javascript
npm install --save-dev jest
```


Let's get started by writing a test for a hypothetical function that adds two numbers. First, create a sum.js file:
```javascript
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```
Then, create a file named sum.test.js. This will contain our actual test:

```javascript
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```


Add the following section to your package.json:

```javascript
{
  "scripts": {
    "test": "jest"
  }
}
```

Finally, run yarn test or npm run test and Jest will print this message:


```javascript
PASS  ./sum.test.js
✓ adds 1 + 2 to equal 3 (5ms)
```



### toEqual

toBe uses Object.is to test exact equality. If you want to check the value of an object, use toEqual instead:

```javascript
test('object assignment', () => {
  const data = {one: 1};
  data['two'] = 2;
  expect(data).toEqual({one: 1, two: 2});
});

```



toEqual recursively checks every field of an object or array.




### toBeCloseTo


For floating point equality, use toBeCloseTo instead of toEqual, because you don't want a test to depend on a tiny rounding error.

```javascript
test('adding floating point numbers', () => {
  const value = 0.1 + 0.2;
  //expect(value).toBe(0.3);           This won't work because of rounding error
  expect(value).toBeCloseTo(0.3); // This works.
});
```




### describe(name, fn)

[https://jestjs.io/docs/en/api#describename-fn](https://jestjs.io/docs/en/api#describename-fn)

describe(name, fn) creates a block that groups together several related tests. For example, if you have a myBeverage object that is supposed to be delicious but not sour, you could test it with:



```javascript
const myBeverage = {
  delicious: true,
  sour: false,
};

describe('my beverage', () => {
  test('is delicious', () => {
    expect(myBeverage.delicious).toBeTruthy();
  });

  test('is not sour', () => {
    expect(myBeverage.sour).toBeFalsy();
  });
});
```



### toMatchSnapshot

This ensures that a value matches the most recent snapshot. Check out the Snapshot Testing guide for more information.


You can provide an optional propertyMatchers object argument, which has asymmetric matchers as values of a subset of expected properties, if the received value will be an object instance. It is like toMatchObject with flexible criteria for a subset of properties, followed by a snapshot test as exact criteria for the rest of the properties.




























