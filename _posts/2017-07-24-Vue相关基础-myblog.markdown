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

<image src="https://cn.vuejs.org/images/lifecycle.png"></image>



![生命周期][1]




#### 注意

不要在实例属性或者回调函数中（如 vm.$watch('a', newVal => this.myMethod())）使用箭头函数。因为箭头函数绑定父级上下文，所以 this 不会像预想的一样是 Vue 实例，而是 this.myMethod 未被定义。





### slot


Slot是用于组件中的占位符，组件标签是成对存在的，被组件的标签包裹的，会被填入到slot的位置，替代slot。也就是说，slot是为了让组件里的内容可变做出的一个手段。

主要有以下几种slot用法：
方法1：单slot区
```html
// 组件A
<template>
    <div>
        <h2>A</h2>
        <slot></slot>
    </div>
</template>

// 调用组件A的部分
<A>
    <span>这是slot的部分</span>
</A>
这个最后会被解析成：

<div>
    <h2>A</h2>
    <span>这是slot的部分</span>
</div>
```

当然，还有另外一个用法：具名slot，主要是针对有多个区域需要填入不同的内容，为了内容错位，给不同的slot加上名字，在组件调用时，填入slot的时候也写上对应的名字，这样，对应的填充块就被写到组件中相同名字的slot处。
```html
// 组件A
<template>
    <div>
        <h1>这是标题</h1>
        <slot name="img"></slot>
        <slot name="para"></slot>
    </div>
</template>

// 调用组件A的地方
<A>
    <p slot="para">这是段落</p>
    <img slot="img" />
</A>
最后会被编译成HTML代码：

<div>
    <h1>这是标题</h1>
    <img slot="img" />
    <p slot="para">这是段落</p>
</div>
```







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































































[1]: http://img.zhimengzhe.com/d/file/p/2017-03-05/6e69817f1e18ae5389320cc5c00641b4.jpg

