---
layout:     post
title:      "vue-router相关"
date:       2017-08-30 22:15:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”


# 正文


## 基础



### 模式

hash模式：

* 可以改变URL，但不会触发页面重新加载（hash的改变会记录在window.hisotry中）**因此并不算是一次http请求，所以这种模式不利于SEO优化**
* 只能修改#后面的部分，因此只能跳转与当前URL同文档的URL
* 只能通过字符串改变URL
* 通过window.onhashchange监听hash的改变，借此实现无刷新跳转的功能。



history模式：

* **history 模式改变 url 的方式会导致浏览器向服务器发送请求 （我们需要在服务器端做处理：如果匹配不到任何静态资源，则应该始终返回同一个 html 页面）**
* 新的URL可以是与当前URL同源的任意 URL，也可以与当前URL一样，但是这样会把重复的一次操作记录到栈中
* 通过参数stateObject可以添加任意类型的数据到记录中
* 可额外设置title属性供后续使用
* **通过pushState、replaceState实现无刷新跳转的功能。**  它们分别可以添加和修改历史记录条目



**回车刷新： hash 可以加载到hash值对应页面 ； history一般就是404掉了**



### 存在问题

#### 404

当应用通过vue-router跳转到某个页面后，因为此时是前端路由控制页面跳转，虽然url改变，但是页面只是内容改变，并没有重新请求，所以这套流程没有任何问题。但是，如果在当前的页面刷新一下，此时会重新发起请求，如果nginx没有匹配到当前url，就会出现404的页面。



那为什么hash模式不会出现这个问题呢

hash虽然可以改变URL，但不会被包括在HTTP请求中。它被用来指导浏览器动作，并不影响服务器端，因此，改变hash并没有改变url，所以页面路径还是之前的路径，nginx不会拦截。 因此，切记在使用history模式时，需要服务端允许地址可访问，否则就会出现404的尴尬场景。



解决思路：

修改服务端的配置，在路径匹配不上的情况下，将其指向index.html文件，具体的路由匹配由vue-router接管。



nginx

```bash
location / {
  try_files $uri $uri/ /index.html;
}
```



#### hash模式实现锚点

js动态获取hash值，然后再根据hash值获得dom对象。最后，用js进行平缓过渡。



### popstate

[https://developer.mozilla.org/zh-CN/docs/Web/API/Window/popstate_event](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/popstate_event)



需要注意的是调用`history.pushState()`或`history.replaceState()`不会触发`popstate`事件。只有在做出浏览器动作时，才会触发该事件，如用户点击浏览器的回退按钮（或者在Javascript代码中调用`history.back()`或者`history.forward()`方法）



### 路由组件(`RouteComponent`)

 [https://juejin.im/post/6880529850159874062?utm_source=gold_browser_extension](https://juejin.im/post/6880529850159874062?utm_source=gold_browser_extension)



当路由成功匹配时，就需要在`router-view`渲染一个组件，这个需要被渲染的组件就是`路由组件`

`RouteConfig`中`component、components`中定义的`vue组件`就是路由组件



路由组件的特殊性

- 拥有**只在路由组件中生效**的守卫(`beforeRouteEnter 、beforeRouteUpdate、beforeRouteLeave`)
- 你是否跟我一样，曾经在组件中调用`beforeRouteEnter`发现没有生效，那是因为这个守卫只能在`路由组件`中被调用，在所有非路由组件中都不会被调用，包括路由组件的后代组件；你如果想在路由组件中实现`beforeRouteEnter`类似守卫监听效果，可以通过`watch $route`来手动判断





# 补充



## 多个路由地址绑定一个组件出问题

[网页链接](http://blog.csdn.net/fungleo/article/details/54140095)

[在vue-admin中的解决方案](https://panjiachen.github.io/vue-element-admin-site/zh/guide/essentials/router-and-nav.html#%E7%82%B9%E5%87%BB%E4%BE%A7%E8%BE%B9%E6%A0%8F-%E5%88%B7%E6%96%B0%E5%BD%93%E5%89%8D%E8%B7%AF%E7%94%B1)



多个路由地址绑定一个组件造成 created 不执行的解决方法

### 问题所在
由于路由没有发生变化，因此，切换组件，只有在第一次进入的时候会执行created。

### 解决方案

```
created () {
  console.log(this.getStatus(this.$route.path))
},
methods: {
  getStatus (urlStr) {
    var urlStrArr = urlStr.split('/')
    return urlStrArr[urlStrArr.length - 1]
  }
},
watch: {
  '$route' (to, from) {
    console.log(this.$route.path)
    // 观测到router变化 在这里做想要的操作
  }
}
```

### 另一种解决办法
方法也很简单，通过不断改变 url 的 query 来触发 view 的变化。我们监听侧边栏每个 link 的 click 事件，每次点击都给 router push 一个不一样的 query 来确保会重新刷新 view。
```javascript
clickLink(path) {
  this.$router.push({
    path,
    query: {
      t: +new Date() //保证每次点击路由的query项都是不一样的，确保会重新刷新view
    }
  })
}
```



不要忘了在 router-view 上加上一个唯一的 key，来保证路由切换时都会重新渲染触发钩子了。这样简单的多了。

```
<router-view :key="key"></router-view>

computed: {
  key() {
    // 或者 :key="route.fullPath" 只要保证key唯一就可以了
    return this.$route.name !== undefined? this.$route.name + +new Date(): this.$route + +new Date()
  }
 }
```


**但这也有一个弊端就是 url 后面有一个很难看的 query 后缀如 xxx.com/article/list?t=1496832345025**



# 总结

看文档要仔细 = = 



### 响应路由参数的变化
提醒一下，当使用路由参数时，例如从 /user/foo 导航到 user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。

复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch（监测变化） $route 对象：
```
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```


组件内的钩子
以在路由组件内直接定义以下路由导航钩子：
* beforeRouteEnter
* beforeRouteUpdate (2.2 新增)
* beforeRouteLeave

```
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当钩子执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```
beforeRouteEnter 钩子 不能 访问 this，因为钩子在导航确认前被调用,因此即将登场的新组件还没被创建。

不过，你可以通过传一个回调给 next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
你可以 在 beforeRouteLeave 中直接访问 this。这个 leave 钩子通常用来禁止用户在还未保存修改前突然离开。可以通过 next(false) 来取消导航。


https://router.vuejs.org/zh-cn/advanced/navigation-guards.html



