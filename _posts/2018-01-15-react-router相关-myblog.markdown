---
layout:     post
title:      "React Router 4.0"
date:       2018-01-15 13:00:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”


## 正文

[api中文文档](http://reacttraining.cn/web/example/basic)

[初探 React Router 4.0](https://www.jianshu.com/p/e3adc9b5f75c)


RR4 本次采用单代码仓库模型架构（monorepo），这意味者这个仓库里面有若干相互独立的包，分别是：

* react-router React Router 核心
* react-router-dom 用于 DOM 绑定的 React Router
* react-router-native 用于 React Native 的 React Router
* react-router-redux React Router 和 Redux 的集成
* react-router-config 静态路由配置的小助手


### react-router 还是 react-router-dom？
在 React 的使用中，我们一般要引入两个包，react 和 react-dom，那么 react-router 和 react-router-dom 是不是两个都要引用呢？
非也，坑就在这里。他们两个只要引用一个就行了，不同之处就是后者比前者多出了 `<Link>` `<BrowserRouter>` 这样的 DOM 类组件。
因此我们**只需引用 react-router-dom 这个包**就行了。当然，如果搭配 redux ，你还需要使用 react-router-redux。

### 组件
`<BrowserRouter>`

一个使用了 HTML5 history API 的高阶路由组件，保证你的 UI 界面和 URL 保持同步。此组件拥有以下属性：

**basename: string**
作用：为所有位置添加一个基准URL
使用场景：假如你需要把页面部署到服务器的二级目录，你可以使用 basename 设置到此目录。

```
<BrowserRouter basename="/minooo" />
<Link to="/react" /> // 最终渲染为 <a href="/minooo/react">
```

**getUserConfirmation: func**
作用：导航到此页面前执行的函数，默认使用 window.confirm
使用场景：当需要用户进入页面前执行什么操作时可用，不过一般用到的不多。

**forceRefresh: bool**
作用：当浏览器不支持 HTML5 的 history API 时强制刷新页面。
使用场景：同上。

```
const supportsHistory = 'pushState' in window.history
<BrowserRouter forceRefresh={!supportsHistory} />
```

**keyLength: number**
作用：设置它里面路由的 location.key 的长度。默认是6。（key的作用：点击同一个链接时，每次该路由下的 location.key都会改变，可以通过 key 的变化来刷新页面。）
使用场景：按需设置。
`<BrowserRouter keyLength={12} />`


**children: node**
作用：渲染唯一子元素。
使用场景：作为一个 React组件，天生自带 children 属性。



### `<Route>`
`<Route>` 也许是 RR4 中最重要的组件了，重要到你必须理解它，学会它，用好它。它最基本的职责就是当页面的访问地址与 Route 上的 path 匹配时，就渲染出对应的 UI 界面。

`<Route>` 自带三个 render method 和三个 props 。

render methods 分别是：
* `<Route component>`
* `<Route render>`
* `<Route children>`

每种 render method 都有不同的应用场景，同一个`<Route>` 应该只使用一种 render method ，大部分情况下你将使用 component 。

props 分别是：
* match
* location
* history

**所有的 render method 无一例外都将被传入这些 props。**

在 Route component 中，以 this.props.location 的方式获取，
在 Route render 中，以 ({ location }) => () 的方式获取，
在 Route children 中，以 ({ location }) => () 的方式获取，


History 和 match的获取方式类似。

如果当前的地址是 `/char/123`
那么match.path是 `/char/:user`
那么match.url是 `/chat/123`

>**match.url是指向实际的url**

>ps：在这个例子中，match.params.user为123

#### component

只有当访问地址和路由匹配时，一个 React component 才会被渲染，此时此组件接受 route props (match, location, history)。
当使用 component 时，router 将使用 React.createElement 根据给定的 component 创建一个新的 React 元素。这意味着如果你使用内联函数（inline function）传值给 component 将会产生不必要的重复装载。对于内联渲染（inline rendering）, 建议使用 render prop。

```
<Route path="/user/:username" component={User} />
const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```

#### render: func
此方法适用于内联渲染，而且不会产生上文说的重复装载问题。
```
// 内联渲染
<Route path="/home" render={() => <h1>Home</h1} />

// 包装 组合
const FadingRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    <FadeIn>
      <Component {...props} />
    </FaseIn>
  )} />
)

<FadingRoute path="/cool" component={Something} />
```

作者：_minooo_
链接：https://www.jianshu.com/p/e3adc9b5f75c
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### IndexRoute

[网页链接](http://www.mycode.net.cn/language/javascript/2198.html)

通常情况下，我们会建立如下情况的路由：

```jsx
<Router>
  <Route path="/" component={App}>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

其中 App 组件一般情况下是一个 layout，比如包含了 header、footer 或者其他内容，其下面的 route 会被嵌入到这个 App 中（它们将成为 App 的
children），但这样配置路由有一个问题，就是我们访问 http://localhost:3000/ 这个地址时，你会发现仅渲染了一个 App 的 layout 内容，Accounts 和 Statements 都没有被渲染，这种情况下我们一般会设置一个默认页，当访问 / 这个路由时显示这个默认页。由此就需要用到 IndexRoute 功能，修改一下路由如下所示：

```jsx
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

如此配置后，我们再次访问 / 路由，你会发现页面渲染了 Home 组件的内容。这就是 IndexRoute 的功能，指定一个路由的默认页。





### IndexRedirect

上面这种情况比较常见，还有一种非常常见的方式就是当我们尝试访问 / 这个路由时，我们想让其直接跳转到 ‘/Accounts’，直接免去了默认页 Home，这样来的更加直接。由此我们就需要 IndexRedirect 功能。考虑如下路由：

```
<Router>
  <Route path="/" component={App}>
    <IndexRedirect to="/accounts"/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

这样设计路由后，我们再次访问 / 时，系统默认会跳转到 /accounts 路由。

---



* **IndexRoute 一般情况下用于设计一个默认页且不改变 URL 地址，而 IndexRedirect 则是跳转默认地址且地址会发生改变。**
* **IndexRoute 指定一个组件作为默认页，而 IndexRedirect 指定一个路由地址作为跳转地址。**





### 嵌套路由

[http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu](http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu)
Route组件还可以嵌套。

```
    <Router history={hashHistory}>
      <Route path="/" component={App}>
        <Route path="/repos" component={Repos}/>
        <Route path="/about" component={About}/>
      </Route>
    </Router>
```
上面代码中，用户访问/repos时，会先加载App组件，然后在它的内部再加载Repos组件。
```
<App>
  <Repos/>
</App>
```
App组件要写成下面的样子。

```
    export default React.createClass({
      render() {
        return <div>
          {this.props.children}
        </div>
      }
    })
```
上面代码中，App组件的this.props.children属性就是子组件。


子路由也可以不写在Router组件里面，单独传入Router组件的routes属性。

```
    let routes = <Route path="/" component={App}>
      <Route path="/repos" component={Repos}/>
      <Route path="/about" component={About}/>
    </Route>;

    <Router routes={routes} history={browserHistory}/>
```

### 通配符
path属性可以使用通配符。

```
    <Route path="/hello/:name">
    // 匹配 /hello/michael
    // 匹配 /hello/ryan

    <Route path="/hello(/:name)">
    // 匹配 /hello
    // 匹配 /hello/michael
    // 匹配 /hello/ryan

    <Route path="/files/*.*">
    // 匹配 /files/hello.jpg
    // 匹配 /files/hello.html

    <Route path="/files/*">
    // 匹配 /files/ 
    // 匹配 /files/a
    // 匹配 /files/a/b

    <Route path="/**/*.jpg">
    // 匹配 /files/hello.jpg
    // 匹配 /files/path/to/file.jpg
```



1. :paramName匹配URL的一个部分，直到遇到下一个/、?、#为止。这个路径参数可以通过this.props.params.paramName取出。
2. ()表示URL的这个部分是可选的。
3. *匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。
4. ** 匹配任意字符，直到下一个/、?、#为止。匹配方式是贪婪模式。

**URL的查询字符串/foo?bar=baz，可以用this.props.location.query.bar获取。**





### histroy 属性
Router组件的history属性，用来监听浏览器地址栏的变化，并将URL解析成一个地址对象，供 React Router 匹配。

history属性，一共可以设置三种值。


* browserHistory
* hashHistory
* createMemoryHistory

如果设为hashHistory，路由将通过URL的hash部分（#）切换，URL的形式类似`example.com/#/some/path`。

```
import { hashHistory } from 'react-router'

render(
  <Router history={hashHistory} routes={routes} />,
  document.getElementById('app')
)
```


如果设为browserHistory，浏览器的路由就不再通过Hash完成了，而显示正常的路径`example.com/some/path`，背后调用的是浏览器的History API。


```
import { browserHistory } from 'react-router'

render(
  <Router history={browserHistory} routes={routes} />,
  document.getElementById('app')
)
```

createMemoryHistory主要用于服务器渲染。它创建一个内存中的history对象，不与浏览器URL互动。






