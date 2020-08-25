---
layout:     post
title:      "element源码相关"
date:       2020-07-15 16:44:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



## 组件






## utils工具函数





### on

on设计成了一个自执行函数，这样可以通过执行，if-else判断，最终返回一个函数

```js
/* istanbul ignore next */
export const on = (function() {
  if (!isServer && document.addEventListener) {
    return function(element, event, handler) {
      if (element && event && handler) {
        element.addEventListener(event, handler, false);
      }
    };
  } else {
    return function(element, event, handler) {
      if (element && event && handler) {
        element.attachEvent('on' + event, handler);
      }
    };
  }
})();

```





## 自定义命令



### clickoutside

[https://www.jianshu.com/p/586581ad62c3](https://www.jianshu.com/p/586581ad62c3)

点击元素外面才会触发

```js
/**
 * v-clickoutside
 * @desc 点击元素外面才会触发的事件
 * @example
 * ```vue
 * <div v-clickoutside="handleClose">
 * ```
 */
```



```js
const nodeList = [];
const ctx = '@@clickoutsideContext';

let startClick;
let seed = 0;

!Vue.prototype.$isServer && on(document, 'mousedown', e => (startClick = e));

!Vue.prototype.$isServer && on(document, 'mouseup', e => {
  nodeList.forEach(node => node[ctx].documentHandler(e, startClick));
});

function createDocumentHandler(el, binding, vnode) {
  return function(mouseup = {}, mousedown = {}) {
    if (!vnode ||
      !vnode.context ||
      !mouseup.target ||
      !mousedown.target ||
      // 在元素内部  
      el.contains(mouseup.target) ||
      el.contains(mousedown.target) ||
      el === mouseup.target ||
      // vnode.context.popperElm这部分内容则是 : 判断是否点击在下拉菜单的上，如果是，也是没有点击在         // 绑定元素外部，不执行clickoutside指令内容
      (vnode.context.popperElm &&
      (vnode.context.popperElm.contains(mouseup.target) ||
      vnode.context.popperElm.contains(mousedown.target)))) return;

    if (binding.expression &&
      el[ctx].methodName &&
      vnode.context[el[ctx].methodName]) {
      vnode.context[el[ctx].methodName]();
    } else {
      el[ctx].bindingFn && el[ctx].bindingFn();
    }
  };
}

// 封装成自定义命令
export default {
  bind(el, binding, vnode) {
    nodeList.push(el);
    const id = seed++;
    el[ctx] = {
      id,
      documentHandler: createDocumentHandler(el, binding, vnode),
      methodName: binding.expression,
      bindingFn: binding.value
    };
  },

  update(el, binding, vnode) {
    el[ctx].documentHandler = createDocumentHandler(el, binding, vnode);
    el[ctx].methodName = binding.expression;
    el[ctx].bindingFn = binding.value;
  },

  unbind(el) {
    let len = nodeList.length;

    for (let i = 0; i < len; i++) {
      if (nodeList[i][ctx].id === el[ctx].id) {
        nodeList.splice(i, 1);
        break;
      }
    }
    delete el[ctx];
  }
};
```





### repeat-click

长按点击重复触发事件

```js
import { once, on } from '@/utils/dom';

export default {
  bind(el,binding,vnode){
    let interval = null 
    let startTime
    const handler = () => vnode.context[binding.expression].apply()
    const clear = () => {
      if(Date.now() - startTime < 100){
        handler()
      }
      clearInterval(interval);
      interval = null 
    }

    on(el,'mousedown',(e)=>{
      // 必须为鼠标左键点击 
      if (e.button !== 0) return;
      startTime = Date.now();
      once(document, 'mouseup', clear);
      clearInterval(interval);
      interval = setInterval(handler, 100);
    })
  }
}
```





## 广播 



### dispath 和 broadcast



broadcast 方法的作用是向后代子孙组件传值，它会遍历所有的后代组件，当遍历到后代组件中 componentName 与当前的组件名一样，则触发 $emit 事件，以此来传递数据



dispatch 的作用是向祖先组件传值，它会一直寻找父组件，直到找到组件名和当前传入的组件名一致的祖先组件，就会触发其身上的 `$emit` 事件，并传递数据



```js
function broadcast(componentName,eventName,params){
  this.$children.forEach(child => {
    let name = child.$options.componentName

    if(name === componentName){
      child.$emit.apply(child,[eventName].concat([params]))
    }else{
      broadcast.apply(child,[componentName, eventName].concat([params]))
    }
  })
}

export default {
  methods:{
    dispatch(componentName, eventName, params){
      let parent = this.$parent || this.$root
      let name = parent.$options.componentName

      while(parent && (!name || name !== componentName)){
        parent = parent.$parent
        if(parent){
          name =  parent.$options.componentName
        }
      }

      if(parent){
        parent.$emit.apply(parent,[eventName].concat(params))
      }
    },
    broadcast(componentName, eventName, params){
      broadcast.call(this, componentName, eventName, params);
    }
  }
}

```





## 文档是如何实现的

[https://juejin.im/post/6862590339396403208?utm_source=gold_browser_extension](https://juejin.im/post/6862590339396403208?utm_source=gold_browser_extension)



把用markdown写的文档拼接成vue文件(这个通过他们自己写的md-loader处理)，这个vue文件和我们平时开发项目的vue组件类型(类似`<template>...</template><script>export default{}</script>`),再通过vue-loader处理。





## 补充



### 正则获取script中的数据

```js
function stripScript(content) {
  const result = content.match(/<(script)>([\s\S]+)<\/\1>/);
  return result && result[2] ? result[2].trim() : '';
}
```





