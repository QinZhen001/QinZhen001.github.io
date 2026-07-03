---
layout:     post
title:      "React Native详解"
date:       2026-07-01 23:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - React Native
---

> “Yeah It's on. ”

官方文档：https://reactnative.dev/

## React Native 是什么

React Native 是 Meta 推出的跨平台原生应用开发框架，核心 slogan 是：

> Learn once, write anywhere.

它不是把网页塞进 WebView，也不是简单地“写一次，到处完全一样运行”。更准确地说：

> React Native 把 React 的声明式 UI、组件化、状态驱动视图模型带到原生应用开发中，让开发者用 JavaScript / TypeScript 编写业务和界面逻辑，最终渲染为 iOS、Android 等平台的原生 UI。

官方首页的描述是：

> Written in JavaScript, rendered with native code.

也就是：

- 开发层使用 JavaScript / TypeScript 和 React；
- UI 抽象层使用 React Native 组件，例如 `View`、`Text`、`Image`；
- 底层渲染到平台原生视图，例如 iOS 的 `UIView`、Android 的 `ViewGroup` / `TextView`；
- 原生能力通过 Native Module / TurboModule 暴露给 JS 使用。

## 最新状态

截至 2026 年中，React Native 的主线变化主要集中在：

1. **New Architecture 成为默认方向**
   - React Native 0.76 开始，新架构默认启用。
   - 后续版本持续移除 Legacy Architecture。
   - React Native 0.82 被官方博客描述为首个完全运行在 New Architecture 上的版本。

2. **Fabric 成为渲染核心**
   - Fabric 是新架构下的渲染器。
   - 它用 C++ 统一更多跨平台渲染逻辑，并改善与原生 Host Platform 的互操作。

3. **TurboModules + JSI 替代传统异步 Bridge**
   - 旧架构依赖异步 Bridge 和序列化通信。
   - 新架构通过 JSI 让 JS 和 C++ / Native 可以互相持有引用，减少序列化成本。

4. **Hermes 持续成为默认 JS 引擎重点**
   - Hermes 是 React Native 默认推荐的 JavaScript 引擎。
   - 官方博客中，React Native 0.84 提到 Hermes V1 成为默认 JS 引擎。

5. **发布节奏更稳定**
   - 官方 Releases Overview 说明 React Native 大约每两个月发布一个 minor 版本。
   - 通常每年约 6 个 minor 版本。
   - 官方维护最近 3 个 minor 系列。

## 为什么需要 React Native

移动端开发长期存在几个核心矛盾：

1. **双端开发成本高**
   - iOS 用 Swift / Objective-C；
   - Android 用 Kotlin / Java；
   - 同一套业务逻辑和 UI 经常要写两遍。

2. **Web 跨平台体验不够原生**
   - WebView 方案开发快，但复杂交互、动画、原生能力、性能和系统体验往往不如原生。

3. **纯原生体验好但迭代慢**
   - 需要分别发版；
   - 业务团队需要维护双端逻辑一致性；
   - 人力成本高。

React Native 的价值就在于：

- 复用 React 技术栈；
- 复用大量 JS / TS 业务逻辑；
- 仍然渲染原生 UI；
- 可以在必要时写原生模块兜底；
- 对已有原生 App 也支持渐进式接入。

## 核心特点

### 1. 声明式 UI

React Native 继承 React 的声明式 UI 模型。

```tsx
function HomeScreen() {
  return (
    <View>
      <Text>Hello React Native</Text>
    </View>
  )
}
```

开发者描述“界面应该是什么样”，React 负责根据状态变化计算更新。

### 2. 组件化

React Native 应用由组件组成。

```tsx
function UserCard({ name, avatar }) {
  return (
    <View>
      <Image source={{ uri: avatar }} />
      <Text>{name}</Text>
    </View>
  )
}
```

组件可以管理自己的状态，也可以组合成更复杂的页面。

### 3. 原生 UI 渲染

`View`、`Text`、`Image` 等不是 DOM 节点，而是 React Native 的 Host Component。

大致映射关系：

```text
JSX
  ↓
React Element Tree
  ↓
React Shadow Tree
  ↓
Host View Tree
  ↓
iOS / Android 原生视图
```

### 4. 支持热更新式开发体验

React Native 的 JS 代码可以通过 Metro Bundler 在开发阶段快速刷新，很多 JS / 样式改动不需要重新编译整个原生工程；在生产环境中，也可以通过 OTA 机制更新 JS bundle 和资源文件，但不能更新原生二进制能力。

### 5. 可访问原生能力

如果 JS 层能力不够，可以通过：

- Native Module；
- TurboModule；
- Native Component；
- Fabric Component；
- JSI C++ 模块；

接入平台原生 API。

## 支持平台

React Native 官方核心目标主要是：

- iOS；
- Android。

社区和公司生态扩展了更多平台：

- React Native Windows；
- React Native macOS；
- React Native Web；
- React Native for TV；
- React Native on Meta Quest。

React Native 的思想不是完全消灭平台差异，而是允许在统一 React 模型下，为不同平台写少量差异化代码。

例如：

```text
Button.ios.tsx
Button.android.tsx
```

或者：

```tsx
import { Platform } from 'react-native'

const paddingTop = Platform.OS === 'ios' ? 44 : 24
```

## 官方推荐：新项目优先考虑 Expo

React Native 官方首页明确建议：

> To build a new app with React Native, we recommend a framework like Expo.

React Native Core 主要解决“用 React 渲染原生 UI”和“JS / Native 互操作”；而路由、构建发布、OTA、原生配置、证书签名、商店提交、常用原生能力封装等工程问题，通常需要额外工具链。

Expo 不是替代 React Native 的另一套渲染引擎，而是在 React Native 之上补齐应用框架和工程化能力：

```text
React Native Core = 跨平台原生渲染和运行时基础设施
Expo = 基于 React Native 的完整应用开发框架
```

如果是新项目，优先考虑 Expo；如果是已有原生工程、强定制原生能力或大型复杂 App，可以选择 React Native CLI / Bare Workflow。Expo 的详细工程化原理放在后文“工程化与选型”里展开。

## 整体架构

React Native 可以分成几层：

```text
┌──────────────────────────────────────┐
│ App Code                              │
│ React / JSX / TS / State / Navigation │
├──────────────────────────────────────┤
│ React Native JS Layer                 │
│ View / Text / Image / AppRegistry     │
├──────────────────────────────────────┤
│ Renderer / Runtime                    │
│ Fabric / TurboModules / JSI / Hermes  │
├──────────────────────────────────────┤
│ C++ Core                              │
│ Shadow Tree / Yoga / Scheduler        │
├──────────────────────────────────────┤
│ Host Platform                         │
│ iOS UIKit / Android View System       │
└──────────────────────────────────────┘
```

关键组件：

| 模块 | 作用 |
| --- | --- |
| `Metro` | JS 打包器，负责打包 React Native 代码 |
| `Hermes` | 面向 React Native 优化的 JavaScript 引擎 |
| `JSI` | JavaScript Interface，连接 JS 与 C++ / Native 的接口层 |
| `Fabric` | 新架构渲染器，负责将 React 树渲染到原生视图 |
| `TurboModules` | 新原生模块系统，替代传统 NativeModules 的主要方向 |
| `Codegen` | 根据类型声明生成跨语言绑定代码，提高类型安全和性能 |
| `Yoga` | 跨平台布局引擎，负责 Flexbox 布局计算 |

## 旧架构的问题

旧架构中，JS 和 Native 之间主要通过异步 Bridge 通信。

简化模型：

```text
JavaScript Thread
  ↓ JSON 序列化
Bridge
  ↓ JSON 反序列化
Native Thread
```

这种设计的问题：

1. **异步通信导致某些场景不够及时**
   - JS 调 Native 是异步；
   - Native 返回 JS 也是异步；
   - 布局测量、手势、高频动画容易出现延迟。

2. **序列化成本高**
   - JS 对象需要序列化成跨 Bridge 消息；
   - Native 侧再反序列化；
   - 高频、大数据量场景成本明显。

3. **类型安全弱**
   - JS 和 Native 方法参数不匹配，很多问题只有运行时才发现。

4. **新 React 能力受限**
   - React 18 的 Concurrent Features、Transitions、Suspense 等能力需要底层渲染器支持。

## New Architecture

React Native New Architecture 是对 React Native 核心内部机制的重构。

官方文档中提到，从 2018 年开始，React Native 团队就在重新设计核心内部结构；到 2024 年，新架构已经在 Meta 的生产应用中经过规模验证。

新架构主要包括：

```text
New Architecture
  ├── JSI
  ├── Fabric Renderer
  ├── TurboModules
  └── Codegen
```

### JSI

JSI 全称是 JavaScript Interface。

它的关键点是：

> 允许 JavaScript 持有 C++ 对象引用，也允许 C++ 持有 JavaScript 对象引用，从而直接调用方法，减少传统 Bridge 的序列化成本。

旧架构：

```text
JS Object → JSON → Bridge → Native Object
```

新架构：

```text
JS Runtime ↔ JSI ↔ C++ Object / Native Object
```

这对高频、大数据量场景非常关键，例如：

- 相机帧处理；
- 音视频处理；
- 图像处理；
- 数据库；
- 加密计算；
- 动画和手势。

官方文档举了 VisionCamera 的例子：典型帧缓冲可能达到每帧约 30MB，高帧率下每秒数据量巨大。如果走传统 Bridge 序列化，成本会非常高；JSI 更适合这类场景。

### Fabric

Fabric 是新架构下的渲染系统。

它的目标是：

- 用 C++ 统一更多渲染逻辑；
- 改善 React Native 与 Host Platform 的互操作；
- 支持同步布局测量；
- 支持 React 18+ 的 Concurrent Renderer；
- 提供更好的跨平台一致性；
- 通过 View Flattening 等机制减少不必要原生视图。

Fabric 的渲染核心可以简化为：

```text
React Element Tree
  ↓ Render
React Shadow Tree
  ↓ Commit + Yoga Layout
Next Shadow Tree
  ↓ Mount + Diff
Host View Tree
```

### TurboModules

TurboModules 是新架构下的原生模块系统。

它相比旧 `NativeModules` 的改进：

- 懒加载模块；
- 更快的 JS / Native 调用；
- 更好的类型约束；
- 通过 Codegen 生成绑定代码；
- 更适合 JSI 直接调用模型。

### Codegen

Codegen 根据 JS / TS / Flow 类型声明生成 Native 绑定代码。

它解决的问题是：

- JS 和 Native API 的类型一致性；
- 减少手写桥接代码；
- 在构建期发现接口不匹配；
- 给 Fabric Native Component 和 TurboModule 提供统一声明方式。

### 带来的能力

#### 1. 同步布局和副作用

旧架构中，测量布局常用 `onLayout`：

```tsx
function Demo() {
  const [rect, setRect] = useState(null)

  return (
    <View
      onLayout={event => {
        setRect(event.nativeEvent.layout)
      }}
    />
  )
}
```

问题是：`onLayout` 回调里的状态更新可能发生在前一次渲染已经绘制之后，用户可能看到中间状态或视觉跳动。

**新架构支持更同步的布局测量和调度，可以配合 `useLayoutEffect` 在同一个 commit 中完成测量和更新，减少 tooltip、popover、浮层定位等场景的跳动。**

#### 2. 支持 Concurrent Renderer

新架构支持 React 18 及之后的并发渲染能力，例如：

- automatic batching；
- `startTransition`；
- Suspense；
- 更细粒度的渲染优先级。

示例：

```tsx
const [isPending, startTransition] = useTransition()

function onChange(value) {
  startTransition(() => {
    setValue(value)
  })
}
```

这类能力可以让低优先级 UI 更新被打断，从而优先响应用户输入。

#### 3. 更快的 JS / Native 互操作

传统 Bridge 的序列化模型不适合高频调用。

JSI 之后，JS 和 Native 可以通过引用直接互操作，减少 JSON 序列化和拷贝。

#### 4. 更好的类型安全

Codegen 让 JS 声明成为接口事实来源，然后生成 Native 侧代码。

如果 JS props 和 Native props 不匹配，可以在构建阶段暴露问题。

## 渲染流程：Render、Commit、Mount

官方架构文档把 React Native 新架构的渲染流程分成三个阶段。

### 1. Render 阶段

React 执行业务逻辑，生成 React Element Tree。

```tsx
function MyComponent() {
  return (
    <View>
      <Text>Hello, World</Text>
    </View>
  )
}
```

React 会递归执行组件，直到得到 Host Component：

```text
<MyComponent />
  ↓
<View>
  <Text>Hello, World</Text>
</View>
```

在 Fabric 中，Renderer 会同步创建对应的 React Shadow Node：

```text
View  → ViewShadowNode
Text  → TextShadowNode
```

注意：`MyComponent` 这种用户自定义组件不会直接对应 Shadow Node，只有 Host Component 才会。

### 2. Commit 阶段

Commit 阶段主要做两件事：

1. **Layout Calculation**
   - 调用 Yoga 计算每个 Shadow Node 的位置和尺寸；
   - 布局输入来自 JS 侧 props / style；
   - 某些组件如 `Text`、`TextInput` 的测量依赖平台能力。

2. **Tree Promotion**
   - 将新的 Shadow Tree 提升为 next tree；
   - 表示这棵树已经具备挂载到屏幕所需的信息。

### 3. Mount 阶段

Mount 阶段将带有布局信息的 Shadow Tree 转换成 Host View Tree。

以 Android 为例：

```text
<View> → android.view.ViewGroup
<Text> → android.widget.TextView
```

以 iOS 为例：

```text
<View> → UIView
<Text> → UILabel / Text 相关原生实现
```

Mount 阶段又可以拆成：

1. **Tree Diffing**
   - 在 C++ 中比较 previously rendered tree 和 next tree；
   - 生成原子 mutation 操作，例如 `createView`、`updateView`、`removeView`、`deleteView`。

2. **Tree Promotion**
   - 将 next tree 原子提升为 rendered tree。

3. **View Mounting**
   - 在 UI thread 上把 mutation 操作应用到原生视图。

整体流程：

```text
React Element Tree
  ↓ render
React Shadow Tree
  ↓ layout + commit
Next Shadow Tree
  ↓ diff
Mutation List
  ↓ mount on UI Thread
Host View Tree
```

## 线程模型

React Native 新架构的 Renderer 主要涉及：

- JavaScript thread；
- UI thread / main thread；
- 部分后台线程或 C++ 执行上下文。

### JavaScript thread

主要负责：

- 执行 JS 代码；
- 执行 React render phase；
- 处理大部分组件逻辑；
- 触发状态更新。

### UI thread

主要负责：

- 操作平台原生视图；
- 处理用户输入、手势、滚动等高优先级事件；
- 执行 mount 阶段的原生视图更新。

**官方文档强调：只有 UI thread 可以操作 Host Views。**

### 高优先级事件

新架构中，Renderer 是 thread-safe 的，可以通过 immutable data structures 和 C++ const correctness 实现线程安全。

当 UI thread 上出现高优先级离散事件时，当前 JS render 可以被中断，React Native 可以把更新切到 UI thread 同步执行，以保证交互响应。

简化理解：

```text
低优先级更新：继续在 JS thread 上调度
高优先级事件：可中断当前 render，并在 UI thread 同步处理
```

## 源码分析

### 启动流程

React Native 应用入口通常长这样：

```tsx
import { AppRegistry } from 'react-native'
import App from './App'
import { name as appName } from './app.json'

AppRegistry.registerComponent(appName, () => App)
```

关键源码在：

```text
packages/react-native/Libraries/ReactNative/AppRegistryImpl.js
```

#### registerComponent

源码核心逻辑可以简化为：

```js
const runnables = {}

export function registerComponent(appKey, componentProvider) {
  runnables[appKey] = (appParameters, displayMode) => {
    const renderApplication = require('./renderApplication').default

    renderApplication({
      RootComponent: componentProvider(),
      initialProps: appParameters.initialProps,
      rootTag: appParameters.rootTag,
      debugName: appKey,
      displayMode,
    })
  }

  return appKey
}
```

也就是说，`registerComponent` 并不会立刻渲染 App，而是把一个 `runnable` 存起来。

#### runApplication

当 Native 侧准备好根视图之后，会调用：

```js
AppRegistry.runApplication(appKey, appParameters)
```

源码核心逻辑可以简化为：

```js
export function runApplication(appKey, appParameters, displayMode) {
  invariant(runnables[appKey], 'app has not been registered')

  SceneTracker.setActiveScene({ name: appKey })
  runnables[appKey](appParameters, coerceDisplayMode(displayMode))
}
```

因此启动链路是：

```text
JS 入口文件
  ↓
AppRegistry.registerComponent(appName, () => App)
  ↓
保存 runnables[appName]
  ↓
Native 创建 RootView
  ↓
Native 调用 runApplication(appName, { rootTag, initialProps })
  ↓
runnables[appName]()
  ↓
renderApplication()
```

### renderApplication

关键源码：

```text
packages/react-native/Libraries/ReactNative/renderApplication.js
```

核心逻辑可以简化为：

```tsx
export default function renderApplication({
  RootComponent,
  initialProps,
  rootTag,
  WrapperComponent,
  rootViewStyle,
  isLogBox,
  debugName,
  displayMode,
}) {
  let renderable = (
    <AppContainer
      rootTag={rootTag}
      WrapperComponent={WrapperComponent}
      rootViewStyle={rootViewStyle}
      initialProps={initialProps ?? Object.freeze({})}
      internal_excludeLogBox={isLogBox}>
      <RootComponent {...initialProps} rootTag={rootTag} />
    </AppContainer>
  )

  Renderer.renderElement({
    element: renderable,
    rootTag: createRootTag(rootTag),
  })
}
```

这里做了几件事：

1. 包一层 `AppContainer`；
2. 把 `initialProps` 和 `rootTag` 传给根组件；
3. 调用 `Renderer.renderElement` 进入渲染器。

链路继续变成：

```text
renderApplication
  ↓
<AppContainer><RootComponent /></AppContainer>
  ↓
Renderer.renderElement
```

### RendererImplementation

关键源码：

```text
packages/react-native/Libraries/ReactNative/RendererImplementation.js
```

当前主线代码中，`RendererImplementation` 已经非常薄，核心是调用 Fabric：

```js
import ReactFabric from '../Renderer/shims/ReactFabric'

export function renderElement({ element, rootTag }) {
  ReactFabric.render(
    element,
    Number(rootTag),
    null,
    true,
    {
      onCaughtError,
      onUncaughtError,
      onRecoverableError,
    },
  )
}
```

注意这里的第四个参数：

```js
true // useConcurrentRoot
```

这说明当前入口默认使用 Concurrent Root。

渲染入口链路可以总结为：

```text
AppRegistry.runApplication
  ↓
renderApplication
  ↓
Renderer.renderElement
  ↓
ReactFabric.render
  ↓
Fabric Renderer
```

这也反映了新版本 React Native 的方向：

> JS 入口层仍然是 React 风格，但底层渲染入口已经固定走 Fabric Renderer。

### View 如何映射到原生组件

React Native 中我们写：

```tsx
<View style={{ flex: 1 }} />
```

它最终不是 DOM，而是 Host Component。

关键源码：

```text
packages/react-native/Libraries/Components/View/ViewNativeComponent.js
```

核心逻辑可以简化为：

```js
import { type ViewProps as Props } from './ViewPropTypes'
import NativeComponentRegistry from '../../NativeComponent/NativeComponentRegistry'

const ViewNativeComponent: HostComponent<Props> =
  NativeComponentRegistry.get<Props>('RCTView', () => ({
    uiViewClassName: 'RCTView',
  }))

export default ViewNativeComponent
```

这段代码说明：

```text
JS 层 ViewProps
  ↓
HostComponent<Props>
  ↓
NativeComponentRegistry.get('RCTView')
  ↓
原生 RCTView
```

也就是说：

- JS 层 `View` 的 props 类型来自 `ViewProps`；
- 底层 Host Component 名称是 `RCTView`；
- iOS / Android 侧会有对应原生 ViewManager / Component 实现；
- Fabric Renderer 会根据 Shadow Tree 和 Host Component 信息创建原生视图。

完整映射关系：

```text
<View />
  ↓
View wrapper
  ↓
ViewNativeComponent
  ↓
HostComponent<ViewProps>
  ↓
NativeComponentRegistry.get('RCTView')
  ↓
iOS / Android 原生 RCTView
```

### 为什么会有 RCTView

`RCTView` 可以理解为 React Native 里的“原生 View 注册名”。

在 Web React 中：

```tsx
<div />
```

最终会创建 DOM 节点：

```text
HTMLDivElement
```

但在 React Native 中：

```tsx
<View />
```

不能创建 DOM，因为移动端没有浏览器 DOM。它要创建的是平台原生视图：

```text
iOS: UIView / RCTView
Android: ViewGroup / ReactViewGroup
```

所以需要一个中间名字，把 JS 层组件和 Native 层实现对应起来：

```text
JS <View />
  ↓
Host Component name: RCTView
  ↓
ViewManager / ComponentDescriptor
  ↓
iOS UIView / Android ViewGroup
```

`RCT` 这个前缀来自 React Native 早期 iOS / Objective-C 代码里的命名前缀，可以简单理解为 `React` 的缩写前缀。由于 React Native 最早很多核心代码在 iOS 侧使用 Objective-C 实现，类名常见格式就是：

```text
RCTBridge
RCTRootView
RCTView
RCTText
RCTImageView
RCTScrollView
```

后来 Android、新架构 Fabric、Codegen 继续保留了很多这些组件注册名，主要是为了让 JS 层、原生 ViewManager、事件系统、样式系统和历史生态保持一致。

也就是说，`RCTView` 不是你业务里要直接写的组件，而是 RN 内部用来描述“这个 Host Component 要由哪个原生实现创建”的名字。

### RCTView 解决了什么问题

如果没有 `RCTView` 这一层映射，React Native 就无法把一段跨平台 JSX 转成具体原生视图。

它解决的是这几个问题：

1. **跨平台统一抽象**
   - JS 层统一写 `<View />`；
   - iOS 创建 `UIView` / `RCTView`；
   - Android 创建 `ViewGroup` / `ReactViewGroup`。

2. **属性映射**
   - JS 写 `style={{ backgroundColor: 'red' }}`；
   - RN 把 props 转成 Shadow Node 属性；
   - 最终由原生侧设置背景色、透明度、边框、点击区域等。

3. **事件映射**
   - JS 写 `onPress`、`onLayout`、`onStartShouldSetResponder`；
   - Native 侧产生触摸、布局事件；
   - RN 事件系统再分发回 JS。

4. **布局映射**
   - JS 写 flex 样式；
   - Shadow Tree + Yoga 计算布局；
   - Mount 阶段把位置和尺寸应用到原生 View。

5. **渲染器解耦**
   - JS 不需要知道 iOS / Android 创建 View 的细节；
   - Renderer 只需要根据 Host Component 名称和 props 生成对应 mutation。

### 常见的 RCT 组件名

React Native 内部有不少以 `RCT` 开头的组件名或原生类名。不同版本、新旧架构、平台之间会有差异，但常见可以分成几类。

#### 1. 基础视图类

| 名称 | 大致对应 JS 组件 | 作用 |
| --- | --- | --- |
| `RCTView` | `View` | 最基础的容器视图 |
| `RCTText` | `Text` | 文本组件 |
| `RCTRawText` | `Text` 内部文本节点 | 保存纯文本内容，本身通常不直接对应独立原生 View |
| `RCTVirtualText` | 嵌套 `Text` | 文本嵌套时使用的虚拟文本节点 |
| `RCTImageView` | `Image` | 图片显示 |
| `RCTScrollView` | `ScrollView` | 滚动容器 |
| `RCTRefreshControl` | `RefreshControl` | 下拉刷新控件 |
| `RCTModalHostView` | `Modal` | Modal 宿主视图 |

#### 2. 表单和系统控件

| 名称 | 大致对应 JS 组件 | 作用 |
| --- | --- | --- |
| `RCTTextInput` / 平台 TextInput 组件 | `TextInput` | 文本输入 |
| `RCTSwitch` | `Switch` | 开关控件 |
| `RCTSlider` | `Slider` 旧核心组件 / 社区组件 | 滑块控件 |
| `RCTActivityIndicatorView` | `ActivityIndicator` | 加载指示器 |
| `RCTSegmentedControl` | 旧 iOS 组件 / 社区组件 | iOS 分段选择器 |
| `RCTSafeAreaView` | `SafeAreaView` | 安全区域容器 |

#### 3. 桥接、根视图和管理类

这些不一定是 JS 组件，但也是 RN 内部常见的 `RCT` 类：

| 名称 | 作用 |
| --- | --- |
| `RCTBridge` | 旧架构 Bridge 核心，负责 JS 与 Native 通信 |
| `RCTRootView` | iOS 侧承载 RN 页面根视图 |
| `RCTBridgeDelegate` | 提供 bundle URL 等 Bridge 配置 |
| `RCTBundleURLProvider` | 开发阶段生成 Metro bundle URL |
| `RCTUIManager` | 旧架构中管理原生 UI 创建、更新和事件 |
| `RCTEventDispatcher` | 旧架构事件分发 |
| `RCTModuleData` | Native Module 元数据管理 |
| `RCTImageLoader` | 图片加载能力 |
| `RCTNetworking` | 网络请求相关模块 |
| `RCTTiming` | 定时器相关模块 |
| `RCTDevMenu` | 开发菜单 |

### 新架构下还会叫 RCTView 吗

要注意：`RCTView` 这个名字在很多资料里来自旧架构 / Paper Renderer 的注册名；在新架构 Fabric 中，底层会引入 `ComponentDescriptor`、`ShadowNode`、`Props`、`EventEmitter` 等 C++ 类型，部分组件也会有 Fabric 侧更现代的名字。

可以这样理解：

```text
旧架构 / Paper
  JS Host Component name: RCTView
  ↓
ViewManager
  ↓
Native View

新架构 / Fabric
  JS Host Component / Codegen spec
  ↓
ComponentDescriptor + ShadowNode + Props
  ↓
Mounting transaction
  ↓
Native View
```

但从学习角度看，`RCTView` 仍然很有价值，因为它能帮助理解 RN 的核心思想：

> JS 写的不是 DOM，而是一棵跨平台组件树；RN 通过 Host Component 名称把这棵树映射到平台原生视图。

所以看到 `RCTView`、`RCTText`、`RCTScrollView` 时，可以把它们理解为 RN 内部的“原生组件类型标识”。

### TurboModuleRegistry

关键源码：

```text
packages/react-native/Libraries/TurboModule/TurboModuleRegistry.js
```

核心逻辑可以简化为：

```js
const NativeModules = require('../BatchedBridge/NativeModules').default
const turboModuleProxy = global.__turboModuleProxy

function requireModule(name) {
  if (turboModuleProxy != null) {
    const module = turboModuleProxy(name)
    if (module != null) {
      return module
    }
  }

  const legacyModule = NativeModules[name]
  if (legacyModule != null) {
    return legacyModule
  }

  return null
}

export function get(name) {
  return requireModule(name)
}

export function getEnforcing(name) {
  const module = requireModule(name)
  invariant(module != null, `TurboModule ${name} could not be found`)
  return module
}
```

这里有几个重点：

1. **优先走 TurboModule**

```js
global.__turboModuleProxy(name)
```

如果运行时注入了 `__turboModuleProxy`，就优先从 TurboModule 系统获取模块。

2. **兼容旧 NativeModules**

```js
NativeModules[name]
```

如果 TurboModule 找不到，会回退到旧架构的 `NativeModules`。

3. **`get` 和 `getEnforcing` 区别**

| API | 行为 |
| --- | --- |
| `get(name)` | 找不到返回 `null` |
| `getEnforcing(name)` | 找不到直接抛错 |

整体链路：

```text
TurboModuleRegistry.get('ModuleName')
  ↓
global.__turboModuleProxy('ModuleName')
  ↓ 找不到
NativeModules['ModuleName']
  ↓ 找不到
null / throw error
```

这说明 React Native 在新架构迁移过程中，仍然保留了一定的旧模块兼容路径。

### 一次完整启动到渲染的链路

把上面的源码串起来：

```text
index.js
  ↓
AppRegistry.registerComponent(appName, () => App)
  ↓
runnables[appName] = function
  ↓
Native RootView ready
  ↓
AppRegistry.runApplication(appName, { rootTag, initialProps })
  ↓
runnables[appName](appParameters)
  ↓
renderApplication({ RootComponent, rootTag, initialProps })
  ↓
<AppContainer><App /></AppContainer>
  ↓
Renderer.renderElement({ element, rootTag })
  ↓
ReactFabric.render(element, rootTag, null, true, errorHandlers)
  ↓
Render Phase: React Element Tree → Shadow Tree
  ↓
Commit Phase: Yoga Layout + Tree Promotion
  ↓
Mount Phase: Diff + Mutation + Host View Mounting
  ↓
iOS / Android 原生视图显示
```

这条链路是理解 React Native 的核心。

## Hermes

Hermes 是 Meta 为 React Native 优化的 JavaScript 引擎。

它不是 React Native 的 UI 渲染器，也不是 Bridge。它负责的是：**在移动设备上加载、解释和执行 React Native 的 JavaScript 代码。**

React Native 中大致关系是：

```text
JS / TS 源码
  ↓ Metro / Babel
JS bundle
  ↓ Hermes Compiler
Hermes Bytecode
  ↓ Hermes Runtime
执行 React / 业务逻辑
  ↓ JSI / Renderer
驱动 Native UI 和 Native Modules
```

### 为什么需要 Hermes

移动端 JavaScript 引擎和浏览器场景不完全一样。

浏览器里的 V8、JavaScriptCore 等引擎通常会面对：

- 长时间运行的页面；
- 大量动态网页脚本；
- 更强的 JIT 优化空间；
- 桌面端或高性能移动浏览器环境。

React Native App 更关注：

- 冷启动速度；
- 首屏渲染时间；
- 内存占用；
- 安装包和更新包体积；
- 低端 Android 设备上的稳定表现；
- 与 RN Runtime、JSI、调试工具的集成。

Hermes 的设计目标就是针对这些移动端 App 场景做取舍。

### Hermes 的核心特点

#### 1. 预编译为字节码

传统流程中，设备启动 App 后可能需要解析 JS 源码、生成 AST、编译再执行。

Hermes 更推荐在构建阶段提前把 JS bundle 编译成 Hermes Bytecode，常见扩展可以理解为 `.hbc`。

```text
构建阶段
JS bundle
  ↓ hermesc
Hermes bytecode

运行阶段
Hermes Runtime
  ↓
直接加载 bytecode
  ↓
执行 JS
```

这样可以减少 App 启动时在设备上做 JS 解析和编译的成本，对冷启动更友好。

#### 2. 更适合移动端内存模型

Hermes 会围绕移动端内存压力做优化：

- 减少启动阶段临时对象；
- 避免设备端重复解析大量源码；
- 字节码可以更紧凑地表达可执行逻辑；
- 垃圾回收策略更关注移动端暂停时间和内存占用。

这对低端 Android 设备尤其重要，因为 RN App 的 JS 线程、UI 线程、图片、原生 SDK 都会竞争内存。

#### 3. 默认不依赖 JIT

很多浏览器 JS 引擎会通过 JIT 编译把热点 JS 编译成本地机器码，以提升长时间运行场景的峰值性能。

Hermes 的重点不是追求浏览器式峰值跑分，而是更稳定的启动、内存和包体表现。移动端 App 里，JIT 还会带来额外复杂度，例如：

- 启动时预热成本；
- 运行时编译成本；
- 额外内存占用；
- iOS 对可执行内存和动态代码生成的限制；
- 调试和线上一致性问题。

**所以 Hermes 更偏向“提前编译字节码 + 运行时解释/执行”的模型。**

### Hermes 在 RN 中的位置

React Native 新架构中经常提到 `JSI`，Hermes 和 JSI 的关系可以这样理解：

```text
React / JS 业务代码
  ↓
Hermes Runtime
  ↓
JSI Runtime API
  ↓
C++ / TurboModules / Fabric / Native
```

Hermes 是一个具体的 JS 引擎实现；JSI 是 React Native 用来抽象 JS 引擎和 Native 互操作的接口层。

也就是说：

- Hermes 负责执行 JS；
- JSI 负责让 JS Runtime 和 C++ / Native 可以互相调用；
- TurboModules 可以通过 JSI 暴露给 JS；
- Fabric Renderer 可以和 JS Runtime 更直接协作；
- 理论上 JSI 不只服务 Hermes，也可以适配其他 JS 引擎。

### Hermes 启动流程

生产构建时，流程通常是：

```text
App.tsx / node_modules
  ↓
Metro 解析依赖图
  ↓
Babel 转换 JSX / TS / 新语法
  ↓
生成 JS bundle
  ↓
Hermes Compiler 编译
  ↓
生成 Hermes bytecode
  ↓
打入 iOS / Android 安装包
```

App 启动时：

```text
Native App 启动
  ↓
创建 React Native Runtime
  ↓
初始化 Hermes Runtime
  ↓
加载内置或 OTA 下发的 bytecode
  ↓
执行 JS 入口文件
  ↓
AppRegistry.registerComponent
  ↓
Native RootView ready
  ↓
AppRegistry.runApplication
  ↓
渲染 RN 页面
```

这里要注意：Hermes 只负责执行 JS。页面最终如何变成原生 View，仍然要经过 React Renderer、Shadow Tree、Yoga、Fabric Mounting 等流程。

### Hermes Bytecode 是什么

Hermes Bytecode 可以理解为 Hermes Runtime 更容易加载和执行的中间格式。

它不是源码 JS，也不是 iOS / Android 原生机器码。

```text
JS Source       给开发者读
JS Bundle       给 RN 打包后的 JS 代码
Hermes Bytecode 给 Hermes Runtime 执行的字节码
Native Binary   iOS / Android 编译后的原生安装包
```

字节码的好处：

- 启动时少做解析和编译；
- 加载速度更稳定；
- 可以减少部分源码文本带来的体积和解析成本；
- 更适合 OTA 分发和运行时加载。

但它也有边界：

- bytecode 需要和 Hermes Runtime 版本兼容；
- 升级 RN / Hermes 后，旧 bytecode 可能不能继续复用；
- bytecode 不能新增 Native Module，也不能修改原生权限；
- 它不是安全加密方案，不能把敏感逻辑完全依赖字节码隐藏。

### Hermes 和 JavaScriptCore 的区别

React Native 早期 iOS 上常见 JavaScriptCore，后续 Hermes 逐渐成为 RN 默认推荐方向。

可以简单对比：

| 维度 | Hermes | JavaScriptCore |
| --- | --- | --- |
| 设计目标 | 面向 React Native 移动端优化 | 通用 JS 引擎，Safari/WebKit 生态相关 |
| 启动策略 | 预编译 bytecode，减少启动解析成本 | 更偏通用 JS 执行模型 |
| 内存优化 | 更关注 RN App 和低端设备 | 通用场景能力强 |
| JSI 集成 | RN 官方重点集成方向 | 可以适配，但不是 RN 当前重点 |
| Android 表现 | 通常更适合 RN 默认场景 | 需要额外集成和权衡 |
| 调试体验 | React Native DevTools / Hermes Debugger 支持 | 依赖具体平台和工具链 |

这里不是说 Hermes 在所有 JS 场景都一定比其他引擎更快，而是说：**Hermes 的优化目标更贴近 RN App 的启动、内存和工具链需求。**

### Hermes 对性能的影响

Hermes 最常见的收益在：

1. **冷启动更稳**
   - 设备启动时少做 JS parse / compile；
   - 大型 bundle 的启动成本更可控。

2. **内存占用更可控**
   - 更少临时解析结构；
   - 字节码和运行时针对移动端优化。

3. **低端 Android 体验更好**
   - 低端机 CPU 和内存更紧张；
   - Hermes 的启动和内存优化更容易体现收益。

4. **调试和 Profiling 更统一**
   - 可以配合 Hermes Debugger、Sampling Profiler、React Native DevTools 分析 JS 执行问题。

但也要注意：

- 计算密集型 JS 不一定因为换 Hermes 就自动变快；
- 大量无意义 render、超大列表、图片内存、JS / Native 高频通信仍然需要业务层优化；
- 如果 JS bundle 本身很大，还是需要拆包、懒加载、删除无用依赖；
- 引擎优化不能替代架构优化。

### Hermes 与调试

Hermes 支持 RN 调试和性能分析能力，常见包括：

- JS 堆栈和异常定位；
- source map 映射源码；
- sampling profiler 分析 JS 执行耗时；
- Chrome DevTools Protocol / Hermes Inspector 相关调试能力；
- React Native DevTools 中查看组件、性能和日志。

生产环境中，如果使用 Hermes bytecode，错误堆栈通常需要配合 source map 还原，否则线上错误可能只看到压缩或字节码相关位置。

所以发布时要注意：

- 保存对应版本的 source map；
- source map 要和 bundle / bytecode 精确匹配；
- OTA 更新也要保存每次更新对应的 source map；
- 崩溃和 JS 错误平台需要按版本、渠道、runtimeVersion 关联 source map。

### Hermes 与 OTA 的关系

启用 Hermes 后，OTA 更新通常分发的可能是 JS bundle，也可能是 Hermes bytecode，取决于工具链和平台配置。

核心原则是：

```text
OTA 包里的 JS / bytecode
必须兼容当前 App 内置的 Hermes Runtime
```

如果一次更新改了这些内容：

- React Native 版本；
- Hermes 版本；
- Expo SDK 版本；
- 新增 Native Module；
- 修改原生权限或配置；

就不能只靠 OTA 覆盖过去，通常需要重新构建原生包，并通过 `runtimeVersion`、`appVersion`、`channel` 等机制隔离更新。

### 什么时候重点关注 Hermes

这些场景下尤其应该关注 Hermes：

- App 冷启动慢；
- JS bundle 很大；
- 低端 Android 内存压力大；
- 首屏前 JS 初始化逻辑太重；
- OTA 更新后出现兼容问题；
- 线上 JS 错误堆栈无法正确还原；
- 需要分析 JS thread 是否被长任务阻塞。

排查思路通常是：

```text
启动慢
  ↓
看 bundle 体积、初始化模块数量、首屏依赖
  ↓
看 Hermes profiler / startup trace
  ↓
拆分启动路径、延迟加载非首屏模块
  ↓
减少同步计算和大型 JSON 解析
```

一句话总结：

> Hermes 是 React Native 面向移动端体验定制的 JS 引擎，它通过预编译字节码、移动端内存优化和 RN 工具链集成，改善启动、内存和调试体验；但它只能优化 JS 执行环境，不能替代渲染优化、业务架构优化和原生发版管理。

## Metro

Metro 是 React Native 默认打包器。

它负责：

- 解析 JS / TS 模块依赖；
- 转换 JSX / TS；
- 打包 bundle；
- 支持 Fast Refresh；
- 为开发环境提供本地 dev server。

开发时常见链路：

```text
源码修改
  ↓
Metro transform
  ↓
Fast Refresh
  ↓
App 局部刷新
```

### Expo 也会用到 Metro 吗

会，但角色不同：

| 场景 | 是否用 Metro | Metro 的角色 | App 如何拿到代码 |
| --- | --- | --- | --- |
| Expo Go 开发调试 | 是 | 本地 dev server，实时打包和 Fast Refresh | 从本机 Metro 拉取 |
| development build 开发调试 | 是 | 本地 dev server，实时打包和 Fast Refresh | 从本机 Metro 拉取 |
| EAS Update 生产 OTA | 是 | 发布时生成生产 bundle / assets | 从 EAS Update 服务下载 |
| 已上架 App 正常运行 | 否 | 不连接 Metro | 加载内置包或已下载 OTA 包 |

一句话：**Expo 开发期热刷新直接依赖 Metro；Expo 生产 OTA 只是在发布时使用 Metro 生成更新包，线上分发和加载由 EAS Update / expo-updates 完成。**

## 热更新原理

`OTA` 是 `Over-the-Air` 的缩写，直译是“通过空中传输”。在移动 App 语境里，它指的是：**不让用户重新去应用商店下载完整安装包，而是让已安装的 App 通过网络下载更新内容并在本地生效。**

在 React Native 中，OTA 通常就是线上热更新：App 保持原来的原生安装包不变，只下载新的 JS bundle、Hermes bytecode 和图片、字体等静态资源。它适合修复 JS 业务 bug、调整页面逻辑和样式，但不能新增原生模块、系统权限或修改已经编译进 App 的 Native 代码。

React Native 常说的“热更新”其实有两类，需要先区分清楚：

| 类型 | 使用场景 | 代表能力 | 是否用于生产 |
| --- | --- | --- | --- |
| 开发期热刷新 | 本地开发调试 | Metro + Fast Refresh | 否 |
| 生产期 OTA 更新 | 已安装 App 的 JS 逻辑和资源更新 | Expo Updates、CodePush、自建更新系统 | 是 |

### 1. 开发期：Fast Refresh

开发期热刷新依赖 Metro Dev Server 和 Fast Refresh，重点是提升本地开发体验：源码变化后，Metro 增量转换相关模块，App 通过调试连接拉取变化，React Refresh 尽量保留组件状态并重新渲染。

它不是下载安装线上版本，也不等同于生产 OTA；Expo Go 和 development build 在开发期也是通过这套机制从 Metro 拉取 JS bundle。

### 2. 生产期：OTA 热更新

生产环境中的热更新，本质是：**在不重新发布 App Store / 应用商店安装包的前提下，让已安装 App 下载并切换到新的 JS bundle 和静态资源。**

React Native App 通常由两部分组成：

```text
Native Binary
  ├── iOS / Android 原生壳
  ├── React Native Runtime
  ├── 原生模块 / SDK
  ├── 内置 JS bundle
  └── 内置 assets

OTA Package
  ├── 新 JS bundle 或 Hermes bytecode
  ├── 新图片 / 字体等 assets
  └── manifest / metadata
```

因此，OTA 能更新的是 JS 层和资源层，不能直接更新已经编译进 App 的 Native 代码。

### 3. 启动时如何加载更新包

一个典型 OTA 系统会改造 React Native 加载 JS bundle 的位置。

简化流程：

```text
App 启动
  ↓
Native 初始化 RN Runtime
  ↓
读取本地更新目录
  ↓
判断是否存在可用 OTA 包
  ├── 有：加载沙盒目录中的新 bundle / assets
  └── 无：加载安装包内置 bundle / assets
  ↓
执行 JS，AppRegistry.runApplication
```

关键点在于：原生侧启动 RN 时需要告诉运行时 JS bundle 在哪里。

- iOS 侧通常由 `RCTBridgeDelegate` / `RCTBundleURLProvider` 一类逻辑决定 bundle URL；
- Android 侧通常由 `ReactNativeHost` / `JSBundleFile` 一类逻辑决定 bundle 路径；
- OTA SDK 会把远端下载的新 bundle 放到 App 沙盒目录，并在下次启动时优先返回这个路径；
- 如果没有可用更新，或者更新包校验失败，就回退到安装包内置 bundle。

### 4. 更新包下载与校验

生产 OTA 不只是下载一个 JS 文件，还要解决版本、资源、完整性和回滚问题。

常见流程：

```text
App 启动或进入前台
  ↓
请求更新服务 manifest
  ↓
携带 appVersion / platform / channel / runtimeVersion
  ↓
服务端判断是否有匹配更新
  ↓
下载 bundle 和 assets
  ↓
校验 hash / 签名 / 资源完整性
  ↓
保存到沙盒目录
  ↓
标记为 pending 或 ready
  ↓
下次启动或用户确认后切换
```

`manifest` 一般会描述：

- 更新包 ID；
- 适用平台：iOS / Android；
- 原生版本约束：例如 `appVersion`、`runtimeVersion`；
- JS bundle 地址；
- assets 列表、hash、大小；
- 是否强制更新；
- 灰度渠道或发布 channel。

下载完成后通常不会立刻无条件切换，而是先写入本地目录并记录状态。下一次启动成功后再把该版本标记为可用，避免“下载了坏包就永久白屏”。

### 5. 为什么不能更新 Native 代码

OTA 热更新有一个硬边界：**只能更新当前 Native Binary 已经具备的运行时能力。**

可以更新：

- JS / TS 编译后的业务逻辑；
- React 组件结构；
- 样式对象；
- 图片、字体、JSON 配置等资源；
- 已存在 Native Module 的调用参数和业务编排。

不能更新：

- 新增或修改 iOS / Android 原生代码；
- 新增 Native Module / TurboModule；
- 新增原生 SDK；
- 修改 `Info.plist`、`AndroidManifest.xml`、权限、Scheme 等原生配置；
- 修改 App 图标、启动图、扩展能力等需要重新打包的内容；
- 改变 Hermes、React Native Runtime、Fabric、TurboModules 等原生运行时版本。

原因是这些内容已经编译、签名并随应用商店包分发，线上 App 只能在这个二进制能力范围内加载新的 JS。

### 6. Hermes 下的热更新

启用 Hermes 后，OTA 更新可能分发 JS bundle，也可能分发 Hermes bytecode，取决于工具链配置。这里最重要的不是文件形态，而是兼容性：**更新包必须匹配当前 App 内置的 Hermes Runtime、RN Runtime 和原生模块集合。**

因此 Expo Updates 常用 `runtimeVersion` 隔离不同原生运行时；如果升级了 RN / Hermes、Expo SDK，或新增 Native Module，就不能只靠 OTA 覆盖，通常需要重新构建原生包。Hermes bytecode 的细节已经在前文 `Hermes` 章节展开，这里只强调 OTA 边界。

### 7. 灰度、回滚与安全

成熟的 OTA 系统一般还需要：

- **灰度发布**：按用户比例、渠道、版本、地区逐步放量；
- **版本隔离**：不同原生版本只接收兼容的 JS 更新；
- **失败回滚**：启动崩溃或白屏时回退到上一个可用包；
- **完整性校验**：校验 hash，防止资源损坏；
- **签名校验**：防止中间人或非法更新包；
- **强制更新策略**：严重 bug 可要求立即 reload 或下次启动生效；
- **监控告警**：结合崩溃率、白屏率、启动失败率决定是否暂停发布。

典型回滚模型：

```text
下载新包
  ↓
标记 pending
  ↓
下次启动尝试加载
  ↓
JS 成功启动并上报 ready
  ├── 成功：标记为 current
  └── 失败：回滚到 previous / embedded bundle
```

### 8. Expo Updates 和 CodePush 的思路

常见方案虽然实现不同，但核心思路相似。

**Expo Updates** 更强调 `runtimeVersion`：

- 同一个 `runtimeVersion` 表示 JS 包可以运行在同一套原生运行时上；
- 原生依赖变化时应升级 `runtimeVersion`，避免旧 App 下载不兼容的新 JS；
- 配合 EAS Update 可以按 channel / branch 发布。

**CodePush** 的核心概念通常包括：

- deployment key：区分 Staging / Production 等部署环境；
- app version：限制更新适用的原生版本；
- mandatory update：强制更新；
- rollback：更新失败自动回滚。

无论使用哪种方案，都要把“JS 更新”和“原生发版”分开管理：

```text
只改 JS / 样式 / 资源
  → 可以考虑 OTA

改 Native Module / SDK / 权限 / RN Runtime
  → 必须重新打原生包并走商店发布
```

### 9. 热更新的本质

一句话总结：

> React Native 热更新不是替换整个 App，而是在稳定的原生运行时外壳中，动态替换 JS bundle 和资源文件。

它成立的前提是：

- React Native 的业务逻辑大部分运行在 JS Runtime 中；
- 原生启动器可以选择从沙盒路径加载 bundle；
- 新 bundle 与当前 Native Binary 兼容；
- 更新包经过校验、可回滚、可灰度。

所以热更新适合高频业务迭代和线上问题修复，但不能替代正常的原生版本发布。

## Yoga 布局引擎

React Native 使用 Yoga 做跨平台 Flexbox 布局。

在 Web 中：

```css
display: flex;
```

在 React Native 中默认就是 Flex 布局，但也有一些差异：

- 默认 `flexDirection` 是 `column`，Web 是 `row`；
- 样式不是 CSS 字符串，而是 JS 对象；
- 不支持完整 CSS 能力；
- 布局最终由 Yoga 计算，再转换为原生视图的 frame / layout params。

示例：

```tsx
<View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
  <Text>Hello</Text>
</View>
```

大致过程：

```text
style object
  ↓
Shadow Node props
  ↓
Yoga layout calculation
  ↓
position / size
  ↓
Host View layout
```

## 性能优化方向

### 1. 减少无意义渲染

使用：

- `React.memo`；
- `useMemo`；
- `useCallback`；
- 合理拆分组件；
- 避免在 render 中创建大量新对象。

示例：

```tsx
const Item = React.memo(function Item({ title }) {
  return <Text>{title}</Text>
})
```

### 2. 长列表使用 FlatList

不要直接：

```tsx
{list.map(item => <Item key={item.id} item={item} />)}
```

大列表应该使用：

```tsx
<FlatList
  data={list}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <Item item={item} />}
/>
```

常用优化参数：

- `initialNumToRender`；
- `maxToRenderPerBatch`；
- `windowSize`；
- `getItemLayout`；
- `removeClippedSubviews`。

### 3. 动画和手势尽量走 UI 线程

复杂动画和手势建议使用：

- React Native Reanimated；
- Gesture Handler；
- 原生动画能力。

避免每一帧都依赖 JS thread，否则 JS 忙时动画容易掉帧。

### 4. 图片优化

需要关注：

- 图片尺寸；
- 缓存；
- WebP / AVIF 等格式；
- 首屏图片加载；
- 列表图片复用。

### 5. 控制 JS bundle 体积

优化方向：

- 删除无用依赖；
- 避免引入大型库；
- 使用 Hermes；
- 关注 Metro 分包和 RAM bundle 相关能力；
- 对启动路径做懒加载。

### 6. 减少 JS / Native 高频通信

即使新架构下 JSI 更快，也不意味着可以无脑高频跨端调用。

建议：

- 高频动画放 UI thread；
- 大数据处理放 Native / C++；
- 批量传输数据；
- 避免 JS 和 Native 来回 ping-pong。

## 工程化与选型

### React Native CLI

适合：

- 已有原生项目接入 RN；
- 需要深度原生定制；
- 对 iOS / Android 工程有强控制要求；
- 公司已有成熟原生基础设施。

特点：

- 原生工程完全暴露；
- 灵活度最高；
- 工程复杂度也最高。

### Expo

适合：

- 新项目；
- 中小团队；
- 希望快速上线；
- 不想一开始维护复杂原生工程；
- 需要 OTA、构建、提交等完整工具链。

特点：

- 开发体验好；
- 工具链完整；
- 社区模块丰富；
- 遇到复杂原生需求时可以使用 dev client 或 prebuild。

#### 为什么需要 Expo

React Native 的难点不只在“写页面”，还在“把一个 App 长期稳定地构建、升级、发布和维护”。如果只使用 React Native CLI，新项目通常很快会遇到这些问题：

1. **原生工程复杂**
   - iOS 有 Xcode、CocoaPods、证书、Provisioning Profile；
   - Android 有 Gradle、AGP、Kotlin、签名、Manifest；
   - 每个原生库都可能要求修改 `Info.plist`、`AndroidManifest.xml`、`Podfile`、Gradle 配置。

2. **常用能力重复建设**
   - 相机、通知、定位、文件系统、权限、设备信息、深链、分享、震动、媒体库等能力，每个 App 都需要；
   - 直接接多个第三方原生库时，API 风格、版本兼容、配置方式都不一致。

3. **构建和发布链路重**
   - 本地机器需要完整 iOS / Android 环境；
   - CI 需要处理证书、缓存、构建产物、商店提交；
   - 小团队很容易把大量时间花在移动端基础设施上。

4. **OTA 更新需要兼容管理**
   - JS bundle 可以更新，但 Native Binary 不能随便变；
   - 必须确保线上更新包只发给兼容的原生运行时；
   - 需要灰度、回滚、渠道、版本隔离和资源完整性校验。

5. **React Native 升级成本高**
   - RN 升级不只是改 JS 依赖，还涉及 iOS / Android 模板、Gradle、Pods、Hermes、Metro、Babel 等；
   - Expo SDK 把一组 React Native 版本、原生模块版本和工具链组合打包成一个相对稳定的发行单位。

因此，Expo 的价值可以总结为：

> 把 React Native 从“一个跨平台运行时”补齐成“一个可以持续开发、构建、发布、更新的 App 平台”。

#### Expo 的核心组成

`EAS` 是 `Expo Application Services` 的缩写，也就是 Expo 提供的一组云端应用服务。它不是一个新的运行时框架，而是围绕 React Native / Expo App 的构建、提交和线上更新提供工程化基础设施。

简单说：

- `EAS Build`：在云端构建 iOS / Android 安装包；
- `EAS Submit`：把构建产物提交到 App Store / Google Play；
- `EAS Update`：向已安装 App 分发 JS bundle 和资源，也就是 Expo 的 OTA 热更新能力。

Expo 生态可以分成几块：

| 模块 | 作用 |
| --- | --- |
| Expo CLI | 创建项目、启动 Metro、运行开发构建、执行 prebuild |
| Expo SDK | 一组维护良好的跨平台原生模块，例如 Camera、Notifications、Location、FileSystem |
| Expo Router | 基于文件系统的路由方案，类似 Web 框架里的 file-based routing |
| app config | 用 `app.json` / `app.config.js` 声明 App 名称、图标、权限、scheme、更新配置等 |
| config plugins | 在 prebuild 阶段自动修改 iOS / Android 原生工程 |
| Expo Go | 预装 Expo SDK 的通用客户端，用于快速预览不含自定义原生代码的项目 |
| development build | 包含项目自定义原生依赖的开发客户端 |
| EAS Build | 云端构建 iOS / Android 原生包 |
| EAS Submit | 自动提交到 App Store / Google Play |
| EAS Update | OTA 分发 JS bundle 和 assets |

它们之间的关系可以简化为：

```text
App Code + app config
  ↓
Expo CLI
  ↓
Prebuild / Config Plugins
  ↓
iOS / Android native project
  ↓
EAS Build
  ↓
App Store / Google Play

JS bundle + assets
  ↓
EAS Update
  ↓
Installed app downloads compatible updates
```

#### Expo 的实现原理

##### 1. app config 是工程配置入口

Expo 项目的根目录通常有：

```text
app.json
app.config.js
app.config.ts
```

这些配置描述了很多原生工程信息：

- App 名称、bundle identifier、package name；
- 图标、启动图、屏幕方向；
- iOS `Info.plist` 配置；
- Android 权限、intent、adaptive icon；
- deep link scheme；
- EAS Update 的 `runtimeVersion` 和 `updates.url`；
- config plugins 列表。

传统 React Native 项目里，很多配置需要直接改原生文件。Expo 的思路是：**先把配置声明在 JS/JSON 层，再由工具生成或修改原生工程。**

##### 2. Continuous Native Generation：持续生成原生工程

Expo 现在强调 Continuous Native Generation，简称 CNG。

核心命令是：

```bash
npx expo prebuild
```

它大致会做这些事：

```text
读取 app config
  ↓
选择匹配当前 Expo SDK 的原生模板
  ↓
生成 ios/ 和 android/
  ↓
执行 config plugins
  ↓
安装并链接原生依赖
  ↓
得到可用的 Xcode / Gradle 工程
```

这意味着 Expo 项目不是没有原生工程，而是可以把原生工程当作“由配置和插件生成的产物”。需要深入修改时，也可以把 `ios/`、`android/` 留在仓库里继续手动维护。

##### 3. Config Plugins：把原生配置自动化

很多原生库的接入步骤本质上是固定的，例如：

- 往 `Info.plist` 写权限描述；
- 往 `AndroidManifest.xml` 加 permission / activity / service；
- 修改 Gradle 配置；
- 修改 Podfile；
- 拷贝资源文件；
- 增加 App Extension 或特殊 target。

Config Plugin 就是用 JavaScript 描述这些原生修改。

简化理解：

```text
app.config.js
  ↓ plugins: ['expo-camera', 'expo-notifications']
prebuild
  ↓
执行插件函数
  ↓
修改 ios/ android/ 原生文件
```

这样，库作者可以把“安装这个库需要怎么改原生工程”封装进插件里，使用者只需要在 app config 中声明配置，减少手动改原生文件带来的漏配和升级冲突。

##### 4. Expo Modules：统一封装原生能力

Expo SDK 里的很多能力都是 Expo Modules，例如相机、定位、通知、文件系统等。

一个 Expo Module 通常包含：

```text
JS / TS API
  ↓
expo-modules-core
  ↓
Swift / Kotlin 原生实现
  ↓
iOS / Android 系统 API
```

对开发者来说，使用方式像普通 JS 包：

```tsx
import * as Location from 'expo-location'

const location = await Location.getCurrentPositionAsync()
```

但底层会进入对应的 iOS / Android 原生实现。Expo Modules API 的目标是减少写原生模块的样板代码，并让 Swift / Kotlin 模块以一致的方式暴露方法、事件、常量和原生视图。它也和 React Native New Architecture 保持兼容，适合大多数常规原生能力封装。

##### 5. Expo Go 的实现原理

Expo Go 可以理解为一个预先安装了 React Native Runtime、Expo SDK 原生模块和开发调试能力的通用客户端。

它的核心思想是：**把原生壳提前做好并发布成一个通用 App，开发者只需要把自己的 JS bundle 通过 Metro 交给这个壳运行。**

简化结构：

```text
Expo Go Native Binary
  ├── React Native Runtime
  ├── Hermes / JS Runtime
  ├── Expo Modules Core
  ├── 预置 Expo SDK 原生模块
  ├── 开发菜单 / 日志 / 错误覆盖层
  └── JS bundle loader

你的项目
  ├── App.tsx / 路由 / 业务代码
  ├── JS dependencies
  └── assets
```

启动链路大致是：

```text
本地执行 npx expo start
  ↓
Expo CLI 启动 Metro Dev Server
  ↓
生成项目 manifest 和 bundle URL
  ↓
Expo Go 扫码或通过 deep link 打开项目
  ↓
Expo Go 请求 manifest
  ↓
根据 manifest 找到 JS bundle 地址
  ↓
从 Metro 下载 JS bundle 和 assets
  ↓
在内置 RN Runtime 中执行 JS
  ↓
AppRegistry.runApplication 渲染页面
```

这里的 `manifest` 可以理解为项目启动说明书，里面会包含：

- 项目名称、入口信息；
- JS bundle 地址；
- assets 地址；
- SDK 版本；
- 平台信息；
- 调试相关配置。

Expo Go 不是把你的项目重新编译进 App，而是在运行时加载你的 JS。也就是说：

```text
Expo Go 原生壳固定
  +
从 Metro 拉取你的 JS bundle
  =
快速预览 Expo / React Native 页面
```

这也是为什么 Expo Go 启动很快：你不需要每次修改 JS 都重新执行 Xcode / Gradle 构建，只要 Metro 重新转换 JS，Expo Go 重新加载或 Fast Refresh 即可。

##### Expo Go 如何调用原生能力

当 JS 中调用 Expo API 时，例如：

```tsx
import * as Location from 'expo-location'

const location = await Location.getCurrentPositionAsync()
```

实际链路是：

```text
JS API: expo-location
  ↓
Expo Modules JS binding
  ↓
expo-modules-core
  ↓
Expo Go 内置的 Location 原生模块
  ↓
iOS CoreLocation / Android Location API
```

关键点是：`expo-location` 的原生实现必须已经存在于 Expo Go 的二进制中。JS 只是调用入口，真正访问系统能力的 Swift / Kotlin 代码来自 Expo Go 已经内置的 Expo SDK。

##### 为什么 Expo Go 有限制

Expo Go 的限制来自一个事实：**App Store / Google Play 安装下来的 Expo Go 二进制是固定的。**

它能运行：

- 普通 React / React Native JS 代码；
- Expo Go 已内置 SDK 对应的模块；
- 不需要额外原生配置的页面、样式、状态、网络请求等逻辑。

它不能运行：

- 你自己新写的 Native Module / TurboModule；
- 没有被 Expo Go 内置的第三方原生库；
- 需要修改 `Info.plist`、`AndroidManifest.xml`、Gradle、Podfile 的能力；
- 自定义启动图、App 图标、权限、Scheme、Push 配置等原生工程行为；
- 与 Expo Go 当前 SDK / RN Runtime 不兼容的代码。

原因很简单：JS 可以动态下载，但 Native 代码必须提前编译、签名并打进安装包。如果 Expo Go 的安装包里没有某个原生模块，JS 层 `import` 了对应包也无法真正调用到 Native 实现。

##### 6. Expo Go 和 development build 的区别

当项目只依赖 Expo Go 已经内置的能力时，可以直接用 Expo Go。

一旦项目需要自定义原生依赖，就应该使用 development build：

```text
你的项目原生依赖
  ↓
prebuild / config plugins
  ↓
生成或修改 ios/ android/
  ↓
EAS Build / local build
  ↓
生成专属开发客户端
  ↓
从 Metro 加载 JS bundle
```

二者本质区别是：

| 维度 | Expo Go | development build |
| --- | --- | --- |
| 原生壳 | Expo 官方通用壳 | 当前项目自己的原生壳 |
| 原生模块 | 只能用 Expo Go 内置模块 | 可以包含项目自定义原生模块 |
| 原生配置 | 基本不可改 | 可通过 config plugins / 原生工程修改 |
| 启动方式 | 扫码或 deep link 加载 Metro bundle | 像专属调试 App 一样加载 Metro bundle |
| 适合场景 | 快速试验、学习、轻量 Expo 项目 | 真实业务 App、需要自定义原生能力的项目 |

所以，development build 本质上是“带 Expo 开发体验的自定义原生 App”。它既保留类似 Expo Go 的扫码、Metro、Fast Refresh、开发菜单等体验，又允许使用项目自己的原生依赖。

##### 7. EAS Update：Expo 的生产 OTA 能力

EAS Update 负责向已安装 App 分发兼容的 JS bundle / Hermes bytecode、assets 和 manifest。它会结合 `platform`、`channel`、`branch`、`runtimeVersion`、当前构建信息等条件判断是否返回更新。

这里最关键的是 `runtimeVersion`：它表示“这份 JS 更新可以运行在哪一组原生运行时上”。如果新增 Native Module、升级 Expo SDK、改原生权限或改 RN / Hermes 运行时，就应该切换 `runtimeVersion` 并重新构建原生包。

所以 EAS Update 的边界和所有 RN OTA 一样：能更新 JS 逻辑、样式和资源；不能新增当前二进制里不存在的原生能力；也不能替代 App Store / Google Play 的原生发版。完整 OTA 流程见前文 `热更新原理` 章节。

##### 8. EAS Build：把本地原生环境移到云端

EAS Build 的本质是云端 CI 构建服务。

它会根据项目配置：

```text
拉取代码
  ↓
安装依赖
  ↓
执行 prebuild 或使用已有 ios/android
  ↓
安装 Pods / Gradle dependencies
  ↓
注入证书和签名配置
  ↓
构建 .ipa / .aab / .apk
  ↓
产出可安装或可提交的包
```

这对团队的意义是：不需要每个开发者本机都维护完整的发布环境，也不需要自己从零搭一套移动端 CI/CD。

#### 什么时候不用 Expo

Expo 是新 React Native 项目的默认推荐起点，但不是所有场景都必须用。

可以优先考虑 React Native CLI 或保留完整手写原生工程的场景包括：

- 已有大型原生 App，只想局部接入 RN 页面；
- 公司已经有成熟的 iOS / Android 构建、发布、热更新和监控体系；
- 需要大量非标准原生改造，并且团队原生能力很强；
- 对每一行原生工程配置都要强控制；
- 某些关键第三方 SDK 与 Expo SDK / config plugin 生态不匹配。

但对大多数从零开始的新项目，Expo 通常是更务实的选择：先用 Expo 获得完整工具链和标准库，等确实需要自定义原生能力时，再通过 prebuild、config plugins、development build 或自定义 Expo Module 扩展。


### 混合接入已有 App

适合：

- 大型原生 App；
- 局部业务页面跨平台；
- 逐步引入 React Native；
- 需要保留大量原生能力。

典型方式：

```text
Native App
  ├── Native Home
  ├── Native Login
  ├── React Native Business Page
  └── Native Settings
```

## 框架对比

### 总览

| 方案 | 技术栈 | UI 渲染 | 代码复用 | 性能 | 生态 | 适合场景 |
| --- | --- | --- | --- | --- | --- | --- |
| React Native | JS/TS + React | 原生 UI | UI + 逻辑 | 接近原生，依赖架构和优化 | 成熟 | React 团队、跨平台业务 App |
| Flutter | Dart | 自绘 UI | UI + 逻辑 | 高且稳定 | 成熟 | 强一致 UI、多端统一体验 |
| Kotlin Multiplatform | Kotlin | 原生 UI 或 Compose | 主要共享逻辑，也可共享 UI | 原生级 | 增长快 | 原生团队、重业务逻辑复用 |
| 原生开发 | Swift/Kotlin | 原生 UI | 低 | 最强 | 最成熟 | 极致体验、系统深度集成 |
| Ionic / WebView | Web 技术 | WebView | 高 | 中等 | Web 生态 | 内容型、后台型、轻交互 App |

### React Native vs Flutter

#### React Native

优势：

- 使用 React 和 JS / TS，前端团队学习成本低；
- 渲染到原生控件，平台质感更自然；
- 可以渐进式接入已有原生 App；
- 和 Web React 思维一致；
- 原生扩展能力强。

短板：

- 平台差异仍然存在；
- 复杂原生依赖升级可能麻烦；
- 性能上限依赖 JS、Native、渲染和列表优化；
- 大版本升级有一定成本。

#### Flutter

Flutter 官方定位是用单一代码库构建美观的多平台应用。

优势：

- UI 一致性强；
- 自绘引擎，不依赖平台原生控件；
- Widget 体系完整；
- 动画、布局、渲染一致性好；
- 多端一致体验更容易控制。

短板：

- 需要学习 Dart；
- 与原生 UI 风格的完全一致需要额外适配；
- 包体积和平台嵌入成本需要评估；
- Web 端体验和 SEO 等场景不一定适合所有项目。

#### 怎么选

选择 React Native：

- 团队熟悉 React；
- 想复用 Web 前端技术栈；
- 希望使用原生 UI；
- 需要渐进式接入已有原生 App；
- 业务页面多、迭代频繁。

选择 Flutter：

- 希望 UI 在多端高度一致；
- 团队愿意使用 Dart；
- 需要强控制的视觉和动画体验；
- 从零开始做跨平台 App；
- 对自绘体系接受度高。

### React Native vs Kotlin Multiplatform

Kotlin Multiplatform，简称 KMP，官方定位是跨平台复用代码，同时保留原生编程收益。

它有几种模式：

1. 只共享业务逻辑，UI 仍然 iOS / Android 原生；
2. 共享部分核心模块，例如网络、存储、校验、算法；
3. 配合 Compose Multiplatform 共享 UI。

#### React Native 更适合

- 前端团队主导；
- UI 和业务都希望跨端复用；
- 快速迭代业务页面；
- 需要大量 React 生态能力。

#### KMP 更适合

- 原生团队主导；
- 更重视平台原生体验；
- 只想复用业务逻辑，不想统一 UI；
- 已有 Kotlin / Android 技术积累；
- 对性能和平台 API 直接访问要求高。

对比：

| 维度 | React Native | Kotlin Multiplatform |
| --- | --- | --- |
| 主要语言 | JS / TS | Kotlin |
| UI 复用 | 强 | 可选，默认更偏逻辑复用 |
| 原生体验 | 高 | 很高 |
| 前端友好度 | 高 | 中 |
| 原生团队友好度 | 中 | 高 |
| 渐进式接入 | 支持 | 支持，而且更适合逻辑模块接入 |
| 学习成本 | React 团队低 | Android/Kotlin 团队低 |

### React Native vs 原生开发

#### React Native 优势

- 双端复用；
- 迭代快；
- 前端团队可参与移动端；
- 热刷新开发体验好；
- 适合业务页面密集型 App。

#### 原生开发优势

- 性能上限最高；
- 系统 API 支持最快；
- 调试工具最完整；
- 平台体验最一致；
- 复杂动画、音视频、图形、后台任务更稳。

#### 怎么选

选择 React Native：

- 页面型业务；
- 电商、社区、内容、工具类 App；
- 双端需求一致；
- 团队希望提高交付效率。

选择原生：

- 音视频、图形、游戏、AR、复杂后台任务；
- 对系统能力依赖极重；
- 对性能和稳定性要求极致；
- 平台差异很大。

### React Native vs Ionic / WebView

Ionic / WebView 方案本质是 Web App 包一层壳。

优势：

- Web 代码复用率最高；
- 开发门槛低；
- 页面型内容开发快；
- Web 生态可直接使用。

短板：

- UI 和交互不够原生；
- 大量动画和复杂手势性能较弱；
- 原生能力依赖插件；
- 用户体验上限通常低于 RN / Flutter / 原生。

React Native 更适合对原生体验有要求的 App；WebView 更适合内容型、后台型、轻交互应用。

## 适用场景

React Native 适合：

- 电商 App；
- 社区 App；
- 内容资讯；
- 工具类应用；
- 中后台移动端；
- 已有 Web React 团队扩展移动端；
- 大型 App 中部分业务模块跨平台。

不太适合：

- 重度 3D 游戏；
- 极复杂音视频编辑；
- 大量底层系统服务；
- 对首帧、包体、内存要求极端苛刻的场景；
- 平台差异大到 UI 和交互几乎无法复用的项目。

## 项目结构示例

一个常见 React Native 项目结构：

```text
src/
  app/
    App.tsx
  screens/
    HomeScreen.tsx
    DetailScreen.tsx
  components/
    Button.tsx
    UserCard.tsx
  hooks/
    useUser.ts
  services/
    request.ts
  store/
    userStore.ts
  utils/
    format.ts
  native/
    modules.ts
```

如果使用 Expo Router：

```text
app/
  _layout.tsx
  index.tsx
  detail/[id].tsx
  settings.tsx
components/
services/
store/
```

## 常用库

| 方向 | 常用选择 |
| --- | --- |
| 路由 | React Navigation、Expo Router |
| 状态管理 | Zustand、Redux Toolkit、Jotai、Recoil |
| 数据请求 | TanStack Query、SWR、Axios、fetch |
| 动画 | Reanimated、Moti |
| 手势 | React Native Gesture Handler |
| 样式 | StyleSheet、NativeWind、Tamagui |
| 表单 | React Hook Form |
| 存储 | AsyncStorage、MMKV、SQLite |
| 调试 | React Native DevTools、Flipper、Hermes Debugger |
| 构建发布 | Expo EAS、Fastlane、Xcode、Gradle |

## 常见问题

### 1. React Native 是不是 WebView？

不是。

React Native 的 `View`、`Text` 等组件会映射到原生视图，不是 HTML DOM。

### 2. React Native 性能一定比 Flutter 差吗？

不一定。

普通业务 App 中，性能更多取决于：

- 是否正确使用列表；
- 是否避免无意义渲染；
- 动画是否跑在 UI 线程；
- JS / Native 通信是否高频；
- 图片和启动是否优化；
- 是否使用新架构和 Hermes。

Flutter 的自绘模型更容易获得跨平台一致性能；React Native 的原生 UI 模型更贴近平台体验。

### 3. 新架构开启后性能一定变好吗？

不一定。

官方文档也强调，启用 New Architecture 不代表应用性能立刻提升。它更多是为新能力提供基础，例如：

- 同步布局；
- Concurrent Renderer；
- 更快 JS / Native 互操作；
- 类型安全 Codegen；
- 更好的未来扩展。

如果项目瓶颈在业务 JS、图片、列表或网络，新架构不会自动解决这些问题。

### 4. Expo 会不会限制原生能力？

现在 Expo 已经不是早期“只能用 Expo Go”的模式。

可以通过：

- prebuild；
- config plugins；
- expo-dev-client；
- custom native modules；

接入自定义原生能力。

对于多数新项目，Expo 是更推荐的起点。

## 迁移和升级建议

1. **优先升级到仍在官方支持周期内的版本**
   - React Native 官方维护最近 3 个 minor 系列。

2. **关注 New Architecture 兼容性**
   - 检查第三方库是否支持 Fabric / TurboModules。

3. **避免深层导入内部路径**
   - 使用公开 API。
   - 新版本正在收紧 JS API 边界。

4. **优先使用活跃维护的库**
   - 特别是导航、动画、手势、相机、地图、存储等原生相关库。

5. **升级前看官方 Upgrade Helper**
   - React Native 升级涉及 iOS、Android、Gradle、CocoaPods、Metro、Babel 等多个部分。

## 总结

React Native 的本质是：

> 用 React 写原生应用。

它的核心优势不是“完全屏蔽平台差异”，而是：

- 用统一 React 模型提升双端研发效率；
- 通过原生 UI 保留平台体验；
- 通过 JSI / Fabric / TurboModules 提升底层架构能力；
- 在必要时允许开发者写原生代码兜底。

选择建议：

- 前端 React 团队做跨平台 App：优先 React Native / Expo；
- 追求多端 UI 高一致性：考虑 Flutter；
- 原生团队只想共享业务逻辑：考虑 Kotlin Multiplatform；
- 极致平台能力和性能：选择原生；
- 内容型轻交互应用：WebView / Ionic 也可以。

React Native 经过 New Architecture 重构后，已经从早期“JS Bridge 跨平台框架”逐渐演进成以 Fabric、JSI、TurboModules、Codegen、Hermes 为核心的现代原生应用框架。

## 参考资料

- https://reactnative.dev/
- https://reactnative.dev/architecture/landing-page
- https://reactnative.dev/architecture/fabric-renderer
- https://reactnative.dev/architecture/render-pipeline
- https://reactnative.dev/architecture/threading-model
- https://reactnative.dev/docs/releases
- https://reactnative.dev/blog
- https://github.com/facebook/react-native
- https://docs.expo.dev/
- https://docs.expo.dev/workflow/continuous-native-generation/
- https://docs.expo.dev/config-plugins/introduction/
- https://docs.expo.dev/modules/overview/
- https://docs.expo.dev/eas-update/introduction/
- https://docs.flutter.dev/
- https://www.jetbrains.com/kotlin-multiplatform/
