---
layout:     post
title:      "Vue相关基础"
date:       2017-07-24 10:44:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”


## 正文


### 生命周期
[网页链接](https://cn.vuejs.org/v2/guide/)

* Vue.js（读音 /vjuː/，类似于 view） 是一套构建用户界面的渐进式框架。与其他重量级框架不同的是，Vue 采用自底向上增量开发的设计。
* Vue 的核心库只关注视图层，它不仅易于上手，还便于与第三方库或既有项目整合。
* 另一方面，当与单文件组件和 Vue 生态系统支持的库结合使用时，Vue 也完全能够为复杂的单页应用程序提供驱动。

```
var vm = new Vue({
  // 选项
})
```

虽然没有完全遵循 MVVM 模式， Vue 的设计无疑受到了它的启发。因此在文档中经常会使用 vm (ViewModel 的简称) 这个变量名表示 Vue 实例。

---



Vue实例有一个完整的生命周期，也就是从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、卸载等一系列过程，我们称这是Vue的生命周期。通俗说就是Vue实例从创建到销毁的过程，就是生命周期。




#### 注意

不要在实例属性或者回调函数中（如 vm.$watch('a', newVal => this.myMethod())）使用箭头函数。因为箭头函数绑定父级上下文，所以 this 不会像预想的一样是 Vue 实例，而是 this.myMethod 未被定义。





### slot

[slot高级用法 作用域插槽](https://cn.vuejs.org/v2/guide/components-slots.html#作用域插槽)



Vue2.6新增 具名插槽的缩写 v-slot:替换为# 

[https://cn.vuejs.org/v2/guide/components-slots.html#%E5%85%B7%E5%90%8D%E6%8F%92%E6%A7%BD%E7%9A%84%E7%BC%A9%E5%86%99](https://cn.vuejs.org/v2/guide/components-slots.html#具名插槽的缩写)





**插槽 prop 允许我们将插槽转换为可复用的模板，这些模板可以基于输入的 prop 渲染出不同的内容。**这在设计封装数据逻辑同时允许父级组件自定义部分布局的可复用组件时是最有用的。

### nextTick

[网页链接](https://github.com/answershuto/learnVue/blob/master/docs/Vue.js%E5%BC%82%E6%AD%A5%E6%9B%B4%E6%96%B0DOM%E7%AD%96%E7%95%A5%E5%8F%8AnextTick.MarkDown)

[https://cn.vuejs.org/v2/api/#vm-nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick)



#### 异步更新视图
来看一下下面这一段代码
```html
<template>
  <div>
    <div>{{test}}</div>
  </div>
</template>
export default {
    data () {
        return {
            test: 0
        };
    },
    mounted () {
      for(let i = 0; i < 1000; i++) {
        this.test++;
      }
    }
}
```

现在有这样的一种情况，mounted的时候test的值会被++循环执行1000次。

每次++时，都会根据响应式触发setter->Dep->Watcher->update->patch。 如果这时候没有异步更新视图，那么每次`++`都会直接操作DOM更新视图，这是非常消耗性能的。 


所以Vue.js实现了一个queue队列，在下一个tick的时候会统一执行queue中Watcher的run。同时，拥有相同id的Watcher不会被重复加入到该queue中去，所以不会执行1000次Watcher的run。最终更新视图只会直接将test对应的DOM的0变成1000。 保证更新视图操作DOM的动作是在当前栈执行完以后下一个tick的时候调用，大大优化了性能。



#### 访问真实DOM节点更新后的数据
所以我们需要在修改data中的数据后访问真实的DOM节点更新后的数据，只需要这样
```html
<template>
  <div>
    <div ref="test">{{test}}</div>
    <button @click="handleClick">tet</button>
  </div>
</template>
export default {
    data () {
        return {
            test: 'begin'
        };
    },
    methods () {
        handleClick () {
            this.test = 'end';
            this.$nextTick(() => {
                console.log(this.$refs.test.innerText);//打印"end"
            });
            console.log(this.$refs.test.innerText);//打印“begin”
        }
    }
}
```



使用Vue.js的global API的$nextTick方法，即可在回调中获取已经更新好的DOM实例了。



参数：
{Function} [callback]

用法：
**将回调延迟到下次 DOM 更新循环之后执行**。在修改数据之后立即使用它，然后等待 DOM 更新。它跟全局方法 Vue.nextTick 一样，不同的是回调的 this 自动绑定到调用它的实例上。






>2.1.0 起新增：如果没有提供回调且在支持 Promise 的环境中，则返回一个 Promise。请注意 Vue 不自带 Promise 的 polyfill，所以如果你的目标浏览器不是原生支持 Promise (IE：你们都看我干嘛)，你得自行 polyfill。




#### 什么时候需要用

**在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的DOM结构的时候，这个操作都应该放进Vue.nextTick()的回调函数中。**



原因是，Vue是异步执行dom更新的，一旦观察到数据变化，Vue就会开启一个队列，然后把在同一个事件循环 (event loop) 当中观察到数据变化的 watcher 推送进这个队列。如果这个watcher被触发多次，只会被推送到队列一次。这种缓冲行为可以有效的去掉重复数据造成的不必要的计算和DOm操作。而在下一个事件循环时，Vue会清空队列，并进行必要的DOM更新。

当你设置 vm.someData = 'new value'，DOM 并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新时才会进行必要的DOM更新。如果此时你想要根据更新的 DOM 状态去做某些事情，就会出现问题。。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback) 。这样回调函数在 DOM 更新完成后就会调用。







### mixins



[网页链接](https://cn.vuejs.org/v2/api/#mixins)

类型：Array`<Object>`

#### 详细：

mixins 选项接受一个混合对象的数组。这些混合实例对象可以像正常的实例对象一样包含选项，他们将在 Vue.extend() 里最终选择使用相同的选项合并逻辑合并。


#### 选项合并

当组件和混合对象含有同名选项时，这些选项将以恰当的方式混合。比如，同名钩子函数将混合为一个数组，因此都将被调用。另外，混合对象的钩子将在组件自身钩子 **之前**调用:

```javascript
var mixin = {
  created: function () {
    console.log('混合对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})

// => "混合对象的钩子被调用"
// => "组件钩子被调用"
```

值为对象的选项，例如 methods, components 和 directives，将被混合为同一个对象。**两个对象键名冲突时，取组件对象的键值对。**

```javascript
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```


>注意：Vue.extend() 也使用同样的策略进行合并。







### keep-alive




[网页链接](https://cn.vuejs.org/v2/api/#keep-alive)


[keep-alive深入学习](https://github.com/answershuto/learnVue/blob/master/docs/%E8%81%8A%E8%81%8Akeep-alive%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E5%8F%8A%E5%85%B6%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86.MarkDown)

如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令

#### Props:
* include - 字符串或正则表达式。只有匹配的组件会被缓存。
* exclude - 字符串或正则表达式。任何匹配的组件都不会被缓存。

#### 用法:

`<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

当组件在 `<keep-alive>` 内被切换，它的 **activated 和 deactivated** 这两个生命周期钩子函数将会被对应执行。

>在 2.2.0 及其更高版本中，activated 和 deactivated 将会在 `<keep-alive>` 树内的所有嵌套组件中触发。


主要用于保留组件状态或避免重新渲染。

```html
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>

<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>

<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```

注意，`<keep-alive>` 是用在其一个直属的子组件被开关的情形。如果你在其中有 v-for 则不会工作。如果有上述的多个条件性的子元素，`<keep-alive>` 要求同时只有一个子元素被渲染。


#### include and exclude


include 和 exclude 属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示：
```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>

<!-- 正则表达式 (使用 `v-bind`) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>

<!-- 数组 (使用 `v-bind`) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```

匹配首先检查组件自身的 name 选项，如果 name 选项不可用，则匹配它的局部注册名称 (父组件 components 选项的键值)。匿名组件不能被匹配。


>`<keep-alive>` 不会在函数式组件中正常工作，因为它们没有缓存实例。




### filters



[网页链接](https://cn.vuejs.org/v2/guide/filters.html#ad)

Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：**双花括号插值和 v-bind 表达式** (后者从 2.1.0+ 开始支持)。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示：

```html
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```
你可以在一个组件的选项中定义本地的过滤器：

```javascript
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

或者全局定义过滤器：
```javascript
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})
```

过滤器函数总接收表达式的值 (之前的操作链的结果) 作为第一个参数。在上述例子中，capitalize 过滤器函数将会收到 message 的值作为第一个参数。

过滤器可以串联：
`{{ message | filterA | filterB }}`

在这个例子中，filterA 被定义为接收单个参数的过滤器函数，表达式 message 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 filterB，将 filterA 的结果传递到 filterB 中。

----------


过滤器是 JavaScript 函数，因此可以接收参数：
`{{ message | filterA('arg1', arg2) }}`

这里，filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数。





[vue filter 传入两个参数，结果参数位置互换，为什么会这样？](https://segmentfault.com/q/1010000010062982)



错误的写法：



```js
<p class="time-counter">{{(5,12) | formatTime(5,12)}}</p>

相当于是执行了 formatTime((5,12),5,12) 而 (5,12) 是一个逗号表达式，结果为12，因此最终执行的是 formatTime(12, 5, 12)，看起来就像反过来了
```





----



应该这样：

```js
formatTime ([minute,second]){
    if(minute<10){
        minute = '0' + minute;
    }
    if(second<10){
        second = '0' + second;
    }
    return minute + '分' + second + '秒';
}
```

调用时

```js
{{[5, 12] | formatTime}}
```




### render:h => h(App)
[网页链接](http://www.cnblogs.com/whkl-m/p/6970859.html)
```javascript
new Vue({

  router,
  store,
  //components: { App }  vue1.0的写法
  render: h => h(App)    vue2.0的写法
}).$mount('#app')
```
ender函数是渲染一个视图，然后提供给el挂载，如果没有render那页面什么都不会出来

vue.2.0的渲染过程：

1. 首先需要了解这是 es 6 的语法，表示 Vue 实例选项对象的 render 方法作为一个函数，接受传入的参数 h 函数，返回 h(App) 的函数调用结果。
2. 其次，Vue 在创建 Vue 实例时，通过调用 render 方法来渲染实例的 DOM 树。
3. 最后，Vue 在调用 render 方法时，会传入一个 createElement 函数作为参数，也就是这里的 h 的实参是 createElement 函数，然后 createElement 会以 APP 为参数进行调用，关于 createElement 函数的参数说明参见：Element-Arguments

结合一下官方文档的代码便可以很清晰的了解Vue2.0 render:h => h(App)的渲染过程。
```javascript
 render: function (createElement) {
     return createElement(
       'h' + this.level,   // tag name 标签名称
       this.$slots.default // 子组件中的阵列
     )
   }
```


### v-once
[网页链接](https://cn.vuejs.org/v2/api/#v-once)


不需要表达式

详细：
只渲染元素和组件一次。随后的重新渲染,元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

```html
<!-- 单个元素 -->
<span v-once>This will never change: {{msg}}</span>
<!-- 有子元素 -->
<div v-once>
  <h1>comment</h1>
  <p>{{msg}}</p>
</div>
<!-- 组件 -->
<my-component v-once :comment="msg"></my-component>
<!-- v-for 指令-->
<ul>
  <li v-for="i in list" v-once>{{i}}</li>
</ul>
```




### router-view


[网页链接](https://router.vuejs.org/zh-cn/essentials/named-views.html)

有时候想同时（同级）展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar（侧导航） 和 main（主内容） 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置（带上 s）：
```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```


**要注意，以 / 开头的嵌套路径会被当作根路径。 这让你充分的使用嵌套组件而无须设置嵌套的路径。**




### Vue嵌套路由


[网页链接](https://router.vuejs.org/zh-cn/essentials/nested-routes.html)

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如：

```javascript
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

借助 vue-router，使用嵌套路由配置，就可以很简单地表达这种关系。

接着上节创建的 app：
```javascript
<div id="app">
  <router-view></router-view>
</div>
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```
这里的 `<router-view>` 是最顶层的出口，渲染最高级路由匹配到的组件。同样地，一个被渲染组件同样可以包含自己的嵌套 `<router-view>`。例如，在 User 组件的模板添加一个 `<router-view>`：

```javascript
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```
要在嵌套的出口中渲染组件，需要在 VueRouter 的参数中使用 children 配置：

```javascript
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

**要注意，以 / 开头的嵌套路径会被当作根路径。这让你充分的使用嵌套组件而无须设置嵌套的路径。**




### 异步组件


[vue异步组件](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#%E5%BC%82%E6%AD%A5%E7%BB%84%E4%BB%B6)


最近看到一篇blog，里面讲述了关于vue的异步组件的应用，这部分平时用的少，所以记录一下。

[https://github.com/DDFE/DDFE-blog/issues/13](https://github.com/DDFE/DDFE-blog/issues/13)



-------------------




在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。为了简化，Vue 允许你以一个工厂函数的方式定义你的组件，这个工厂函数会异步解析你的组件定义。**Vue 只有在这个组件需要被渲染的时候才会触发该工厂函数，且会把结果缓存起来供未来重渲染。**


例如：


```javascript
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // 向 `resolve` 回调传递组件定义
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```




如你所见，这个工厂函数会收到一个 resolve 回调，这个回调函数会在你从服务器得到组件定义的时候被调用。你也可以调用 reject(reason) 来表示加载失败。这里的 setTimeout 是为了演示用的，如何获取组件取决于你自己。




一个推荐的做法是将异步组件和 webpack 的 code-splitting 功能一起配合使用：



```javascript
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 `require` 语法将会告诉 webpack
  // 自动将你的构建代码切割成多个包，这些包
  // 会通过 Ajax 请求加载
  require(['./my-async-component'], resolve)
})
```


你也可以在工厂函数中返回一个 Promise，所以把 webpack  和 ES2015 语法加在一起，我们可以写成这样：


```javascript
Vue.component(
  'async-webpack-example',
  // 这个 `import` 函数会返回一个 `Promise` 对象。
  () => import('./my-async-component')
)
```




当使用局部注册的时候，你也可以直接提供一个返回 Promise 的函数：


```javascript
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```


处理加载状态






这里的异步组件工厂函数也可以返回一个如下格式的对象：
```javascript
const AsyncComponent = () => ({
  // 需要加载的组件 (应该是一个 `Promise` 对象)
  component: import('./MyComponent.vue'),
  // 异步组件加载时使用的组件
  loading: LoadingComponent,
  // 加载失败时使用的组件
  error: ErrorComponent,
  // 展示加载时组件的延时时间。默认值是 200 (毫秒)
  delay: 200,
  // 如果提供了超时时间且组件加载也超时了，
  // 则使用加载失败时使用的组件。默认值是：`Infinity`
  timeout: 3000
})
```


>注意如果你希望在 Vue Router 的路由组件中使用上述语法的话，你必须使用 Vue Router 2.4.0+ 版本。



###  vue-router打开新窗口



 `<router-link>`  实现



```html
<router-link target="_blank" :to="{path:'/home',query:{id:'1'}}">新页面打开home页</router-link>
```





---



编程式导航



有些时候需要在单击事件或者在函数中实现页面跳转，那么可以借助router的示例方法，通过编写代码实现。我们常用的是 $router.push 和 $router.go 但是vue2.0以后，这种方式就不支持新窗口打开的属性了，这个时候就需要使用this.$router.resolve



```js
seeShare(){
     let routeUrl = this.$router.resolve({
          path: "/share",
          query: {id:96}
     });
     window.open(routeUrl .href, '_blank');
}
```









### vue-router query和params区别



[网页链接](https://segmentfault.com/a/1190000012735168)

#### query方式传参和接收参数

```
传参: 
this.$router.push({
        path:'/xxx'
        query:{
          id:id
        }
      })
  
接收参数:
this.$route.query.id
```


**注意:传参是this.$router,接收参数是this.$route,这里千万要看清了！！！**



#### this.$router 和this.$route有何区别？


在控制台打印两者可以很明显的看出两者的一些区别


1. $router为VueRouter实例，想要导航到不同URL，则使用$router.push方法
2. $route为当前router跳转对象，里面可以获取name、path、query、params等


#### params方式传参和接收参数

```
传参: 
this.$router.push({
        name:'xxx'
        params:{
          id:id
        }
      })
  
接收参数:
this.$route.params.id
```



**注意:params传参，push里面只能是 name:'xxxx',不能是path:'/xxx',因为params只能用name来引入路由，如果这里写成了path，接收参数页面会是undefined！！！**

----------



另外，二者还有点区别，直白的来说query相当于get请求，页面跳转的时候，可以在地址栏看到请求参数，而params相当于post请求，参数不会再地址栏中显示



**还有一点，params数据刷新会消失，query则不会**



### 自定义全局组件



[网页链接](https://www.cnblogs.com/yesyes/p/6658611.html)

首先，在main.js里
```javascript
import Vue from 'vue'
import App from './App.vue'

// 引入自定义组件。index.js是组件的默认入口
import Loading from '../components/loading/index'
Vue.use(Loading);

Vue.use(ElementUi);
new Vue({
  el: '#app',
  render: h => h(App)
})
```


然后在Loading.vue里面定义自己的组件模板
```
<!-- 这里和普通组件的书写一样 -->
<template>
    <div class="loading">
        loading...
    </div>
</template>
```


在index.js文件里面添加install方法
```
import MyLoading from './Loading.vue'
// 这里是重点
const Loading = {
    install: function(Vue){
        Vue.component('Loading',MyLoading)
    }
}

// 导出组件
export default Loading
```


这样就可以在仍何地方使用Loading组件了




### Vue.extend( options )

[https://cn.vuejs.org/v2/api/#Vue-extend](https://cn.vuejs.org/v2/api/#Vue-extend)


最近再看vue源码，在globalApi中发现Vue.extend

使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。


在 vue 项目中，我们有了初始化的根实例后，所有页面基本上都是通过 router 来管理，组件也是通过 import 来进行局部注册，所以组件的创建我们不需要去关注，相比 extend 要更省心一点点。但是这样做会有几个缺点：

* 组件模板都是事先定义好的，如果我要从接口动态渲染组件怎么办？
* 所有内容都是在 #app 下渲染，注册组件都是在当前位置渲染。如果我要实现一个类似于 window.alert() 提示组件要求像调用 JS 函数一样调用它，该怎么办？

这时候，Vue.extend + vm.$mount 组合就派上用场了。

```javascript
import Vue from 'vue'

const testComponent = Vue.extend({
  template: '<div>{{ text }}</div>',
  data: function () {
    return {
      text: 'extend test'
    }
  }
})
```
然后我们将它手动渲染：

```javascript
const extendComponent = new testComponent().$mount()
```


这时候，我们就将组件渲染挂载到 body 节点上了。

我们可以通过 $el 属性来访问 extendComponent 组件实例：

```javascript
document.body.appendChild(extendComponent.$el)
```





```javascript
  Vue.extend = function (extendOptions: Object): Function {
    extendOptions = extendOptions || {}
    const Super = this
    const SuperId = Super.cid
    const cachedCtors = extendOptions._Ctor || (extendOptions._Ctor = {})
    if (cachedCtors[SuperId]) {
      return cachedCtors[SuperId]
    }

    const name = extendOptions.name || Super.options.name
    if (process.env.NODE_ENV !== 'production' && name) {
      validateComponentName(name)
    }

    const Sub = function VueComponent (options) {
      this._init(options)
    }
    Sub.prototype = Object.create(Super.prototype)
    Sub.prototype.constructor = Sub
    Sub.cid = cid++
    Sub.options = mergeOptions(
      Super.options,
      extendOptions
    )
    Sub['super'] = Super

    // For props and computed properties, we define the proxy getters on
    // the Vue instances at extension time, on the extended prototype. This
    // avoids Object.defineProperty calls for each instance created.
    if (Sub.options.props) {
      initProps(Sub)
    }
    if (Sub.options.computed) {
      initComputed(Sub)
    }

    // allow further extension/mixin/plugin usage
    Sub.extend = Super.extend
    Sub.mixin = Super.mixin
    Sub.use = Super.use

    // create asset registers, so extended classes
    // can have their private assets too.
    ASSET_TYPES.forEach(function (type) {
      Sub[type] = Super[type]
    })
    // enable recursive self-lookup
    if (name) {
      Sub.options.components[name] = Sub
    }

    // keep a reference to the super options at extension time.
    // later at instantiation we can check if Super's options have
    // been updated.
    Sub.superOptions = Super.options
    Sub.extendOptions = extendOptions
    Sub.sealedOptions = extend({}, Sub.options)

    // cache constructor
    cachedCtors[SuperId] = Sub
    return Sub
  }

```



### 编程式的导航


[网页链接](https://router.vuejs.org/zh-cn/essentials/navigation.html)

除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。
router.push(location, onComplete?, onAbort?)

**注意：在 Vue 实例内部，你可以通过 $router 访问路由实例。因此你可以调用 this.$router.push。**

想要导航到不同的 URL，则使用 router.push 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

当你点击 `<router-link>` 时，这个方法会在内部调用，所以说，点击 `<router-link :to="...">` 等同于调用 router.push(...)。

----------


该方法的参数可以是一个字符串路径，或者一个描述地址的对象。例如：

// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})

----------


**注意：如果提供了 path，params 会被忽略，上述例子中的 query 并不属于这种情况。取而代之的是下面例子的做法，你需要提供路由的 name 或手写完整的带有参数的 path：**

```
const userId = 123
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

同样的规则也适用于 router-link 组件的 to 属性。

#### router.replace
router.replace(location, onComplete?, onAbort?)

跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

* 声明式 	`<router-link :to="..." replace>`
* 编程式	router.replace(...)


#### router.go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 window.history.go(n)。

// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)


### **data必须是函数**

[网页链接](https://cn.vuejs.org/v2/guide/components.html)

构造 Vue 实例时传入的各种选项大多数都可以在组件里使用。只有一个例外：data 必须是函数。实际上，如果你这么做：
```javascript
Vue.component('my-component', {
  template: '<span>{{ message }}</span>',
  data: {
    message: 'hello'
  }
})
```
那么 Vue 会停止运行，并在控制台发出警告，告诉你在组件实例中 data 必须是一个函数。但理解这种规则为何存在也是很有益处的，所以让我们先作个弊：
```html
<div id="example-2">
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
  <simple-counter></simple-counter>
</div>
```

```javascript
var data = { counter: 0 }
Vue.component('simple-counter', {
  template: '<button v-on:click="counter += 1">{{ counter }}</button>',
  // 技术上 data 的确是一个函数了，因此 Vue 不会警告，
  // 但是我们却给每个组件实例返回了同一个对象的引用
  data: function () {
    return data
  }
})
new Vue({
  el: '#example-2'
})
```

由于这三个组件实例共享了同一个 data 对象，因此递增一个 counter 会影响所有组件！这就错了。我们可以通过为每个组件返回全新的数据对象来修复这个问题：
```javascript
data: function () {
  return {
    counter: 0
  }
}
```
现在每个 counter 都有它自己内部的状态了



### proxyTable解决开发环境的跨域


[网页链接](http://blog.csdn.net/qq_33559304/article/details/72966028)

在实际项目开发过程中vue cli自带的服务器，但是我们实际要去请求我们的数据接口，服务器与服务器之间产生了一个代理跨域问题，我们需要修改自带服务的配置。在config 中的index文件中，有一个proxyTable参数

参数修改如下：
```javascript
proxyTable: {
  '/list': {
    target: 'http://xxx.xxx.com/xxx/6',
    pathRewrite: {
      '^/list': '/'
    }
  }
},
```
如果需要跨域那么需要加上参数changeOrigin:true
```javascript
proxyTable: {
  '/list': {
    target: 'http://xxx.xxx.com/xxx/6',
changeOrigin:true,
    pathRewrite: {
      '^/list': '/'
    }
  }
},
```



### v-if与v-show



[网页链接](http://www.cnblogs.com/wmhuang/p/5420344.html)

#### 共同点
都是动态显示DOM元素

#### 区别
1. 手段：v-if是动态的向DOM树内添加或者删除DOM元素；v-show是通过设置DOM元素的display样式属性控制显隐；
2. 编译过程：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；v-show只是简单的基于css切换；
3. 编译条件：v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译（编译被缓存？编译被缓存后，然后再切换的时候进行局部卸载); v-show是在任何条件下（首次条件是否为真）都被编译，然后被缓存，而且DOM元素保留；
4. 性能消耗：v-if有更高的切换消耗；v-show有更高的初始渲染消耗；
5. 使用场景：v-if适合运营条件不大可能改变；v-show适合频繁切换。


####  Tips：

**如果v-show作用的元素，相当于css文件中display:none**


原因：v-show控制显隐，是通过js代码去修改元素的element style，如果value为false，设置display:none;如果value为true，设置display: ''；于是value为true时，只能将element style中的display效果清除，并不能覆盖css中的display效果；



#### 总结 

除以下情况使用v-show，其他情况尽量使用v-if

* **有预渲染需求**
* **需要频繁切换显示状态**





### v-if中使用key



 [https://cn.vuejs.org/v2/guide/conditional.html#%E7%94%A8-key-%E7%AE%A1%E7%90%86%E5%8F%AF%E5%A4%8D%E7%94%A8%E7%9A%84%E5%85%83%E7%B4%A0](https://cn.vuejs.org/v2/guide/conditional.html#用-key-管理可复用的元素) 



Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做除了使 Vue 变得非常快之外，还有其它一些好处。例如，如果你允许用户在不同的登录方式之间切换：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>

```



那么在上面的代码中切换 `loginType` 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素， `<input>`不会被替换掉——仅仅是替换了它的 placeholder。



这样也不总是符合实际需求，所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 `key` 属性即可：



```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```



现在，每次切换时，输入框都将被重新渲染。





### 数据的获取

[网页链接](https://router.vuejs.org/zh-cn/advanced/data-fetching.html)





有时候，进入某个路由后，需要从服务器获取数据。例如，在渲染用户信息时，你需要从服务器获取用户的数据。我们可以通过两种方式来实现：

* 导航完成之后获取：先完成导航，然后在接下来的组件生命周期钩子中获取数据。在数据获取期间显示『加载中』之类的指示。

* 导航完成之前获取：导航完成前，在路由的 enter 钩子中获取数据，在数据获取成功后执行导航。

从技术角度讲，两种方式都不错 —— 就看你想要的用户体验是哪种。

----------

#### 导航完成后获取数据

当你使用这种方式时，我们会马上导航和渲染组件，然后在组件的 created 钩子中获取数据。这让我们有机会在数据获取期间展示一个 loading 状态，还可以在不同视图间展示不同的 loading 状态。

假设我们有一个 Post 组件，需要基于 `$route.params.id` 获取文章数据：

```
<template>
  <div class="post">
    <div class="loading" v-if="loading">
      Loading...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace getPost with your data fetching util / API wrapper
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

#### 在导航完成前获取数据

通过这种方式，我们在导航转入新的路由前获取数据。我们可以在接下来的组件的 beforeRouteEnter 钩子中获取数据，当数据获取成功后只调用 next 方法。

```js
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => 
      if (err) {
        // display some global error message
        next(false)
      } else {
        next(vm => {
          vm.post = post
        })
      }
    })
  },
  // 路由改变前，组件就已经渲染完了
  // 逻辑稍稍不同
  watch: {
    $route () {
      this.post = null
      getPost(this.$route.params.id, (err, post) => {
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```





### 替换/合并已有的 Attribute

[https://cn.vuejs.org/v2/guide/components-props.html#%E6%9B%BF%E6%8D%A2-%E5%90%88%E5%B9%B6%E5%B7%B2%E6%9C%89%E7%9A%84-Attribute](https://cn.vuejs.org/v2/guide/components-props.html#替换-合并已有的-Attribute)



想象一下 `<bootstrap-date-input>` 的模板是这样的：

```html
<input type="date" class="form-control">
```

为了给我们的日期选择器插件定制一个主题，我们可能需要像这样添加一个特别的类名：

```html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

在这种情况下，我们定义了两个不同的 `class` 的值：

- `form-control`，这是在组件的模板内设置好的
- `date-picker-theme-dark`，这是从组件的父级传入的

对于绝大多数 attribute 来说，从外部提供给组件的值会替换掉组件内部设置好的值。所以如果传入 `type="text"` 就会替换掉 `type="date"` 并把它破坏！庆幸的是，`class` 和 `style` attribute 会稍微智能一些，即两边的值会被合并起来，从而得到最终的值：`form-control date-picker-theme-dark`。





### props





#### 支持多种type

```js
// 正确的写法 
status: {
      type: [Array, Number],
      default: 2
 }


// 错误的写法
 status: {
      type: Array | Number,
      default: 2
 }
```









## 进阶



### Vue 的父组件和子组件生命周期钩子执行顺序是什么



[https://juejin.im/post/5e4d24cce51d4526f76eb2ba](https://juejin.im/post/5e4d24cce51d4526f76eb2ba)



**渲染过程：**
 父组件挂载完成一定是等子组件都挂载完成后，才算是父组件挂载完，所以父组件的mounted在子组件mouted之后
 父beforeCreate -> 父created -> 父beforeMount -> 子beforeCreate -> 子created -> 子beforeMount -> 子mounted -> 父mounted

**子组件更新过程：**

1. 影响到父组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
2. 不影响父组件： 子beforeUpdate -> 子updated

**父组件更新过程：**

1. 影响到子组件： 父beforeUpdate -> 子beforeUpdate->子updated -> 父updted
2. 不影响子组件： 父beforeUpdate -> 父updated

**销毁过程：**
 父beforeDestroy -> 子beforeDestroy -> 子destroyed -> 父destroyed





**看起来很多好像很难记忆，其实只要理解了，不管是哪种情况，都一定是父组件等待子组件完成后，才会执行自己对应完成的钩子，就可以很容易记住。**







### provide/inject 高级用法



如果想在父组件中拿到层级很深的子组件实例，该如何操作？





```js
// 父组件

export default {
  provide() {
    return {
      // 向后代提供父组件自己的this  
      rootTab: this
    }
  },
  data() {
  },
  methods: {
    onAddSuccess() {
      if (this._myInstance) {
        // 拿到子组件实例 调用方法  
        this._myInstance.fetchData()
      }
    }
  },
}
```









```js
// 子组件


export default {
  inject: ['rootTab'],
  created() {
    // 向父组件实例注入子组件this  
    this.rootTab._myInstance = this
  },
}   
```









### 运行时 + 编译器 

 [https://cn.vuejs.org/v2/guide/installation.html#%E8%BF%90%E8%A1%8C%E6%97%B6-%E7%BC%96%E8%AF%91%E5%99%A8-vs-%E5%8F%AA%E5%8C%85%E5%90%AB%E8%BF%90%E8%A1%8C%E6%97%B6](https://cn.vuejs.org/v2/guide/installation.html#运行时-编译器-vs-只包含运行时) 





如果你需要在客户端编译模板 (比如传入一个字符串给 `template` 选项，或挂载到一个元素上并以其 DOM 内部的 HTML 作为模板)，就将需要加上编译器，即完整版：







```js
// 需要编译器
new Vue({
  template: '<div>{{ hi }}</div>'
})

// 不需要编译器
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```







当使用 `vue-loader` 或 `vueify` 的时候，`*.vue` 文件内部的模板会在构建时预编译成 JavaScript。你在最终打好的包里实际上是不需要编译器的，所以只用运行时版本即可。

因为运行时版本相比完整版体积要小大约 30%，所以应该尽可能使用这个版本。如果你仍然希望使用完整版，则需要在打包工具里配置一个别名：



webpack



```js
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 用 webpack 1 时需用 'vue/dist/vue.common.js'
    }
  }
}
```







### template的使用



 **div**用**v-for**做了列表循环，现在想要**span**也一起循环，应该怎么做 

```html
  <div class="app">
      <div v-for="(item,index) in list" :key="item.id">{{item.text}}</div>
      <span>11111</span>
  </div>
```



 **在div和span外面包裹一个div，给这个div加循环（该方法会额外增加一个多余的div标签）** 





 **若你不想额外增加一个div，此时应该使用template来实现（推荐）** 



**template的作用是模板占位符，可帮助我们包裹元素，但在循环过程当中，template不会被渲染到页面上** 



```html
 <div id="app">
      <template v-for="(item, index) in list" :key="item.id">
            <div>{{item.text}}--{{index}}</div>
             <span>{{item.text}}</span>
       </template>
 </div>
```







### Runtime only 



`Runtime only` / `Runtime + compiler` 是否带编译模块



带有`compiler`的会将模板转化成`render`函数





> 我们在开发时一般使用的是`Runtime only`,可以减小`vue`的体积，但是同样在开发时也不能再使用`template`,`.vue`文件中的`template`是通过`vue-loader`来进行编译的，和我们所说的`compiler`无关哈





```js
new Vue({
	template:`<div></div>`
})
```

 

> 这样的template必须要使用带compiler的入口才能进行模板的解析

##   







###  Single File Components 



[ https://meixg.cn/2018/04/23/vue-sfc-parser/ ]( https://meixg.cn/2018/04/23/vue-sfc-parser/ )





我们平时写的 .vue 文件称为 SFC(Single File Components)。vue 会先对 .vue 文件进行解析，分成 template、script、styles、customBlocks 四个部分，称为 descriptor。之后，再对这四个部分分别进行编译最终得到可以在浏览器中执行的 .js 文件。本文介绍将 SFC 解析为 descriptor 这一过程。



SFCDescriptor，是表示 .vue 各个代码块的对象，为以下数据格式：



```js
// an object format describing a single-file component.
declare type SFCDescriptor = {
    template: ?SFCBlock;
    script: ?SFCBlock;
    styles: Array<SFCBlock>;
    customBlocks: Array<SFCBlock>;
};
```



> `compiler.parseComponent(file, [options])`得到的只是一个组件的 `SFCDescriptor`，最终编译成.js 文件是交给 vue-loader 等库来做的。











###  nextTick



[ http://www.zhufengpeixun.cn/jiagou/vue-analyse/note-2.html#_3-vue-nexttick ]( http://www.zhufengpeixun.cn/jiagou/vue-analyse/note-2.html#_3-vue-nexttick )





```js
const callbacks = []; // 存放nextTick回调
let pending = false; 
function flushCallbacks () { // 清空队列
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
let timerFunc
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx) // 1.将回调函数存入到callbacks中
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc();     // 2.异步刷新队列
  }
  // 3.支持promise写法
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => { 
      _resolve = resolve
    })
  }
}
```



> 不难看出nextTick原理就是将回调函数存入到一个队列中，最后异步的清空这个队列



 **timerFunc** 

```js
// 1.默认先使用Promise 因为mutationObserver有bug可能不工作
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    // 解决队列不刷新问题
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
// 2.使用MutationObserver
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
// 3.使用 setImmediate
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
// 4.使用setTimeout
} else {
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```



> 采用EventLoop中的微任务和宏任务，先采用微任务并按照优先级优雅降级的方式实现异步刷新

###   



### 通用前端组件设计

[https://juejin.im/post/5c02142fe51d4511be77aad7?utm_source=gold_browser_extension](https://juejin.im/post/5c02142fe51d4511be77aad7?utm_source=gold_browser_extension)



我们原则上一个组件只专注一件事情,单一职责的组件的好处很明显,由于职责单一就可以最大可能性地复用组件,但是这也带来一个问题,过度单一职责的组件也可能会导致过度抽象,造成组件库的碎片化。




**组件的形态(DOM结构)永远是千变万化的,但是其行为(逻辑)是固定的,因此通用组件的秘诀之一就是将 DOM 结构的控制权交给开发者,组件只负责行为和最基本的 DOM 结构**





### 内部监听生命周期函数



```js
  mounted() {
    this.chart = echarts.init(this.$el)
    // 请求数据，赋值数据 等等一系列操作...
    
    // 监听窗口发生变化，resize组件
    window.addEventListener('resize', this.$_handleResizeChart)
    // 通过hook监听组件销毁钩子函数，并取消监听事件
    this.$once('hook:beforeDestroy', () => {
      window.removeEventListener('resize', this.$_handleResizeChart)
    })
  },
```





### 外部监听生命周期函数

 同事用了一个第三方组件，需要监听第三方组件数据的变化，但是组件又没有提供`change`事件，同事也没办法了，才想出来要去在外部监听组件的`updated`钩子函数。查看了一番资料，发现`Vue`支持在外部监听组件的生命周期钩子函数。 



```html
<template>
  <!--通过@hook:updated监听组件的updated生命钩子函数-->
  <!--组件的所有生命周期钩子都可以通过@hook:钩子函数名 来监听触发-->
  <custom-select @hook:updated="$_handleSelectUpdated" />
</template>

```





### $attrs属性透传

[https://juejin.im/post/6865451649817640968](https://juejin.im/post/6865451649817640968)

先看官方对 `vm.$attrs` 的定义：

> 包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 **(class 和 style 除外)**。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。



大白话：调用一个组件的时候传入属性 (`class` 和 `style` 除外)，而且不在该组件内部的 `props` 中声明，就可以通过 `v-bind="$attrs"` 传入该组件的内部组件





```vue
<!-- Input 组件 -->
<template>
  <div class="hello input-con">
    <label>输入框：</label>
    <el-input v-bind="$attrs"></el-input>
  </div>
</template>
<script>
export default {
  name: 'Input'
}
</script>

```

调用上面的组件

```vue
<Input placeholder="我是默认值"
        :clearable="true"/> 
```







#### 动态组件如何透传属性



虽然上面可以解决了大部分的问题了，但同事发现并不能满足场景，主要是他用了动态组件 `component`。他的想法是通过 `JSON Schema` 的方式生成表单，其中应用了动态组件 `component`，这是一个很棒的想法，相信现在很多公司应该都有类似的做法。



```vue
<!-- 动态组件，根据 JSON 配置 调用 Input 或者 Select 组件等等 -->
<template>
  <div class="hello">
    <div v-for="(config, index) in configJsonArr" :key="config.type + index">
      <!-- 动态组件，根据配置中的 Type 来决定调用的是 Input 还是 Select -->
      <component :is="config.type" :configProps="config.props"></component>
    </div>
  </div>
</template>

<script>
import Input from './Input'
import Select from './Select'

export default {
  name: 'Config',
  components: {
    Input,
    Select
  },
  props: {
    // 动态组件 JSON schema 的配置
    configJsonArr: {
      type: Array,
      required: true,
      default: () => []
    }
  }
}
</script>

```

其中 `configJsonArr` 为如下：



```js
[{
  type: 'Input',
  props: {
    placeholder: '我是默认值',
    clearable: true
  }
}, {
  type: 'Select',
  props: {
    placeholder: '我是默认值'
  }
}, {
  type: 'Input',
  props: {
    placeholder: '我是默认值',
    suffixIcon: 'el-icon-delete'
  }
}]

```

其中 `Input` 组件类似如下：

```html
<template>
  <div class="hello input-con">
    <label>输入框：</label>
    <el-input ></el-input>
  </div>
</template>

<script>
export default {
  name: 'Input'
}
</script>

```

这个时候，假如我们直接在 `el-input` 设置 `v-bind="$attrs"` 是不行的，原因在于动态组件传入的属性 `configProps` 是一个对象，而不是解构后的对象属性，那怎么办呢？

```js
[{
  type: 'Input',
  props: {
    placeholder: '我是默认值',
    clearable: true
  }
}, {
  type: 'Select',
  props: {
    placeholder: '我是默认值'
  }
}, {
  type: 'Input',
  props: {
    placeholder: '我是默认值',
    suffixIcon: 'el-icon-delete'
  }
}]

```

其中 `Input` 组件类似如下：

```html
<template>
  <div class="hello input-con">
    <label>输入框：</label>
    <el-input ></el-input>
  </div>
</template>

<script>
export default {
  name: 'Input'
}
</script>
```

这个时候，假如我们直接在 `el-input` 设置 `v-bind="$attrs"` 是不行的，原因在于动态组件传入的属性 `configProps` 是一个对象，而不是解构后的对象属性，那怎么办呢？



**我们可以使用渲染函数！**



```js
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // 一个 HTML 标签名、组件选项对象，或者
  // resolve 了上述任何一种的一个 async 函数。必填项。
  'div',

  // {Object}
  // 一个与模板中 attribute 对应的数据对象。可选。
  {
    // (详情见下一节)
  },

  // {String | Array}
  // 子级虚拟节点 (VNodes)，由 `createElement()` 构建而成，
  // 也可以使用字符串来生成“文本虚拟节点”。可选。
  [
    '先写一些文字',
    createElement('h1', '一则头条'),
    createElement(MyComponent, {
      props: {
        someProp: 'foobar'
      }
    })
  ]
)

```

我们再将重点放在第二个参数 `Object` 中，我们可以在这个 `Object` 中指定相关的属性值，比如 `class`、`style`、`attrs`(普通的 `HTML attribute`)、组件的 `props`【这个就是我们这一期重点关注的属性值】....。具体如下：

```js
// 第二个参数 `Object` 中

{
  // 与 `v-bind:class` 的 API 相同，
  // 接受一个字符串、对象或字符串和对象组成的数组
  'class': {
    foo: true,
    bar: false
  },
  // 与 `v-bind:style` 的 API 相同，
  // 接受一个字符串、对象，或对象组成的数组
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 普通的 HTML attribute
  attrs: {
    id: 'foo'
  },
  // 组件 prop
  props: {
    myProp: 'bar'
  },
  // DOM property
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器在 `on` 内，
  // 但不再支持如 `v-on:keyup.enter` 这样的修饰器。
  // 需要在处理函数中手动检查 keyCode。
  on: {
    click: this.clickHandler
  },
  // 仅用于组件，用于监听原生事件，而不是组件内部使用
  // `vm.$emit` 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler
  },
  // 自定义指令。注意，你无法对 `binding` 中的 `oldValue`
  // 赋值，因为 Vue 已经自动为你进行了同步。
  directives: [
    {
      name: 'my-custom-directive',
      value: '2',
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // 作用域插槽的格式为
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => createElement('span', props.text)
  },
  // 如果组件是其它组件的子组件，需为插槽指定名称
  slot: 'name-of-slot',
  // 其它特殊顶层 property
  key: 'myKey',
  ref: 'myRef',
  // 如果你在渲染函数中给多个元素都应用了相同的 ref 名，
  // 那么 `$refs.myRef` 会变成一个数组。
  refInFor: true
}
```

可以看到，我们可以在上面这个对象中设置 `props` 属性的值的时候，将它解构掉就可以了。

核心代码实现，如下所示：



```js
// 这其实就是一个组件
const CompFormItem = {
  components: {
    Input, Select
  },
  name: 'FormItem',
  props: {
    // 传入配置
    configJson: {
      required: true
    }
  },
  // h 实际上就是 createElement 参数
  render (h) {
    // 第一个参数就是配置中的 type，也就是我们的组件名称
    return h(`${this.configJson.type}`, {
      props: {
        // 针对 props 进行解构
        ...this.configJson.props || {}
      },
      attrs: {
        // 针对 attrs 进行解构
        ...this.configJson.props || {}
      }
    })
  }
}

```

这样我们再在 `Input` 组件中写上 `v-bind="$attrs"` 就可以了

```html
<template>
  <div class="hello input-con">
    <label>输入框：</label>
    <el-input v-bind="$attrs"></el-input>
  </div>
</template>

<script>
export default {
  name: 'Input'
}
</script>
```



---



**更好的方法是使用 v-bind.props="xxx"**



[v-bind](https://cn.vuejs.org/v2/api/#v-bind)

- `.prop` - 作为一个 DOM property 绑定而不是作为 attribute 绑定。([差别在哪里？](https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028))
- `.camel` - (2.1.0+) 将 kebab-case attribute 名转换为 camelCase。(从 2.1.0 开始支持)
- `.sync` (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 `v-on` 侦听器。





```vue
<!-- 通过 prop 修饰符绑定 DOM attribute -->
<div v-bind:text-content.prop="text"></div>

<!-- prop 绑定。“prop”必须在 my-component 中声明。-->
<my-component :prop="someThing"></my-component>

<!-- 通过 $props 将父组件的 props 一起传给子组件 -->
<child-component v-bind="$props"></child-component>
```



















[1]: http://img.zhimengzhe.com/d/file/p/2017-03-05/6e69817f1e18ae5389320cc5c00641b4.jpg

