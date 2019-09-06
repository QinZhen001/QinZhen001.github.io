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


### key


[网页链接](https://cn.vuejs.org/v2/api/#key)


预期：number | string
key 的特殊属性主要用在 Vue的虚拟DOM算法，在新旧nodes对比时辨识VNodes。如果不使用key，Vue会使用一种最大限度减少动态元素并且尽可能的尝试修复/再利用相同类型元素的算法。使用key，它会基于key的变化重新排列元素顺序，并且会移除key不存在的元素。

有相同父元素的子元素必须有独特的key。重复的key会造成渲染错误。

最常见的用例是结合 v-for:
```html
<ul>
  <li v-for="item in items" :key="item.id">...</li>
</ul>
```


它也可以用于强制替换元素/组件而不是重复使用它。当你遇到如下场景时它可能会很有用:

* 完整地触发组件的生命周期钩子
* 触发过渡

例如:
```html
<transition>
  <span :key="text">{{ text }}</span>
</transition>
```


当 text 发生改变时，`<span>` 会随时被更新，因此会触发过渡。



 **v-for必须加上key，并避免同时使用v-if**

一般我们在两种常见的情况下会倾向于这样做:


为了过滤一个列表中的项目 比如 v-for="user in users" v-if="user.isActive"。在这种情形下，请将 users替换为一个计算属性 (比如activeUsers)，让其返回过滤后的列表


为了避免渲染本应该被隐藏的列表 比如 v-for="user in users" v-if="shouldShowUsers"。这种情形下，请将 v-if 移动至容器元素上 (比如 ul, ol)





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











  [1]: http://img.zhimengzhe.com/d/file/p/2017-03-05/6e69817f1e18ae5389320cc5c00641b4.jpg