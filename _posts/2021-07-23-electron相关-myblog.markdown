---
layout:     post
title:      "electron相关"
date:       2021-04-05 14:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - electron
---

> “Yeah It's on. ”
>

# 正文

[https://www.electronjs.org/](https://www.electronjs.org/)

electron 基于 node 和 chromium 做 js 逻辑的执行和页面渲染，并且提供了 BrowserWindow 来创建窗口，提供了 electron 包的 api，来执行一些操作系统的功能，比如打开文件选择窗口、进程通信等。



每个 BrowserWindow 窗口内的 js 都跑在一个渲染进程，而 electron 有一个主进程，负责和各个窗口内的渲染进程通信。



![]( https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5b5273020e34a26babf387576a62773~tplv-k3u1fbpfcp-watermark.image )





## Web Embeds

[https://www.electronjs.org/docs/tutorial/web-embeds](https://www.electronjs.org/docs/tutorial/web-embeds)

有三种方式可以让你在Electron的BrowserWindow里集成（第三方）web内容，`<iframe>`, `<webview>` 和 `BrowserViews`每个功能都略有不同，适用于不同的情况。 为了帮助您在这些选择之间进行选择，本指南将解释他们之间的差异和功能。





## 打包工具

### Electron Forge

[https://www.electronforge.io/](https://www.electronforge.io/)

Electron Forge is a complete tool for creating, publishing, and installing modern Electron applications.

Electron Forge 是一个用来构建现代化Electron应用的完善的工具。 Electron Forge将多个现有的（ 且有稳定维护的 ）Electron构建工具整合为一个简单易用的工具包，所有人都可以用它来快速地搭建Electron开发环境。



### [electron-builder](https://www.electronjs.org/docs/tutorial/boilerplates-and-clis#electron-builder)

Electron Builder 是一个完备的Electron应用打包和分发解决方案，它致力于软件开发的集成体验。 [`electron-builder`](https://github.com/electron-userland/electron-builder) 出于简化的目的添加了一个依赖项，可以在内部管理所有更多的要求。

`electron-builder` 会将Electron维护者使用的模块和功能(例如: auto-updater) 替换为自定义的. Electron Builder打包的应用内组件的集成度会更高，同时与主流的Electron应用共同点也就更少了。







## 调试渲染进程

```tsx
  // Open the DevTools.
  mainWindow.webContents.openDevTools();
```





## 进程通信 

`electron`中主进程和渲染进程两者之间需要通信

**主线程** 到 **渲染线程** 通过 `webContents.send` 来发送 --->`ipcRenderer.on` 来监听

**渲染线程** 到 **主线程** 需要通过 `ipcRenderer.send`发送 ---> `ipcMain.on`来监听



### ipcRenderer

[https://www.electronjs.org/docs/api/ipc-renderer](https://www.electronjs.org/docs/api/ipc-renderer)

`ipcRenderer` 是一个 [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) 的实例。 你可以使用它提供的一些方法从渲染进程 (web 页面) 发送同步或异步的消息到主进程。 也可以接收主进程回复的消息。







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



