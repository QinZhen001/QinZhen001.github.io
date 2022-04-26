---
layout:     post
title:      "electron相关"
date:       2021-04-05 14:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Electron 
---

> “Yeah It's on. ”
>

# 正文

[https://www.electronjs.org/](https://www.electronjs.org/)

electron 基于 node 和 chromium 做 js 逻辑的执行和页面渲染，并且提供了 BrowserWindow 来创建窗口，提供了 electron 包的 api，来执行一些操作系统的功能，比如打开文件选择窗口、进程通信等。



每个 BrowserWindow 窗口内的 js 都跑在一个渲染进程，而 electron 有一个主进程，负责和各个窗口内的渲染进程通信。



![]( https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5b5273020e34a26babf387576a62773~tplv-k3u1fbpfcp-watermark.image )



缺点：性能比原生桌面应用要低，最终打包后的应用比原生应用大很多。





## Web Embeds

[https://www.electronjs.org/docs/tutorial/web-embeds](https://www.electronjs.org/docs/tutorial/web-embeds)

有三种方式可以让你在Electron的BrowserWindow里集成（第三方）web内容，`<iframe>`, `<webview>` 和 `BrowserViews`每个功能都略有不同，适用于不同的情况。 为了帮助您在这些选择之间进行选择，本指南将解释他们之间的差异和功能。





## 打包工具

[electron-builder vs electron-forge 下载量分析](https://www.npmtrends.com/electron-builder-vs-electron-forge-vs-electron-packager)

[electron-builder vs electron-forge 讨论](https://github.com/electron-userland/electron-builder/issues/1193)



* electron-builder it is a tool to build your Electron application.

* electron-forge it is a tool to build your Electron application PLUS boilerplate to create electron app.

> `electron-forge` was built to provide a wider scope than `electron-builder` ever intended to as it's goal is not just to build Electron application but to help you from the moment you create your app to the moment you want to publish it. Our main goal was to make a tool that used community packages that we already used and wire them up in an intuitive and easy to use way so that **anyone** could just jump right in to Electron development without having to worry about setting up build files and such.







### Electron Forge

> 它更突出整合

[https://www.electronforge.io/](https://www.electronforge.io/)

Electron Forge is a complete tool for creating, publishing, and installing modern Electron applications.

Electron Forge 是一个用来构建现代化Electron应用的完善的工具。 Electron Forge将多个现有的（ 且有稳定维护的 ）Electron构建工具整合为一个简单易用的工具包，所有人都可以用它来快速地搭建Electron开发环境。



### [electron-builder](https://www.electronjs.org/docs/tutorial/boilerplates-and-clis#electron-builder)

[https://www.electron.build/](https://www.electron.build/)

Electron Builder 是一个完备的Electron应用打包和分发解决方案，它致力于软件开发的集成体验。 [`electron-builder`](https://github.com/electron-userland/electron-builder) 出于简化的目的添加了一个依赖项，可以在内部管理所有更多的要求。

`electron-builder` 会将Electron维护者使用的模块和功能(例如: auto-updater) 替换为自定义的. Electron Builder打包的应用内组件的集成度会更高，同时与主流的Electron应用共同点也就更少了。







## 调试渲染进程

[https://www.electronjs.org/zh/docs/latest/tutorial/application-debugging](https://www.electronjs.org/zh/docs/latest/tutorial/application-debugging)

最广泛使用来调试指定渲染进程的工具是Chromium的开发者工具集。 它可以获取到所有的渲染进程，包括`BrowserWindow`的实例，`BrowserView`以及`WebView`。

```tsx
const { BrowserWindow } = require('electron')

const win = new BrowserWindow()
win.webContents.openDevTools()
```





## 主进程和渲染进程

主进程(Main Process)

- 应用启动时，会创建个主进程
- 一个应用有且只有一个主进程
- 只有主进程可以进行 `GUI` 的 API 操作，即调用 `Native APIs`

渲染进程(Renderer Process)

* Windows 中展示的界面通过渲染进程表现，DOM 操作 

- 一个应用可以有多个渲染进程
- 要通过主进程才可以访问原生 API(Native APIs)，要先跟主进程进行 ipc 通信







## 进程通信 

[https://www.electronjs.org/zh/docs/latest/api/ipc-main](https://www.electronjs.org/zh/docs/latest/api/ipc-main)

`electron`中主进程和渲染进程两者之间需要通信

**主线程** 到 **渲染线程** 通过 `webContents.send` 来发送 --->`ipcRenderer.on` 来监听

**渲染线程** 到 **主线程** 需要通过 `ipcRenderer.send`发送 ---> `ipcMain.on`来监听



### MessagePort

[https://www.electronjs.org/zh/docs/latest/tutorial/message-ports](https://www.electronjs.org/zh/docs/latest/tutorial/message-ports)

[`MessagePort`](https://developer.mozilla.org/en-US/docs/Web/API/MessagePort)是一个允许在不同上下文之间传递消息的Web功能。 就像 `window.postMessage`, 但是在不同的通道上。 





## 集成第三方web内容

[https://www.electronjs.org/zh/docs/latest/tutorial/web-embeds](https://www.electronjs.org/zh/docs/latest/tutorial/web-embeds)

有三种方式可以让你在Electron的`BrowserWindow`里集成（第三方）web内容，`<iframe>` 和, `<webview>` 和 `BrowserViews` 每个功能都略有不同，适用于不同的情况。







## BrowserView

[https://www.electronjs.org/zh/docs/latest/api/browser-view](https://www.electronjs.org/zh/docs/latest/api/browser-view)

[BrowserViews](https://www.electronjs.org/zh/docs/latest/api/browser-view) 不是 DOM 的一部分，而是由主进程创建和控制。 它们只是现有窗口之上的另一层 Web 内容。 这意味着它们与您自己的 `BrowserWindow` 内容完全分离，并且它们的位置不受 DOM 或 CSS 的控制，而是通过在主进程中设置边界来控制其位置。 相反，它通过在主进程中设置界面来控制 。



### webPreferences



#### contextIsolation

`contextIsolation` Boolean (可选) - 是否在独立 JavaScript 环境中运行 Electron API和指定的`preload` 脚本. 默认为 `true`。 `预加载`脚本所运行的上下文环境只能访问其自身专用的`文档`和全局`窗口`，其自身一系列内置的JavaScript (`Array`, `Object`, `JSON`, 等等) 也是如此，这些对于已加载的内容都是不可见的。 Electron API 将只在`预加载`脚本中可用，在已加载页面中不可用。 这个选项应被用于加载可能不被信任的远程内容时来确保加载的内容无法篡改`预加载`脚本和任何正在使用的Electron api。 该选项使用的是与[Chrome内容脚本](https://developer.chrome.com/extensions/content_scripts#execution-environment)相同的技术。 你可以在开发者工具Console选项卡内顶部组合框中选择 'Electron Isolated Context'条目来访问这个上下文。









## 上下文隔离

[https://www.electronjs.org/zh/docs/latest/tutorial/context-isolation](https://www.electronjs.org/zh/docs/latest/tutorial/context-isolation)

上下文隔离功能将确保您的 `预加载`脚本 和 Electron的内部逻辑 运行在所加载的 [`webcontent`](https://www.electronjs.org/zh/docs/latest/api/web-contents)网页 之外的另一个独立的上下文环境里。 这对安全性很重要，因为它有助于阻止网站访问 Electron 的内部组件 和 您的预加载脚本可访问的高等级权限的API 。





 ## preload script

Preload scripts contain code that executes in a renderer process before its web contents begin loading. These scripts run within the renderer context, but are granted more privileges by having access to Node.js APIs.







# 补充 



## chromium

Chromium 是 [Google](https://baike.baidu.com/item/Google/86964) 的[Chrome](https://baike.baidu.com/item/Chrome/5633839)浏览器背后的引擎，其目的是为了创建一个安全、稳定和快速的通用浏览器。 



Chromium和Chrome所使用的webkit内核，是目前公认的最快的网页浏览方式。





## IPC

IPC（Inter-[Process](https://baike.baidu.com/item/Process/1170280)[ Communication](https://baike.baidu.com/item/ Communication/20394231)，[进程间通信](https://baike.baidu.com/item/进程间通信/1235923)）。进程间通信是指两个进程的数据之间产生交互

在Linux C编程中有几种方法

* 半双工Unix管道
* FIFOs(命名管道)
*  消息队列
*  信号量
* 共享内存
*  网络Socket



## vscode

vscode 是一个 electron 应用，窗口等功能的实现基于 electron

[vscode 是怎么跑起来的](https://juejin.cn/post/6987420993568374797)



## Electron 原理



### 通信原理

`ipcMain` 和 `ipcRenderer` 都是 `EventEmitter` 类的一个实例。`EventEmitter` 类是 `NodeJS` 事件的基础，它由 `NodeJS` 中的 `events` 模块导出。

我们通过 `ipcMain`和`ipcRenderer` 的 `on、send` 进行监听和发送消息都是 `EventEmitter` 定义的相关接口。



#### 渲染进程间通信

`Electron`并没有提供渲染进程之间相互通信的方式，我们可以在主进程中建立一个消息中转站。

渲染进程之间通信首先发送消息到主进程，主进程的中转站接收到消息后根据条件进行分发。



#### 渲染进程数据共享

在两个渲染进程间共享数据最简单的方法是使用浏览器中已经实现的` HTML5 API`。 其中比较好的方案是用` Storage API`， `localStorage，sessionStorage` 或者 `IndexedDB。`





### 渲染进程打包和升级

我们的大部分业务逻辑代码是在渲染进程完成的，在大部分情况下我们仅仅需要对渲染进程进行更新和升级而不需要改动主进程代码，我们渲染进程的打包实际上和一般的`web`项目打包没有太大差别，使用`webpack`打包即可。

打包完成的`html`和`js`文件，我们一般要上传到我们的前端静态资源服务器下，然后告知服务端我们的渲染进程有代码更新，这里可以说成渲染进程单独的升级。

和壳的升级不同，渲染进程的升级仅仅是静态资源服务器上`html`和`js`文件的更新，而不需要重新下载更新客户端，这样我们每次启动程序的时候检测到离线包有更新，即可直接刷新读取最新版本的静态资源文件，即使在程序运行过程中要强制更新，我们的程序只需要强制刷新页面读取最新的静态资源即可，这样的升级对用户是非常友好的。

这里注意，一旦我们这样配置，就意味着渲染进程和主进程打包升级的完全分离，我们在启动主窗口时读取的文件就不应该再是本地文件，而是打包完成后放在静态资源服务器的文件。







### 主进程打包

主进程，即将整个程序打包成可运行的客户端程序，常用的打包方案一般有两种，`electron-packager`和`electron-builder`。

这里我推荐使用`electron-builder`，它不仅拥有方便的配置 `protocol` 的功能、内置的 `Auto Update`、简单的配置 `package.json` 便能完成整个打包工作，用户体验非常不错。而且`electron-builder`不仅能直接将应用打包成`exe app`等可执行程序，还能打包成`msi dmg`等安装包格式。





### Node.js 集成到 Chromium

如何将 `Node.js` 和 `Chromiums` 整合？

`Node.js` 事件循环基于 [libuv](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Flibuv%2Flibuv)，但 `Chromium` 基于 [message_pump](https://link.juejin.cn?target=https%3A%2F%2Fchromium.googlesource.com%2Fchromium%2Fchromium%2F%2B%2Fmaster%2Fbase%2Fmessage_pump.h)。

解决这个问题的的主要思路有两种：

- 1.将 `Chromium` 集成到 `Node.js`：用 `libuv` 实现 `message_pump`。
- 2.将 `Node.js` 集成到 `Chromium` 。



第一种方案，[NW.js](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fnwjs%2Fnw.js) 就是这么做的。`Electron` 前期也是这样尝试的，结果发现在渲染进程里实现比较容易，但是在主进程里却很麻烦，因为各个系统的 `GUI` 实现都不同，`Mac` 是 `NSRunLoop`，`Linux` 是 `glib`，不仅工程量十分浩大，而且一些边界情况处理起来也十分棘手。



所以采取将 `Node.js` 集成到 `Chromium` 中：

`Electron` 起了一个新的安全线程去轮询 `backend_fd`，当 `Node.js` 有一个新的事件后，通过 `PostTask` 转发到 `Chromium` 的事件循环中，这样就实现了 `Electron` 的事件融合。

> `libuv` 引入了 `backend_fd` 的概念，相当于 `libuv` 轮询事件的文件描述符，这样就可以通过轮询 `backend_fd` 来得到 `libuv` 的一个新事件了







# 问题





## Error: Electron failed to install correctly

[https://zhuanlan.zhihu.com/p/108380451](https://zhuanlan.zhihu.com/p/108380451)

这个是由于Electron依赖安装失败



可以弄一个.yarnrc 或者 .npmrc

[https://yarn.bootcss.com/docs/yarnrc/](https://yarn.bootcss.com/docs/yarnrc/)



```js
ELECTRON_MIRROR=http://npm.taobao.org/mirrors/electron/
SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass
```



> 也可以全局config设置  （亲测有用）

npm config set ELECTRON_MIRROR http://npm.taobao.org/mirrors/electron/



## Tray 消失了

[Tray 消失了](https://www.electronjs.org/docs/faq#%E5%87%A0%E5%88%86%E9%92%9F%E5%90%8E%E6%88%91%E5%BA%94%E7%94%A8%E7%9A%84-tray-%E6%B6%88%E5%A4%B1%E4%BA%86%E3%80%82)

错误写法

```tsx
const { app, Tray } = require('electron')
app.whenReady().then(() => {
  const tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```

正确写法

```tsx
const { app, Tray } = require('electron')
let tray = null
app.whenReady().then(() => {
  tray = new Tray('/path/to/icon.png')
  tray.setTitle('hello world')
})
```





## require is not define

解决方案：

```tsx
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, "preload.js"),
      nodeIntegration: true,
      contextIsolation: false,
    },
  });
```

nodeIntegration 和 contextIsolation 都要设置缺一不可



- `nodeIntegration` Boolean (可选) - 是否启用Node integration. 默认值为 `false`.
- `contextIsolation` Boolean (可选) - 是否在独立 JavaScript 环境中运行 Electron API和指定的`preload` 脚本. 默认为 `true`。 `预加载`脚本所运行的上下文环境只能访问其自身专用的`文档`和全局`窗口`，其自身一系列内置的JavaScript (`Array`, `Object`, `JSON`, 等等) 也是如此，这些对于已加载的内容都是不可见的。 Electron API 将只在`预加载`脚本中可用，在已加载页面中不可用。





## 无法校验开发者

[What should I do about com.apple.quarantine?](https://superuser.com/questions/28384/what-should-i-do-about-com-apple-quarantine)

解决

```bash
xattr -dr com.apple.quarantine **/*
```







# 跨端的通用原理

[https://juejin.cn/post/6966626823912308772#heading-13](https://juejin.cn/post/6966626823912308772#heading-13)

其实跨端和跨平台的思路类似，都是实现一个容器，给它提供统一的 api，这套 api 由不同的平台各自实现，保证一致的功能。

具体一些的话，**跨端分为渲染和逻辑跨端**，有的时候只需要单独的渲染跨端方案（比如 karen）和逻辑跨端方案，有的时候需要完整的跨端引擎。

weex、react native 的渲染部分都是通过实现了 virtual dom 的渲染，用安卓、ios 各自的渲染方式实现，逻辑部分使用 js 引擎，通过 bridge 注入一些安卓、ios 的 api。

flutter 则是直接使用 skia 绘图库绘制，并且逻辑跨端使用 dart vm。

但是不管具体实现怎样，思路都大同小异：**跨端引擎需要实现一个渲染引擎、实现一个 vm，基于这套架构实现各种组件和 api，跨端容器上层对接一个 ui 框架，再上层的业务代码可以基于容器的 api 实现跨端的渲染和逻辑**


作者：zxg_神说要有光
链接：https://juejin.cn/post/6966626823912308772
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



