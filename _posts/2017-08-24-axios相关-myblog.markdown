---
layout:     post
title:      "axios相关"
date:       2017-08-24 18:19:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 网络
---

> “Yeah It's on. 






## 正文
[网页链接](https://ykloveyxk.github.io/2017/02/25/axios%E5%85%A8%E6%94%BB%E7%95%A5/)

[github地址](https://github.com/axios/axios)

https://ykloveyxk.github.io/2017/02/25/axios%E5%85%A8%E6%94%BB%E7%95%A5/

### axios 简介
axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端，它本身具有以下特征：
* 从浏览器中创建 XMLHttpRequest
* 从 node.js 发出 http 请求
* 支持 Promise API
* 拦截请求和响应
* 转换请求和响应数据
* 取消请求
* 自动转换JSON数据
* **客户端支持防止 CSRF/XSRF**


执行 GET 请求
```
// 向具有指定ID的用户发出请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
// 也可以通过 params 对象传递参数
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
执行 POST 请求
```
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
执行多个并发请求
```
function getUserAccount() {
  return axios.get('/user/12345');
}
function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}
axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    //两个请求现已完成
  }));
```
### axios API
可以通过将相关配置传递给 axios 来进行请求。
axios(config)
```js
// 发送一个 POST 请求
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```
axios(url[, config])
```js
// 发送一个 GET 请求 (GET请求是默认请求模式)
axios('/user/12345');
```
### 请求方法别名
* axios.request（config）
* axios.get（url [，config]）
* axios.delete（url [，config]）
* axios.head（url [，config]）
* axios.post（url [，data [，config]]）
* axios.put（url [，data [，config]]）
* axios.patch（url [，data [，config]]）

注意
当使用别名方法时，不需要在config中指定url，method和data属性。

### 创建实例
axios.create（[config]）
```
var instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```
### 请求配置
这些是用于发出请求的可用配置选项。 只有url是必需的。 如果未指定方法，请求将默认为GET。

```js
{
  // `url`是将用于请求的服务器URL
  url: '/user',
  // `method`是发出请求时使用的请求方法
  method: 'get', // 默认
  // `baseURL`将被添加到`url`前面，除非`url`是绝对的。
  // 可以方便地为 axios 的实例设置`baseURL`，以便将相对 URL 传递给该实例的方法。
  baseURL: 'https://some-domain.com/api/',
  // `transformRequest`允许在请求数据发送到服务器之前对其进行更改
  // 这只适用于请求方法'PUT'，'POST'和'PATCH'
  // 数组中的最后一个函数必须返回一个字符串，一个 ArrayBuffer或一个 Stream
  transformRequest: [function (data) {
    // 做任何你想要的数据转换
    return data;
  }],
  // `transformResponse`允许在 then / catch之前对响应数据进行更改
  transformResponse: [function (data) {
    // Do whatever you want to transform the data
    return data;
  }],
  // `headers`是要发送的自定义 headers
  headers: {'X-Requested-With': 'XMLHttpRequest'},
  // `params`是要与请求一起发送的URL参数
  // 必须是纯对象或URLSearchParams对象
  params: {
    ID: 12345
  },
  // `paramsSerializer`是一个可选的函数，负责序列化`params`
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },
  // `data`是要作为请求主体发送的数据
  // 仅适用于请求方法“PUT”，“POST”和“PATCH”
  // 当没有设置`transformRequest`时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - Browser only: FormData, File, Blob
  // - Node only: Stream
  data: {
    firstName: 'Fred'
  },
  // `timeout`指定请求超时之前的毫秒数。
  // 如果请求的时间超过'timeout'，请求将被中止。
  timeout: 1000,
  // `withCredentials`指示是否跨站点访问控制请求
  // should be made using credentials
  withCredentials: false, // default
  // `adapter'允许自定义处理请求，这使得测试更容易。
  // 返回一个promise并提供一个有效的响应（参见[response docs]（＃response-api））
  adapter: function (config) {
    /* ... */
  },
  // `auth'表示应该使用 HTTP 基本认证，并提供凭据。
  // 这将设置一个`Authorization'头，覆盖任何现有的`Authorization'自定义头，使用`headers`设置。
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },
  // “responseType”表示服务器将响应的数据类型
  // 包括 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default
  //`xsrfCookieName`是要用作 xsrf 令牌的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default
  // `xsrfHeaderName`是携带xsrf令牌值的http头的名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // default
  // `onUploadProgress`允许处理上传的进度事件
  onUploadProgress: function (progressEvent) {
    // 使用本地 progress 事件做任何你想要做的
  },
  // `onDownloadProgress`允许处理下载的进度事件
  onDownloadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },
  // `maxContentLength`定义允许的http响应内容的最大大小
  maxContentLength: 2000,
  // `validateStatus`定义是否解析或拒绝给定的promise
  // HTTP响应状态码。如果`validateStatus`返回`true`（或被设置为`null` promise将被解析;否则，promise将被
  // 拒绝。
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },
  // `maxRedirects`定义在node.js中要遵循的重定向的最大数量。
  // 如果设置为0，则不会遵循重定向。
  maxRedirects: 5, // 默认
  // `httpAgent`和`httpsAgent`用于定义在node.js中分别执行http和https请求时使用的自定义代理。
  // 允许配置类似`keepAlive`的选项，
  // 默认情况下不启用。
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),
  // 'proxy'定义代理服务器的主机名和端口
  // `auth`表示HTTP Basic auth应该用于连接到代理，并提供credentials。
  // 这将设置一个`Proxy-Authorization` header，覆盖任何使用`headers`设置的现有的`Proxy-Authorization` 自定义 headers。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: : {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },
  // “cancelToken”指定可用于取消请求的取消令牌
  // (see Cancellation section below for details)
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

使用 then 时，您将收到如下响应：

```js
axios.get('/user/12345')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```

### 配置默认值
全局axios默认值
```
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```
自定义实例默认值
```
//在创建实例时设置配置默认值
var instance = axios.create（{
   baseURL：'https://api.example.com'
}）;
 
//在实例创建后改变默认值
instance.defaults.headers.common ['Authorization'] = AUTH_TOKEN;
```

### 拦截器
你可以截取请求或响应在被 then 或者 catch 处理之前
```
//添加请求拦截器
axios.interceptors.request.use（function（config）{
     //在发送请求之前做某事
     return config;
   }，function（error）{
     //请求错误时做些事
     return Promise.reject（error）;
   }）;
 
//添加响应拦截器
axios.interceptors.response.use（function（response）{
     //对响应数据做些事
      return response;
   }，function（error）{
     //请求错误时做些事
     return Promise.reject（error）;
   }）;
```
如果你以后可能需要删除拦截器。
```
var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

### 处理错误
```
axios.get（'/ user / 12345'）
   .catch（function（error）{
     if（error.response）{
       //请求已发出，但服务器使用状态代码进行响应
       //落在2xx的范围之外
       console.log（error.response.data）;
       console.log（error.response.status）;
       console.log（error.response.headers）;
     } else {
       //在设置触发错误的请求时发生了错误
       console.log（'Error'，error.message）;
     }}
     console.log（error.config）;
   }）;
```
您可以使用validateStatus配置选项定义自定义HTTP状态码错误范围。
```
axios.get（'/ user / 12345'，{
   validateStatus：function（status）{
     return status < 500; //仅当状态代码大于或等于500时拒绝
   }}
}）
```

### 客户端支持防止 CSRF
原理:
axios默认在Request Config里已经设置了
```
 //  ``xsrfCookieName`是用作xsrf标记值的cookie的名称
  xsrfCookieName ：' XSRF-TOKEN '，//  默认  
 
  //  ``xsrfHeaderName`是携带xsrf标记值的http标头的名称
  xsrfHeaderName ：' X-XSRF-TOKEN '，//  默认  
```



### 流式处理

当后端返回的 response

content-type: application/octet-stream

可以利用axios 的 onDownloadProgress 处理数据 

```tsx
axios.get(url, {  
    responseType: 'stream', 
    onDownloadProgress: (progressEvent) => {  
        const total = progressEvent.total;  
        const current = progressEvent.loaded;  
        const percentage = Math.round((current / total) * 100);  
        console.log(`已下载 ${current} bytes / ${total} bytes (${percentage}%)`);  

        const { event } = progressEvent
        const xhr = event.target
        const { responseText } = xhr
        // 处理相应内容
    }  
})  
```








## 补充




### 手写一个axios


我们知道手写的一个axios，一个比较难的点就是

axios()

axios.get()



axios同时支持这两种模式，**也就是说axios本身是一个函数**



实现：


```javascript
  class Axios {
    constructor() {
      // 这里是因为类方法不可以枚举 extend中的for...in查找不到
      // 这一点与 ES5 的行为不一致
      this.request = this.request.bind(this)
      this.get = this.get.bind(this)
    }

    get(aaa) {
      console.log(aaa)
    }

    request() {
      console.log("axios request")
    }
  }


  function extend(to, form) {
    for (let key in form) {
      to[key] = form[key]
    }
  }

  function createInstance() {
    const context = new Axios()
    const instance = Axios.prototype.request.bind(context)
    extend(instance, context)
    return instance
  }

  // 最终的axios
  let axios = createInstance()
```





