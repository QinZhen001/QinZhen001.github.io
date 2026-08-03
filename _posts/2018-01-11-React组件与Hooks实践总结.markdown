---
layout:     post
title:      "React 组件与 Hooks 实践总结"
description: "从组件数据流、表单状态到常用 Hooks，整理现代 React 开发中最值得掌握的基础实践。"
date:       2018-01-11 13:59:00
updated:    2026-07-30
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
permalink: /2018/01/11/React相关-myblog/
tags:
    - React
    - Hooks
---

> 本文最初是一篇持续追加的 React 笔记，后来逐渐混入了类型、性能、动画、SSR 和排障等内容。此次更新将它收敛为“组件与 Hooks”主线，其余内容拆到独立文章中。

## 阅读导航

- 本文：组件模型、表单、常用 Hooks 与组件组合
- [React 性能优化与工程问题排查]({% post_url 2018-01-13-React性能优化与工程问题排查 %})
- [React 设计哲学与数据流]({% post_url 2018-01-20-React设计哲学与数据流 %})
- [React 核心原理与渲染机制总结]({% post_url 2018-01-21-React核心原理与渲染机制总结 %})

## 一、React 的组件模型

React 的核心可以概括为：**UI 是状态的函数**。

```tsx
function Greeting({ name }: { name: string }) {
  return <h1>Hello, {name}</h1>;
}
```

组件根据 `props`、`state` 和 `context` 计算 UI。一次渲染只描述当前状态下页面应该是什么样子；事件处理、网络请求和 DOM 操作等副作用，应放在事件回调或 Effect 中。

实践中优先遵循以下原则：

1. 使用函数组件和 Hooks 编写新代码。
2. 状态尽量放在真正需要它的最近公共父组件中。
3. 能在渲染期间计算出的值，不要再存一份 state。
4. 优先使用组合，而不是通过继承扩展组件。
5. 不要为了“可能更快”而提前堆叠 memo；先保证数据流清晰。

## 二、受控组件与非受控组件

### 受控组件

表单值由 React state 控制，适合需要实时校验、联动或动态修改值的场景。

```tsx
function SearchInput() {
  const [query, setQuery] = useState('');

  return (
    <input
      value={query}
      onChange={(event) => setQuery(event.target.value)}
    />
  );
}
```

### 非受控组件

表单值由浏览器维护，React 只在需要时通过 ref 或表单提交读取。

```tsx
function SearchForm() {
  const inputRef = useRef<HTMLInputElement>(null);

  function handleSubmit(event: React.FormEvent) {
    event.preventDefault();
    console.log(inputRef.current?.value);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input ref={inputRef} defaultValue="React" />
      <button type="submit">搜索</button>
    </form>
  );
}
```

### `value` 与 `defaultValue`

- `value`：组件持续控制当前值，必须配合 `onChange` 更新。
- `defaultValue`：只设置初始值，挂载后再修改它不会同步更新 DOM 中的当前值。
- `checkbox` 和 `radio` 对应 `checked` / `defaultChecked`。

同一个表单项在生命周期内不要在受控和非受控模式之间切换。大多数业务表单适合受控模式；简单表单、文件输入或原生表单提交可以考虑非受控模式。

## 三、状态设计

### 避免冗余 state

如果一个值可以由现有的 props 或 state 直接计算，就在渲染时计算：

```tsx
function Cart({ items }: { items: Array<{ price: number }> }) {
  const total = items.reduce((sum, item) => sum + item.price, 0);
  return <strong>{total}</strong>;
}
```

把 `total` 再存入 state，会增加同步成本，并可能产生两个数据源不一致的问题。

### `useState` 不会自动合并对象

函数组件的 state 更新会替换原值；更新对象时需要显式合并：

```tsx
setForm((previous) => ({
  ...previous,
  name: 'Qz',
}));
```

### 惰性初始化

初始化计算较昂贵时，将函数传给 `useState`，避免每次渲染都执行：

```tsx
const [state, setState] = useState(() => createInitialState(props));
```

### 何时使用 `useReducer`

当状态转换复杂、多个字段需要一起更新，或希望用 action 明确表达业务事件时，`useReducer` 往往比多个分散的 `useState` 更清晰。

```tsx
type State = { count: number };
type Action = { type: 'increment' } | { type: 'decrement' };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
  }
}
```

## 四、Effect：与外部系统同步

`useEffect` 用于让组件与 React 之外的系统保持同步，例如网络连接、订阅、计时器或第三方组件。

```tsx
useEffect(() => {
  const connection = createConnection(roomId);
  connection.connect();

  return () => connection.disconnect();
}, [roomId]);
```

使用 Effect 时要注意：

- 不要把普通数据转换放进 Effect；能在渲染期间算出的值直接计算。
- 依赖数组应包含 Effect 中读取的所有响应式值，不要通过遗漏依赖“控制执行次数”。
- 清理函数用于撤销本次 Effect 建立的订阅、连接或计时器。
- 开发环境的 Strict Mode 可能额外执行一次“建立 → 清理 → 建立”，用于发现清理不完整的问题。

### `useEffect` 与 `useLayoutEffect`

`useEffect` 通常在浏览器绘制后执行，不阻塞页面展示。`useLayoutEffect` 在 DOM 更新后、浏览器绘制前同步执行，适合测量布局并立即修正位置。

大多数场景应使用 `useEffect`。只有当用户能看到明显闪烁，且逻辑确实依赖布局测量时，才使用 `useLayoutEffect`。服务端渲染期间它不会执行。

## 五、`useRef`

`useRef` 可以保存跨渲染稳定、但变化时不需要触发重新渲染的值。

```tsx
const inputRef = useRef<HTMLInputElement>(null);

function focusInput() {
  inputRef.current?.focus();
}
```

常见用途：

- 聚焦、选择文本或控制媒体播放；
- 保存计时器 ID、第三方实例等非渲染数据；
- 与命令式 DOM API 或第三方库集成。

修改 `ref.current` 不会触发渲染，因此不要用 ref 保存页面必须立即展示的数据。`ref.current` 也不适合作为 Effect 依赖：React 无法通过它的变化触发重新渲染。

## 六、Context

Context 适合传递主题、语言、认证信息等跨层级数据：

```tsx
const ThemeContext = createContext<'light' | 'dark'>('light');

function Toolbar() {
  const theme = useContext(ThemeContext);
  return <div data-theme={theme}>...</div>;
}
```

Context 不是全局状态的默认答案。值频繁变化时，所有读取该 Context 的组件都可能重新渲染。可以按职责拆分 Context，并让 Provider 的 `value` 保持合理稳定。

## 七、Memoization

### `useMemo`

缓存一次昂贵计算的结果：

```tsx
const visibleItems = useMemo(
  () => filterItems(items, query),
  [items, query],
);
```

### `useCallback`

缓存函数引用，通常用于向已经通过 `memo` 优化的子组件传递回调：

```tsx
const handleSubmit = useCallback(() => {
  save(productId);
}, [productId]);
```

### `memo`

当组件在相同 props 下输出相同，且重复渲染成本已经通过性能分析确认较高时，可以使用 `memo`。

三者都是性能工具，不是语义保证。使用它们之前先检查：状态位置是否合理、是否创建了不必要的 Effect、Context 是否过大、列表 key 是否稳定。详细策略见 [React 性能优化与工程问题排查]({% post_url 2018-01-13-React性能优化与工程问题排查 %})。

## 八、组件组合与特殊 API

### Portal

Portal 可以把子节点渲染到组件 DOM 层级之外，但它仍然属于原 React 树，Context 和事件传播仍按 React 树工作。

```tsx
import { createPortal } from 'react-dom';

function Modal({ children }: { children: React.ReactNode }) {
  return createPortal(children, document.body);
}
```

适合模态框、浮层和 Tooltip。

### `lazy` 与 `Suspense`

```tsx
const Settings = lazy(() => import('./Settings'));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <Settings />
    </Suspense>
  );
}
```

`lazy` 用于按需加载组件代码，`Suspense` 提供加载期间的回退 UI。在支持 Suspense 的框架中，它还可以参与流式服务端渲染和数据读取。

### `cloneElement`

`cloneElement` 可以在保留原元素 `key` 和 `ref` 的同时浅合并 props，但它容易形成隐式数据流。新代码通常优先考虑显式 props、Context 或 render prop。

## 九、旧项目迁移提示

维护早期 React 代码时经常会遇到这些 API：

- `ReactDOM.render`：新根节点使用 `createRoot`。
- `unmountComponentAtNode`：使用 root 对象的 `unmount()`。
- `findDOMNode`：已不推荐，改用 ref 直接指向 DOM。
- class 生命周期：新代码优先使用函数组件；不要机械地把生命周期逐个翻译成 Effect。
- HOC 和 render props：仍然有效，但共享有状态逻辑时通常优先考虑自定义 Hook。

```tsx
import { createRoot } from 'react-dom/client';

const container = document.getElementById('root');
if (container) {
  const root = createRoot(container);
  root.render(<App />);
  // root.unmount();
}
```

## 总结

现代 React 的重点不是记忆 API，而是建立稳定的思考顺序：先确定状态归属，再用渲染表达 UI；事件负责用户操作，Effect 只负责外部同步；最后根据真实性能数据决定是否缓存。

参考资料：

- [React：描述 UI](https://react.dev/learn/describing-the-ui)
- [React：添加交互](https://react.dev/learn/adding-interactivity)
- [React：使用 Effect 同步](https://react.dev/learn/synchronizing-with-effects)
