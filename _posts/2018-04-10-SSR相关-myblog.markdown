---
layout:     post
title:      "SSR相关"
date:       2018-04-10 23:54:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 后端
---

> “Yeah It's on. ”



[https://github.com/yacan8/blog/issues/30](https://github.com/yacan8/blog/issues/30)

与传统 SPA (单页应用程序 (Single-Page Application)) 相比，服务器端渲染 (SSR) 的优势主要在于：

- 更好的 SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。请注意，截至目前，Google 和 Bing 可以很好对同步 JavaScript 应用程序进行索引。在这里，同步是关键。如果你的应用程序初始展示 loading 菊花图，然后通过 Ajax 获取内容，抓取工具并不会等待异步完成后再行抓取页面内容。也就是说，如果 SEO 对你的站点至关重要，而你的页面又是异步获取内容，则你可能需要服务器端渲染(SSR)解决此问题。
- 更快的内容到达时间 (time-to-content)，特别是对于缓慢的网络情况或运行缓慢的设备。无需等待所有的 JavaScript 都完成下载并执行，才显示服务器渲染的标记，所以你的用户将会更快速地看到完整渲染的页面。通常可以产生更好的用户体验，并且对于那些「内容到达时间(time-to-content) 与转化率直接相关」的应用程序而言，服务器端渲染 (SSR) 至关重要。



##  React 



### Suspense in ssr

[New Suspense SSR Architecture in React 18](https://github.com/reactwg/react-18/discussions/37)

支持在ssr下的组件的懒加载







## Vue



### 组件生命周期钩子函数

由于没有动态更新，所有的生命周期钩子函数中，只有 `beforeCreate` 和 `created` 会在服务器端渲染 (SSR) 过程中被调用。这就是说任何其他生命周期钩子函数中的代码（例如 `beforeMount` 或 `mounted`），只会在客户端执行。



此外还需要注意的是，你应该避免在 `beforeCreate` 和 `created` 生命周期时产生全局副作用的代码，例如在其中使用 `setInterval` 设置 timer。在纯客户端 (client-side only) 的代码中，我们可以设置一个 timer，然后在 `beforeDestroy` 或 `destroyed` 生命周期时将其销毁。但是，由于在 SSR 期间并不会调用销毁钩子函数，所以 timer 将永远保留下来。为了避免这种情况，请将副作用代码移动到 `beforeMount` 或 `mounted` 生命周期中。





### 避免状态单例





当编写纯客户端 (client-only) 代码时，我们习惯于每次在新的上下文中对代码进行取值。但是，Node.js 服务器是一个长期运行的进程。当我们的代码进入该进程时，它将进行一次取值并留存在内存中。这意味着如果创建一个单例对象，它将在每个传入的请求之间共享。

如基本示例所示，我们**为每个请求创建一个新的根 Vue 实例**。这与每个用户在自己的浏览器中使用新应用程序的实例类似。如果我们在多个请求之间使用一个共享的实例，很容易导致交叉请求状态污染 (cross-request state pollution)。

因此，我们不应该直接创建一个应用程序实例，而是应该暴露一个可以重复执行的工厂函数，为每个请求创建新的应用程序实例：

```js
// app.js
const Vue = require('vue')

module.exports = function createApp (context) {
  return new Vue({
    data: {
      url: context.url
    },
    template: `<div>访问的 URL 是： {{ url }}</div>`
  })
}
```



>同样的规则也适用于 router、store 和 event bus 实例。你不应该直接从模块导出并将其导入到应用程序中，而是需要在 `createApp` 中创建一个新的实例，并从根 Vue 实例注入。

### 数据预取存储容器 (Data Store)

[数据预取存储容器](https://ssr.vuejs.org/zh/guide/data.html#数据预取存储容器-data-store)









## 补充




### asset-require-hook



[网页链接](https://www.npmjs.com/package/asset-require-hook)

解决SSR中图片路径的问题



### css-modules-require-hook



[css-modules-require-hook](https://www.npmjs.com/package/css-modules-require-hook)

[react中SSR](https://www.jianshu.com/p/47c8e364d0bc?appinstall=1&mType=Group)


解决后端不支持CSS的问题


The require hook compiles CSS Modules in runtime. This is similar to Babel's babel/register.

[See the example](https://github.com/css-modules/css-modules-require-hook/tree/a432b76e1eb46a7bf9ef729c16a96b6ef2295410/demo)


The second one allows you to move options to the separate file cmrh.conf.js. Config file should be located in the same directory where executor is or in its ancestor directories. In that case hook will be attached right after the css-modules-require-hook/preset module will be required. Example:

```
// cmrh.conf.js
module.exports = {
  generateScopedName: '[name]__[local]___[hash:base64:5]',
};

require('css-modules-require-hook/preset');
 
// const styles = require('./icon.css');
```



### Using with babel-node / ES6 Imports

You will need to create a cmrh.conf.js file within the directory as you are importing css-modules-require-hook.

```js
// server.js
import csshook from 'css-modules-require-hook/preset' // import hook before routes
import routes from '/shared/views/routes'
 
// create server, etc

// cmrh.conf.js
module.exports = {
  // Same scope name as in webpack build
  generateScopedName: '[name]__[local]___[hash:base64:5]',
}
```



**需要注意的此处有一小坑，csshook必须放所有组件引入之前**

```js
// 处理css
import csshook from 'css-modules-require-hook/preset';

const express = require("express"); const bodyParser = require("body-parser"); const cookieParser = require("cookie-parser"); const userRoute = require("./userRoute"); const app = express(); const path = require('path'); app.use(cookieParser()); app.use(bodyParser.json());
```



作者：抱紧我_8204
链接：https://www.jianshu.com/p/47c8e364d0bc
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。











