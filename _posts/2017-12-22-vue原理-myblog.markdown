---
layout:     post
title:      "Vue原理"
date:       2017-12-22 12:45:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”

# 正文

这里存放vue全家桶相关的原理分析




## 响应式原理

[网页链接](https://cn.vuejs.org/v2/guide/reactivity.html)

现在是时候深入一下了Vue 的一个最明显的特性就是其不太引人注意的响应式系统。数据模型仅仅是普通的 JavaScript 对象。而当你修改它们时，视图会进行更新。这使得状态管理非常简单直接，不过理解其工作原理同样重要，这样你可以回避一些常见的问题。

### 如何追踪变化
当你把一个普通的 JavaScript 对象传给 Vue 实例的 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。Object.defineProperty 是 ES5 中一个无法 shim 的特性，这也就是为什么 Vue 不支持 IE8 以及更低版本浏览器的原因。

用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。这里需要注意的问题是浏览器控制台在打印数据对象时 getter/setter 的格式化并不同，所以你可能需要安装 vue-devtools 来获取更加友好的检查接口。

个组件实例都有相应的 **watcher** 实例对象，它会在组件渲染的过程中把属性记录为依赖，之后当依赖项的 setter 被调用时，会通知 watcher 重新计算，从而致使它关联的组件得以更新。

![enter description here][1]


### 检测变化的注意事项
受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue **不能检测到对象属性的添加或删除**。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。例如：
```
var vm = new Vue({
  data:{
  a:1
  }
})

// `vm.a` 是响应的

vm.b = 2
// `vm.b` 是非响应的
```

Vue 不允许在已经创建的实例上动态添加新的根级响应式属性 (root-level reactive property)。然而它可以使用 **Vue.set(object, key, value)** 方法将响应属性添加到嵌套的对象上：
```
Vue.set(vm.someObject, 'b', 2)
```
还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名：
```
this.$set(this.someObject,'b',2)
```


有时你想向已有对象上添加一些属性，例如使用 Object.assign() 或 _.extend() 方法来添加属性。但是，添加到对象上的新属性不会触发更新。在这种情况下可以创建一个新的对象，让它包含原对象的属性和新的属性：
```
// 代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

### 声明响应式属性
由于 Vue 不允许动态添加根级响应式属性，所以你必须在初始化实例前声明根级响应式属性，哪怕只是一个空值：
```
var vm = new Vue({
  data: {
    // 声明 message 为一个空值字符串
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// 之后设置 `message`
vm.message = 'Hello!'
```
如果你在 data 选项中未声明 message，Vue 将警告你渲染函数在试图访问的属性不存在。



### 异步更新队列
可能你还没有注意到，Vue **异步**执行 DOM 更新。只要观察到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。

例如，当你设置 vm.someData = 'new value' ，该组件不会立即重新渲染。当刷新队列时，组件会在事件循环队列清空时的下一个“tick”更新。多数情况我们不需要关心这个过程，但是如果你想在 DOM 状态更新后做点什么，这就可能会有些棘手。虽然 Vue.js 通常鼓励开发人员沿着“数据驱动”的方式思考，避免直接接触 DOM，但是有时我们确实要这么做。为了在数据变化之后等待 Vue 完成更新 DOM ，可以在数据变化之后立即使用 **Vue.nextTick(callback)** 。这样回调函数在 DOM 更新完成后就会调用。
```
<div id="example">{{message}}</div>
```

```
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // 更改数据
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```

>**在组件内使用 vm.$nextTick() 实例方法特别方便，因为它不需要全局 Vue ，并且回调函数中的 this 将自动绑定到当前的 Vue 实例上**


```js
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: '没有更新'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = '更新完成'
      console.log(this.$el.textContent) // => '没有更新'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => '更新完成'
      })
    }
  }
})
```









### 源码

[ https://mp.weixin.qq.com/s/4ke__PpGpQNPGXbr1MEgAA ]( https://mp.weixin.qq.com/s/4ke__PpGpQNPGXbr1MEgAA )



#### 更新数据渲染页面





我们是通过new Watcher()来初始化页面的，也就是说这个watcher具有重新渲染页面的功能，因此，我们一旦改数据的时候，就再一次让这个watcher执行刷新页面的功能。这里有必要解释下一个watcher对应一个组件，也就是说你new Vue 机会生成一个wacther，因此有多个组件的时候就会生成多个watcher。 







现在，我们给每一个data里的属性生成一个对应的dep。例如：

```js
data:{
  age:18,
  friend:{
    name:"赵丽颖",
    age:12
  }
}
```



上面中，age,friend,friend.name,friend.age分别对应一个dep。一共四个dep。dep的功能是用来通知上面谈到的watcher执行刷新页面的功能的。

```js
export function defineReactive(data,key,value) {
    // 观察value是不是对象，是的话需要监听它的属性。
    observe(value)
    let dep = new Dep() // 新增代码：一个key对应一个dep
    Object.defineProperty(data,key,{
        get(){
            return value
        },
        set(newValue){
            if (newValue === value) return
            value = newValue
            observe(value)
        }
    })
}
```

现在有一个问题，就是dep要怎么跟watcher关联起来，我们可以把watcher存储到dep里

```js
let id = 0
class Dep {
    constructor(){
        this.id = id++
        this.subs = []
    }
    addSub(watcher){ //订阅
        this.subs.push(watcher)
    }
}
```

如代码所示，我们希望执行addSub方法就可以将watcher放到subs里。那什么时候可以执行addSub呢？





我们在执行compile的时候，也就是将dom里的{{}}表达式换成data里的值的时候，因为要获得data里的值，因此会触发get。这样，我们就可以在get里执行addSub。而watcher是放在全局作用域的，我们可以直接重全局作用域中拿这个watcher放到传入addSub。





好了，现在的问题就是，怎么把watcher放到全局作用域

```js
let id = 0
class Watcher {
    constructor(vm,exprOrFn,cb = ()=>{},opts){
        this.vm = vm
        this.exprOrFn = exprOrFn
        this.cb = cb
        this.id = id++
        this.deps = []
        this.depsId = new Set()
        if (typeof exprOrFn === 'function'){
            this.getter = exprOrFn
        }
        this.get()  // 创建一个watcher，默认调用此方法
    }
    get(){
        pushTarget(this)
        this.getter()
        popTarget()
    }
}
export default Watcher
```



可见，是通过pushTarget(this)放到全局作用域，再通过popTarget()将它移除。



要知道，wachter和dep是多对多的关系，dep里要保存对应的watcher，watcher也要保存对应的dep 因此，但我们触发get的时候，希望可以同时让当前的watcher保存当前的dep，也让当前的dep保存当前的wacther 



```js
export function defineReactive(data,key,value) {
    // 观察value是不是对象，是的话需要监听它的属性。
    observe(value)
    let dep = new Dep()
    Object.defineProperty(data,key,{
        get(){
            if (Dep.target){
                dep.depend() //让dep保存watcher，也让watcher保存这个dep
            }
            return value
        },
        set(newValue){
            if (newValue === value) return
            value = newValue
            observe(value)

        }
    })
}
```





让我们看下depend方法怎么实现

```js
let id = 0
class Dep {
    constructor(){
        this.id = id++
        this.subs = []
    }
    addSub(watcher){ //订阅
        this.subs.push(watcher)
    }
    depend(){
        if (Dep.target){
            Dep.target.addDep(this)
        }
    }
}
// 保存当前watcher
let stack = []
export function pushTarget(watcher) {
    Dep.target = watcher
    stack.push(watcher)
}
export function popTarget() {
    stack.pop()
    Dep.target = stack[stack.length - 1]
}

export default Dep
```



可见depend方法又执行了watcher里的addDep，看一下watcher里的addDep。

```js
import {pushTarget , popTarget} from "./dep"
let id = 0
class Watcher {
    constructor(vm,exprOrFn,cb = ()=>{},opts){
        this.vm = vm
        this.exprOrFn = exprOrFn
        this.cb = cb
        this.id = id++
        this.deps = []
        this.depsId = new Set()
        if (typeof exprOrFn === 'function'){
            this.getter = exprOrFn
        }
        this.get()  // 创建一个watcher，默认调用此方法
    }
    get(){
        pushTarget(this)
        this.getter()
        popTarget()
    }
    update(){
        this.get()
    }
    addDep(dep){
        let id = dep.id
        if(this.depsId.has(id)){
            this.depsId.add(id)
            this.deps.push(dep)
        }
        dep.addSub(this)
    }
}
export default Watcher
```

如此一来，就让dep和watcher实现了双向绑定。这里代码，你可能会有个疑问，就是为什么是用一个stack数组来保存watcher，这里必须解释下，因为每一个watcher是对应一个组件的，也就是说，当页面中有多个组件的时候，就会有多个watcher，而多个组件的执行是依次执行的，也就是说Dep.target中 只会有 当前被执行的组件所对应的watcher。





#### 批量更新防止重复渲染



每更改一个数据，就会通知watcher重新渲染页面，显然，要是我们在一个组件里更改多个数据，那么就会多次通知wathcer渲染页面，因此这节我们来实现 批量更新，防止重复渲染。





## vue-loader

> webpack loader for Vue Single-File Components. 用于 Vue 单文件组件的 webpack 加载器。

处理 SFC 中的每个语言块，然后组装成最终模块。

 

使用 `@vue/component-compiler-utils` 解析 SFC 为每个语言块生成一个导入

```js
// import the <template> block
import render from 'source.vue?vue&type=template'
// import the <script> block
import script from 'source.vue?vue&type=script'
export * from 'source.vue?vue&type=script'
// import <style> blocks
import 'source.vue?vue&type=style&index=1'

script.render = render
export default script
```

对相应语言块，增加其他处理规则

```js
import script from 'babel-loader!vue-loader!source.vue?vue&type=script'
import 'source.vue?vue&type=style&index=1&scoped&lang=scss'
```

处理扩展请求时，再次调用 vue-loader，但这次会明确将其传递给匹配的目标加载器

对于 scoped 等进行处理（VueLoaderPlugin 会注入一个全局的Pitching Loader（`src/pitcher.ts`），它会拦截Vue的 `<template>`和`<style>`请求并注入必要的加载器）













## vue-template-compiler

[https://zhuanlan.zhihu.com/p/114239056](https://zhuanlan.zhihu.com/p/114239056)

该模块可用于将 Vue 2.0 **模板预编译为渲染函数（template => ast => render）**，以避免运行时编译开销和 CSP 限制。**大都数场景下，与 `vue-loader`一起使用，只有在编写具有非常特定需求的构建工具时，才需要单独使用它**



可得知，`vue-template-compiler` 的代码是**从 vue 源码中抽离的**！接着，我们对比一下 `vue-template-compiler` 和 vue 关于编译的 API。发现对于 compile 等函数是一致，只是 `vue-template-compiler` 开放的参数和方法更多。因此，**`vue` 和 `vue-template-compiler` 的版本必须一致（同一份源码）！**


