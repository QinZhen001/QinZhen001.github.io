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







## 面试题



###  父子组件的执行顺序是什么 



在组件开始生成到结束生成的过程中，如果该组件还包含子组件，则自己开始生成后，要让所有的子组件也开始生成，然后自己就等着，直到所有的子组件生成完毕，自己再结束。“父亲”先开始自己的created，然后“儿子”开始自己的created和mounted，最后“父亲”再执行自己的mounted。



 为什么会这样，到这里我们就应该发现了，new Vue的时候是先执行initData，也就是初始化数据，然后执行$mounted,也就是new Watcher。而初始化数据的时候，也要处理components里的数据。处理component里的数据的时候，每处理一个子组件就会new Vue，生成一个子组件。因此是顺序是这样的。也就对应了上面的答案。 



1.  初始化父组件数据
2.  初始化 子组件数据 
3.  new 子组件Wacther 
4.  new 父组件Watcher





### 为什么需要虚拟dom diff

既然vue通过数据劫持可以精确探测数据在具体dom上的变化，为什么还需要虚拟dom diff？



答案：



现代前端框架有两种方式侦测变化，一种是pull，一种是push



* pull其代表为react，我们可以回忆一下react是如何侦测到变化的，我们通常会用setState Api显式更新，然后 React会进行一层层的 Virtual dom diff操作找出差异，然后 Patch到DOM上， React从一开始就不知道到是哪发生了变化，只是知道「有变化了」，然后再进行比较暴力的Diff操作查找「哪发生变化了」，另外一个代表就是 Angular的脏检查操作
* push：vue的响应式系统则是push的代表，当vue程序初始化的时候就会对数据data进行依赖的收集，一但数据发生变化，响应式系统就会立刻得知。因此vue是一开始就知道是「在哪发生变化了」，但是这又会产生一个问题，如果你熟悉ue的响应式系统就知道，通常一个绑定一个数据就需要一个 Watcher。一但我们的绑定细粒度过高就会产生大量的 Watcher，这会芾来內存以及依赖追踪的开销，而细粒度过低会无法精准侦测变化因此vue的设计是选择中等细粒度的方案在组件级别进行push侦测的方式也就是那套响应式系统通常我们会第一时间侦测到发生变化的组件然后在组件内部进行Virtual dom diff获取更加具体的差异，而 Virtual dom diff则是pukl操作，vue是push+pull结合的方式进行变化侦测的。

![](https://s1.ax1x.com/2020/07/09/UegPQx.png)











### react为什么要整虚拟dom



* react的架构根本感知不到数据变化，也无法得知数据变化会影响到哪些dom，他只能通过diff vdom来找出差异点。
* 而vue的架构和react是完全不一样的，vue是可以知道精准的知道数据变化的，也可以知道这个数据变化会影响到哪些dom。





### v-show 与 v-if 区别

1.  v-hsow和v-if的区别： v-show是css切换，v-if是完整的销毁和重新创建。
2.  使用 频繁切换时用v-show，运行时较少改变时用v-if
3.  v-if=‘false’ v-if是条件渲染，当false的时候不会渲染





### 绑定 class 的数组用法

* 对象方法 `v-bind:class="{'orange': isRipe, 'green': isNotRipe}"`

* 数组方法  `v-bind:class="[class1, class2]"`

* 行内 `v-bind:style="{color: color, fontSize: fontSize+'px' }"`





### 组件中 data 为什么是函数

> 为什么组件中的 data 必须是一个函数，然后 return 一个对象，而 new Vue 实例里，data 可以直接是一个对象？



因为组件是用来复用的，JS 里对象是引用关系，这样作用域没有隔离，而 new Vue 的实例，是不会被复用的，因此不存在引用对象的问题。





### vnode 有什么优势

首先是抽象，引入 vnode，可以把渲染过程抽象化，从而使得组件的抽象能力也得到提升。

其次是跨平台，因为 patch vnode 的过程不同平台可以有自己的实现，基于 vnode 再做服务端渲染、Weex 平台、小程序平台的渲染都变得容易了很多。



**使用 vnode 并不意味着不用操作 DOM 了，很多同学会误以为 vnode 的性能一定比手动操作原生 DOM 好，这个其实是不一定的。**



**性能并不是 vnode 的优势**







[1]: https://cn.vuejs.org/images/data.png