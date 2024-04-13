---
layout:     post
title:      "单元测试unit-test"
date:       2019-08-22 16:04:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 测试
---

> “Yeah It's on. ”


# 正文



## private

[Can I unit test private methods?](https://roadfiresoftware.com/2018/05/can-i-unit-test-private-methods/)

[https://vuejs.org/guide/scaling-up/testing.html](https://vuejs.org/guide/scaling-up/testing.html)

Don't assert the private state of a component instance or test the private methods of a component

不要测试private相关的东西。例如：class 中的 private methods





## jest

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



#### toEqual 和 toBe 的区别

* toBe just checks that a value is what you expect. It uses === to check strict equality.
* Use .toEqual when you want to check that two objects have the same value. This matcher recursively checks the equality of all fields, rather than checking for object identity—this is also known as “deep equal”. For example, toEqual and toBe behave differently in this test suite, so all the tests pass.




### toBeCloseTo


For floating point equality, use toBeCloseTo instead of toEqual, because you don't want a test to depend on a tiny rounding error.

```javascript
test('adding floating point numbers', () => {
  const value = 0.1 + 0.2;
  //expect(value).toBe(0.3);           This won't work because of rounding error
  expect(value).toBeCloseTo(0.3); // This works.
});
```



### objectContaining 对象包含某些属性

例子：

```tsx
expect(testProps.onRemove).toHaveBeenCalledWith(expect.objectContaining({
     raw: testFile,
     status: 'success',
     name: 'test.png'
}))
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





### toMatchInlineSnapshot

[内联快照](https://jestjs.io/zh-Hans/docs/snapshot-testing)

内联快照和普通快照(`.snap` 文件)表现一致，只是会将快照值自动写会源代码中。 这意味着你可以从自动生成的快照中受益，并且不用切换到额外生成的快照文件中保证值的正确性。

```tsx
it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="https://example.com">Example Site</Link>)
    .toJSON();
  expect(tree).toMatchInlineSnapshot();
});
```

下次运行Jest时，`tree` 会被重新评估，此次的快照会被当作一个参数传递到 `toMatchInlineSnapshot`:

```tsx
it('renders correctly', () => {
  const tree = renderer
    .create(<Link page="https://example.com">Example Site</Link>)
    .toJSON();
  expect(tree).toMatchInlineSnapshot(`
<a
  className="normal"
  href="https://example.com"
  onMouseEnter={[Function]}
  onMouseLeave={[Function]}
>
  Example Site
</a>
`);
});
```














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



### Mock



#### Mock a function

```tsx
function forEach(items, callback) {
  for (let index = 0; index < items.length; index++) {
    callback(items[index]);
  }
}
```

To test this function, we can use a mock function, and inspect the mock's state to ensure the callback is invoked as expected.

```tsx
const mockCallback = jest.fn(x => 42 + x);
forEach([0, 1], mockCallback);

// The mock function is called twice
expect(mockCallback.mock.calls.length).toBe(2);

// The first argument of the first call to the function was 0
expect(mockCallback.mock.calls[0][0]).toBe(0);

// The first argument of the second call to the function was 1
expect(mockCallback.mock.calls[1][0]).toBe(1);

// The return value of the first call to the function was 42
expect(mockCallback.mock.results[0].value).toBe(42);
```



#### Mock Modules

Suppose we have a class that fetches users from our API. The class uses [axios](https://github.com/axios/axios) to call the API then returns the `data` attribute which contains all the users:

```tsx
// users.js
import axios from 'axios';

class Users {
  static all() {
    return axios.get('/users.json').then(resp => resp.data);
  }
}

export default Users;
```

Now, in order to test this method without actually hitting the API (and thus creating slow and fragile tests), we can use the `jest.mock(...)` function to automatically mock the axios module.

Once we mock the module we can provide a `mockResolvedValue` for `.get` that returns the data we want our test to assert against. In effect, we are saying that we want `axios.get('/users.json')` to return a fake response.

```tsx
// users.test.js
import axios from 'axios';
import Users from './users';

jest.mock('axios');

test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);

  // or you could use the following depending on your use case:
  // axios.get.mockImplementation(() => Promise.resolve(resp))

  return Users.all().then(data => expect(data).toEqual(users));
});
```



#### Mock Implementations

Still, there are cases where it's useful to go beyond the ability to specify return values and full-on replace the implementation of a mock function. This can be done with `jest.fn` or the `mockImplementationOnce` method on mock functions.

```tsx
const myMockFn = jest.fn(cb => cb(null, true));

myMockFn((err, val) => console.log(val));
// > true
```

The `mockImplementation` method is useful when you need to define the default implementation of a mock function that is created from another module:

```tsx
// foo.js
module.exports = function () {
  // some implementation;
};

// test.js
jest.mock('../foo'); // this happens automatically with automocking
const foo = require('../foo');

// foo is a mock function
foo.mockImplementation(() => 42);
foo();
// > 42
```









#### Manual Mocks

[https://jestjs.io/docs/en/manual-mocks](https://jestjs.io/docs/en/manual-mocks)



### jest.config

[setupFiles and setupFilesAfterEnv?](https://stackoverflow.com/questions/58080435/when-should-i-use-setupfiles-rather-than-setupfilesafterenv)

**setupFiles** will be executed

> before the test framework is installed in the environment.

**setupFilesAfterEnv** will be executed

> after the test framework has been installed in the environment.







## testing-library

[https://testing-library.com/docs/react-testing-library/example-intro](https://testing-library.com/docs/react-testing-library/example-intro)



在react中，我们使用下面的库进行单元测试

```js
"@testing-library/jest-dom": "^4.2.4"
"@testing-library/react": "^9.3.2"
```





### debug

[https://testing-library.com/docs/react-testing-library/api](https://testing-library.com/docs/react-testing-library/api)



This method is a shortcut for `console.log(prettyDOM(baseElement))`.

```tsx
import React from 'react'
import { render } from '@testing-library/react'

const HelloWorld = () => <h1>Hello World</h1>
const { debug } = render(<HelloWorld />)
debug()
// <div>
//   <h1>Hello World</h1>
// </div>
// you can also pass an element: debug(getByTestId('messages'))
// and you can pass all the same arguments to debug as you can
// to prettyDOM:
// const maxLengthToPrint = 10000
// debug(getByTestId('messages'), maxLengthToPrint, {highlight: false})

```



### `cleanup`



Unmounts React trees that were mounted with [render](https://testing-library.com/docs/react-testing-library/api#render).

> Please note that this is done automatically if the testing framework you're using supports the `afterEach` global (like mocha, Jest, and Jasmine). If not, you will need to do manual cleanups after each test.

For example, if you're using the [ava](https://github.com/avajs/ava) testing framework, then you would need to use the `test.afterEach` hook like so:

```tsx
import { cleanup, render } from '@testing-library/react'
import test from 'ava'

test.afterEach(cleanup)

test('renders into document', () => {
  render(<div />)
  // ...
})

// ... more tests ...
```



### 使用querySelector

```tsx
const wrapper = render(
     <Input  placeholder="sizes" size="lg"></Input>
   )
const testnNode = wrapper.container.querySelector('.viking-input-wrapper')
```







### FAQ

[https://testing-library.com/docs/react-testing-library/faq](https://testing-library.com/docs/react-testing-library/faq)





### 补充



#### 插入css样式

一般我们写单元测试都是只判断className，无需使用css做测试

但是，在某些情况下，为了更好地测试,我们需要覆盖原来的css样式

```tsx
import { render } from '@testing-library/react'


const createStyleFile = () => {
  const cssFile: string = `
    .viking-submenu {
      display: none;
    }
    .viking-submenu.menu-opened {
      display:block;
    }
  `
  const style = document.createElement('style')
  style.type = 'text/css'
  style.innerHTML = cssFile
  return style
}

let wrapper: RenderResult
wrapper = render(generateMenu(testProps))
wrapper.container.append(createStyleFile())  // 样式生效
```





#### mock axios同时提供代码提示

[https://medium.com/wesionary-team/mocking-axios-in-jest-c68933a1a4fb](https://medium.com/wesionary-team/mocking-axios-in-jest-c68933a1a4fb)

```tsx
jest.mock('axios')
// mock axios 之后 有代码提示
const mockedAxios = axios as jest.Mocked<typeof axios>
```



另外一种解决方案：

Now, let’s mock this Axios request in our test. Create a folder named `__mocks__` under your src directory. Since we are mocking axios, create a file named axios.js inside the `__mocks__` folder.It is a convention to write all the mock related files inside this `__mocks__` folder. And since we have named the file axios.js, Jest identifies it automatically that it has to mock the package axios. Basically, we name the mock file same as the package we are intending to mock.



Moving on, we are here mocking the get method of axios. So, add this code snippet in the axios.js file.

```tsx
export default {
   get: jest.fn().mockResolvedValue()
};
```









## testing-library/react

[https://testing-library.com/docs/react-testing-library/api](https://testing-library.com/docs/react-testing-library/api)









#### render

> render函数的返回值是RenderResult

```tsx
function render(
  ui: React.ReactElement<any>,
  options?: {
    /* You won't often use this, expand below for docs on options */
  }
): RenderResult
```



例子：

```tsx
import { render } from '@testing-library/react'
import '@testing-library/jest-dom/extend-expect'

test('renders a message', () => {
  const { container, getByText } = render(<Greeting />)
  expect(getByText('Hello, world!')).toBeInTheDocument()
  expect(container.firstChild).toMatchInlineSnapshot(`
    <h1>Hello, World!</h1>
  `)
})
```



#### container

By default, `React Testing Library` will create a `div` and append that `div` to the `document.body` and this is where your React component will be rendered. If you provide your own HTMLElement `container` via this option, it will not be appended to the `document.body` automatically.



**如果我们要通过className来查找元素，经常使用container**



例子：

```tsx
let wrapper = render(<AutoComplete {...testProps}/>)
wrapper.container.querySelectorAll('.suggestion-item').length
```



#### test input onChange handlers

```ts
import React from 'react'
import { render, fireEvent } from '@testing-library/react'

test('change values via the fireEvent.change method', () => {
  const handleChange = jest.fn()
  const { container } = render(<input type="text" onChange={handleChange} />)
  const input = container.firstChild
  fireEvent.change(input, { target: { value: 'a' } })
  expect(handleChange).toHaveBeenCalledTimes(1)
  expect(input.value).toBe('a')
})

test('select drop-downs must use the fireEvent.change', () => {
  const handleChange = jest.fn()
  const { container } = render(
    <select onChange={handleChange}>
      <option value="1">1</option>
      <option value="2">2</option>
    </select>
  )
  const select = container.firstChild
  const option1 = container.getElementsByTagName('option').item(0)
  const option2 = container.getElementsByTagName('option').item(1)

  fireEvent.change(select, { target: { value: '2' } })

  expect(handleChange).toHaveBeenCalledTimes(1)
  expect(option1.selected).toBe(false)
  expect(option2.selected).toBe(true)
})

test('checkboxes (and radios) must use fireEvent.click', () => {
  const handleChange = jest.fn()
  const { container } = render(
    <input type="checkbox" onChange={handleChange} />
  )
  const checkbox = container.firstChild
  fireEvent.click(checkbox)
  expect(handleChange).toHaveBeenCalledTimes(1)
  expect(checkbox.checked).toBe(true)
})
```



#### unmount

This will cause the rendered component to be unmounted. **This is useful for testing what happens when your component is removed from the page** (like testing that you don't leave event handlers hanging around causing memory leaks).

```tsx
import { render } from '@testing-library/react'

const { container, unmount } = render(<Login />)
unmount()
// your component has been unmounted and now: container.innerHTML === ''
```





#### rerender

It'd probably be better if you test the component that's doing the prop updating to ensure that the props are being updated correctly (see [the Guiding Principles section](https://testing-library.com/docs/guiding-principles)). That said, if you'd prefer to update the props of a rendered component in your test, this function can be used to update props of the rendered component.

```tsx
import { render } from '@testing-library/react'

const { rerender } = render(<NumberDisplay number={1} />)

// re-render the same component with different props
rerender(<NumberDisplay number={2} />)
```



#### createEvent

```tsx
import { fireEvent,createEvent } from '@testing-library/react'

const mockDropEvent = createEvent.drop(uploadArea)
Object.defineProperty(mockDropEvent,'dataTransfer',{
      value:{
        files: [testFile]
      }
  })
fireEvent(uploadArea, mockDropEvent)
```





### 问题



#### 如何使用querySelector

```tsx
let wrapper:RenderResult
wrapper = render(
   <Upload {...testProps}>Click to upload</Upload>
 )
fileInput = wrapper.container.querySelector('.viking-file-input')
```

如果我们想通过className查询元素，可以利用wrapper.container调用querySelector







## vitest

[https://vitest.dev/](https://vitest.dev/)



### demo





### 基础

```ts
toBeCloseTo(8.135869643784943, 3)
```



### vscode中Debugger

[https://cn.vitest.dev/guide/debugging.html#vscode](https://cn.vitest.dev/guide/debugging.html#vscode)

利用JavaScript Debug Terminal



### 快照

[https://cn.vitest.dev/guide/snapshot.html](https://cn.vitest.dev/guide/snapshot.html)

toMatchInlineSnapshot


### 模拟对象

[https://cn.vitest.dev/guide/mocking.html](https://cn.vitest.dev/guide/mocking.html)



### 模拟请求

[https://cn.vitest.dev/guide/mocking#%E8%AF%B7%E6%B1%82](https://cn.vitest.dev/guide/mocking#%E8%AF%B7%E6%B1%82)

因为 Vitest 运行在 Node 环境中，所以模拟网络请求是一件非常棘手的事情；由于没有办法使用 Web API，因此我们需要一些可以为我们模拟网络行为的包。推荐使用 [Mock Service Worker](https://mswjs.io/) 来进行这个操作。它可以模拟 `REST` 和 `GraphQL` 网络请求，并且与框架无关。



### 测试覆盖率

[https://cn.vitest.dev/guide/coverage.html](https://cn.vitest.dev/guide/coverage.html)

Vitest 通过 [`c8`](https://github.com/bcoe/c8) 支持本机代码覆盖率。同时也支持 [`istanbul`](https://istanbul.js.org/)。

[c8 vs istanbul vs nyc](https://npmtrends.com/c8-vs-istanbul-vs-nyc)

[看懂「测试覆盖率报告」 ](https://github.com/JChehe/blog/issues/49)

**注意需要版本：  "vitest": "0.29.8"**



#### c8

[https://github.com/bcoe/c8#readme](https://github.com/bcoe/c8#readme)

c8 - native V8 code-coverage

Code-coverage using [Node.js' built in functionality](https://nodejs.org/dist/latest-v10.x/docs/api/cli.html#cli_node_v8_coverage_dir) that's compatible with [Istanbul's reporters](https://istanbul.js.org/docs/advanced/alternative-reporters/).



#### **[istanbul](https://github.com/gotwarlost/istanbul)**





### config



#### environment

[https://cn.vitest.dev/config/#environment](https://cn.vitest.dev/config/#environment)



#### setupFiles

> TIP: 重要

[https://cn.vitest.dev/config/#setupfiles](https://cn.vitest.dev/config/#setupfiles)

setup 文件的路径。它们将运行在每个测试文件之前。



### vi



#### advanceTimersByTime

vi的advanceTimersByTime方法用于模拟时间的推进。它会将时间前进指定的毫秒数，并且触发任何在此期间到期的计时器或计划任务。

对于使用vi进行单元测试的代码，如果存在需要模拟时间流逝并执行计时器或计划任务的情况，可以使用advanceTimersByTime方法。



## ts-jest

[https://www.npmjs.com/package/ts-jest](https://www.npmjs.com/package/ts-jest)

A Jest transformer with source map support that lets you use Jest to test projects written in TypeScript.



## rc-test

[https://www.npmjs.com/package/rc-test](https://www.npmjs.com/package/rc-test)

test for react component





## 补充



### msw 

[https://mswjs.io/docs/getting-started](https://mswjs.io/docs/getting-started)

Mock Service Worker (MSW)是一个用于浏览器和Node.js的API模拟库。使用MSW，您可以拦截发出的请求，观察它们，并使用模拟响应对它们进行响应。

例子：

https://github.com/vitest-dev/vitest/tree/main/examples/react-testing-lib-msw



### happy-dom 和 jsdom

happy-dom 和 jsdom 都是 JavaScript 库，用于在服务器上模拟浏览器的 DOM 环境。它们的主要区别在于它们的设计理念和用法：

1. 设计理念：happy-dom 被设计为一个轻量级、快速和稳定的 DOM 实现，它专注于提供简单的 DOM 操作和查询功能，以及对 CSSOM 的一些支持。而 jsdom 更加强大和复杂，它提供了一整套完整的浏览器环境，包括 DOM、CSSOM、XMLHttpRequest、WebSocket 等，可以在服务器端模拟完整的浏览器环境。
2. 用法：happy-dom 提供了一个与浏览器兼容的 API，可以通过 `document.createElement()`、`document.querySelector()` 等方式对 DOM 进行操作，但它不支持像浏览器那样的事件处理和 CSS 样式计算。而 jsdom 提供了一个更加完整的浏览器环境，可以通过 `window.document.createElement()`、`window.document.querySelector()` 等方式对 DOM 进行操作，并支持完整的事件处理和 CSS 样式计算。

**总体而言，如果只需要进行简单的 DOM 操作和查询，或者对性能要求较高，则可以选择 happy-dom。如果需要模拟完整的浏览器环境，包括事件处理和 CSS 样式计算等功能，则可以选择 jsdom。**



# 踩坑



## jest的testEnvironment

Default: `"node"`

The test environment that will be used for testing. The default environment in Jest is a Node.js environment. If you are building a web app, you can use a browser-like environment through jsdom instead.

用于测试的测试环境。Jest中的默认环境是Node.js环境。如果你正在构建一个web应用程序，你可以通过jsdom来使用一个类似于浏览器的环境。



一般我们是写web应用程序的单元测试所以testEnvironment要设置为jsdom







## jest和jest-environment-jsdom版本对应

当我们使用jsdom作为testEnvironment

jest版本和jest-environment-jsdom的版本需要一一对应

例如

```js
  "jest": "24.9.0",
  "testEnvironment": "jest-environment-jsdom-fourteen",
```

例如

```js
"jest": "^27.0.6",
"testEnvironment": 'jest-environment-jsdom'   // 27.0.6
```

