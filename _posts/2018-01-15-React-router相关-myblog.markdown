---
layout:     post
title:      "react-router相关"
date:       2018-01-15 13:00:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React
---

> “Yeah It's on. ”

# 正文



## BrowserRouter

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



## Link

[https://stackoverflow.com/questions/42914666/react-router-external-link](https://stackoverflow.com/questions/42914666/react-router-external-link)

With Link component of react-router you can do that. In the "**to**" prop you can specify 3 types of data:

- **a string**: A string representation of the Link location, created by concatenating the location’s pathname, search, and hash properties.

- an object

  : An object that can have any of the following properties:

  - **pathname**: A string representing the path to link to.
  - **search**: A string representation of query parameters.
  - **hash**: A hash to put in the URL, e.g. #a-hash.
  - **state**: State to persist to the location.

- **a function**: A function to which current location is passed as an argument and which should return location representation as a string or as an object





## Route

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



component

只有当访问地址和路由匹配时，一个 React component 才会被渲染，此时此组件接受 route props (match, location, history)。
当使用 component 时，router 将使用 React.createElement 根据给定的 component 创建一个新的 React 元素。这意味着如果你使用内联函数（inline function）传值给 component 将会产生不必要的重复装载。对于内联渲染（inline rendering）, 建议使用 render prop。

```tsx
<Route path="/user/:username" component={User} />
const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
```



render: func

此方法适用于内联渲染，而且不会产生上文说的重复装载问题。
```tsx
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



### IndexRoute

> TIP:  在 react-router-dom v6 中 IndexRoute 已经被干掉了，请用index属性代替
>
> https://reactrouter.com/en/main/route/route#index

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



## 嵌套路由

[http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu](http://www.ruanyifeng.com/blog/2016/05/react_router.html?utm_source=tool.lu)
Route组件还可以嵌套。

```tsx
    <Router history={hashHistory}>
      <Route path="/" component={App}>
        <Route path="/repos" component={Repos}/>
        <Route path="/about" component={About}/>
      </Route>
    </Router>
```
上面代码中，用户访问/repos时，会先加载App组件，然后在它的内部再加载Repos组件。
```tsx
<App>
  <Repos/>
</App>
```
App组件要写成下面的样子。

```tsx
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

```tsx
 let routes = <Route path="/" component={App}>
      <Route path="/repos" component={Repos}/>
      <Route path="/about" component={About}/>
    </Route>;

  <Router routes={routes} history={browserHistory}/>
```







## React-Router v6

[https://juejin.cn/post/6844904096059621389#heading-1](https://juejin.cn/post/6844904096059621389#heading-1)





### **简化路径**

**React Router v6使用简化的路径格，仅支持2种占位符：动态:`id`样式参数和`\*`通配符**

以下都是v6中的有效路由路径：

```tsx
/groups
/groups/admin
/users/:id
/users/:id/messages
/files/*
/files/:id/*
/files-*
```

使用`RegExp`正则匹配的路径将无效：

```
/users/:id?
/tweets/:id(\d+)
/files/*/cat.jpg
```



`v6`中的所有路径匹配都将忽略URL上的尾部"`/`"。实际上，`<Route strict>`已被删除并且在v6中无效。这并不意味着您不需要使用斜杠。



### Outlet

> React Router v6 出现，用于处理嵌套路由

[https://segmentfault.com/a/1190000023684163](https://segmentfault.com/a/1190000023684163)



例子：

```tsx
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="profile" element={<Profile />}>
          <Route path=":id" element={<MyProfile />} />
          <Route path="me" element={<OthersProfile />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

function Profile() {
  return (
    <div>
      <nav>
        <Link to="me">My Profile</Link>
      </nav>
        {/*
       将直接根据上面定义的不同路由参数，渲染<MyProfile />或<OthersProfile />
        */}
      <Outlet />
    </div>
  )
}
```









# 补充



## react-router 还是 react-router-dom？

[https://www.syncfusion.com/blogs/post/react-router-vs-react-router-dom.aspx](https://www.syncfusion.com/blogs/post/react-router-vs-react-router-dom.aspx)

在 React 的使用中，我们一般要引入两个包，react 和 react-dom，那么 react-router 和 react-router-dom 是不是两个都要引用呢？
非也，坑就在这里。他们两个只要引用一个就行了，不同之处就是后者比前者多出了 `<Link>` `<BrowserRouter>` 这样的 DOM 类组件。
因此我们**只需引用 react-router-dom 这个包**就行了。当然，如果搭配 redux ，你还需要使用 react-router-redux。



如果你只开发一个web应用，使用react-router-dom就足够了。

If you are working on a web application, the better option is to use **react-router-dom**, because it contains all the necessary common components and features essential for routing in a web application. **react-router-dom** installs **react-router** as a dependency, so there is no need to re-install and import anything directly from the **react-router**.



## 滚动至顶部

[https://juejin.cn/post/6844904093694033927#heading-5](https://juejin.cn/post/6844904093694033927#heading-5)

在大多数情况下，您所需要做的只是“滚动到顶部”，因为您有一个较长的内容页面，该页面在导航到该页面时始终保持向下滚动。使用`<ScrollToTop>`组件可以轻松处理此问题，该组件将在每次导航时向上滚动窗口：



```tsx
import { useEffect } from "react";
import { useLocation } from "react-router-dom";

export default function ScrollToTop() {
  const { pathname } = useLocation();

  useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);

  return null;
}
```

然后将其放在您应用的顶部。

```tsx
function App() {
  return (
    <Router>
      <ScrollToTop />
      <App />
    </Router>
  );
}
```





## Switch和Routes

React中的Switch和Routes都是React Router库中的组件，用于路由控制和页面切换。

Switch组件是用于包裹Routes组件的，它的作用是当第一个匹配到URL的Route被发现后，就停止查找匹配并只渲染该Route对应的组件。换句话说，Switch只渲染第一个匹配到的Route组件。

Routes组件则用于定义路由规则，它包含了多个Route组件，并根据URL匹配到的路径渲染对应的组件。Routes会逐个匹配路径，直到找到与URL匹配的路径为止。

**总结起来，Switch是用来选择要渲染的Route组件的，而Routes则是用来定义路由规则的。Switch只渲染第一个匹配到的Route组件，并忽略后续的匹配，而Routes则按照定义的顺序逐个匹配路径，直到找到与URL匹配的路径为止。**



In *react-router-dom* v6, "Switch" is replaced by routes "Routes"

[https://stackoverflow.com/questions/63124161/attempted-import-error-switch-is-not-exported-from-react-router-dom](https://stackoverflow.com/questions/63124161/attempted-import-error-switch-is-not-exported-from-react-router-dom)





# 问题



## location.hash after 6.7.0

[https://github.com/remix-run/react-router/discussions/9973](https://github.com/remix-run/react-router/discussions/9973)
