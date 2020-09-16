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








### Testing Asynchronous Code


[https://jestjs.io/docs/zh-Hans/asynchronous](https://jestjs.io/docs/zh-Hans/asynchronous)


在JavaScript中执行异步代码是很常见的。 当你有以异步方式运行的代码时，Jest 需要知道当前它测试的代码是否已完成，然后它可以转移到另一个测试。 Jest有若干方法处理这种情况。



----



最常见的异步模式是回调函数。

例如，假设您有一个 fetchData(callback) 函数，获取一些数据并在完成时调用 callback(data)。 You want to test that this returned data is the string 'peanut butter'.

默认情况下，Jest 测试一旦执行到末尾就会完成。 那意味着该测试将不会按预期工作：

```javascript
// 不要这样做！
test('the data is peanut butter', () => {
  function callback(data) {
    expect(data).toBe('peanut butter');
  }

  fetchData(callback);
});
```
问题在于一旦fetchData执行结束，此测试就在没有调用回调函数前结束。

还有另一种形式的 test，解决此问题。 使用单个参数调用 done，而不是将测试放在一个空参数的函数。 Jest会等done回调函数执行结束后，结束测试。
```javascript
test('the data is peanut butter', done => {
  function callback(data) {
    expect(data).toBe('peanut butter');
    done();
  }

  fetchData(callback);
});
```
如果 done()永远不会调用，这个测试将失败，这也是你所希望发生的。




#### Promises


If your code uses promises, there is a more straightforward way to handle asynchronous tests. Return a promise from your test, and Jest will wait for that promise to resolve. 


举个例子，如果 fetchData 不使用回调函数，而是返回一个 Promise，其解析值为字符串 'peanut butter' 我们可以这样测试：


```javascript
test('the data is peanut butter', () => {
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  });
});
```



**一定不要忘记把 promise 作为返回值**⸺如果你忘了 return 语句的话，在 fetchData 返回的这个 promise 被 resolve、then() 有机会执行之前，测试就已经被视为已经完成了。



#### .resolves / .rejects

您也可以在 expect 语句中使用 .resolves 匹配器，Jest 将等待此 Promise 解决


test('the data is peanut butter', () => {
  return expect(fetchData()).resolves.toBe('peanut butter');
});


**一定不要忘记把整个断言作为返回值返**回⸺如果你忘了return语句的话，在 fetchData 返回的这个 promise 变更为 resolved 状态、then() 有机会执行之前，测试就已经被视为已经完成了。



#### Async/Await

To write an async test, use the async keyword in front of the function passed to test


```javascript
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});
```


You can combine async and await with .resolves or .rejects.


```javascript

test('the data is peanut butter', async () => {
  await expect(fetchData()).resolves.toBe('peanut butter');
});
```


### 

### Manual Mocks

[https://jestjs.io/docs/en/manual-mocks](https://jestjs.io/docs/en/manual-mocks)



































