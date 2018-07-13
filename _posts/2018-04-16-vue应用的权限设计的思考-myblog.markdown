---
layout:     post
title:      "vue应用的权限设计的思考"
date:       2018-04-16 16:24:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Vue
---

> “Yeah It's on. ”


## 正文
[网页链接](https://github.com/JesseZhao1990/blog/issues/64)

对于单页面应用来说，前端权限控制应该是从两个方面入手

1. 根据用户拥有的权限id的集合决定用户能访问那些前端路由
2. 根据用户拥有的权限id的集合决定用户能看到那些按钮或者模块。

### 完美的方案
在vue-router2.2之后的版本有个新的api叫addRouters，可以动态的添加路由。页面加载时的我们可以先初始化一个空路由，紧接着我们请求用户的权限信息，然后根本用户的权限信息筛选出来用户能访问的路由，然后利用addRouters添加到应用的路由中。

注意：当登录页在单页面之内时，并且登录页没有单独的路由时，按钮的权限过滤并不适合用指令，因为指令是依赖于指令所在的组件而生的，没法根据外部数据来决定指令所在的组件的有无。也就是说没法根据vuex中的数据来动态决定按钮的显示与否。当切换用户的时候，权限的list改变了，但是按钮并不会相应的更新，这时的最佳方法应该是把权限的鉴别功能封装成一个组件，而不是指令


----------


[https://refined-x.com/2017/11/28/Vue2.0%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/](https://refined-x.com/2017/11/28/Vue2.0%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)




会话开始之初，先初始化一个只有登录路由的Vue实例，在根组件created钩子里将路由定向到登录页，用户登录成功后前端拿到用户token，设置axios实例统一为请求headers添加{"Authorization":token}实现用户鉴权，然后获取当前用户的权限数据，主要包括路由权限和资源权限，之后动态添加路由，生成菜单，实现权限指令和全局权限验证方法，并为axios实例添加请求拦截器，至此完成权限控制初始化。动态加载路由后，路由组件将随之加载并渲染，而后展现前端界面。

为解决浏览器刷新路由重置的问题，拿到token后要将其保存到sessionStorage，根组件的created钩子负责检查本地是否已有token，如果有则无需登录直接用该token获取权限并初始化，如果token有效且当前路由有权访问，将加载路由组件并正确展现；若当前路由无权访问将按路由设置跳转404；如果token失效，后端应返回4xx状态码，前端统一为axios实例添加错误拦截器，遇到4xx状态码执行退出操作，清除sessionStorage数据并跳转到登录页，让用户重新登录。


[https://github.com/tower1229/Vue-Access-Control](https://github.com/tower1229/Vue-Access-Control)



[基于Vue实现后台系统权限控制 ](https://refined-x.com/2017/08/29/%E5%9F%BA%E4%BA%8EVue%E5%AE%9E%E7%8E%B0%E5%90%8E%E5%8F%B0%E7%B3%BB%E7%BB%9F%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6/)



[用addRoutes实现动态路由](https://refined-x.com/2017/09/01/%E7%94%A8addRoutes%E5%AE%9E%E7%8E%B0%E5%8A%A8%E6%80%81%E8%B7%AF%E7%94%B1/)





