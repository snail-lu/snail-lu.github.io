---
title: React基础知识总结
date: 2021-05-05 21:11:23
summary: Vue基础知识
tags:
- React
- 面试
categories:
- [前端进阶]
- [读书笔记]
---
### 1. 生命周期
- **挂载阶段**
  - `constructor()`
  - `static getDerivedStateFromProps()`
  - `render()`
  - `componentDidMount()`

- **更新阶段**
  - `static getDerivedStateFromProps()`
  - `shouldComponentUpdate()`
  - `render()`
  - `getSnapshotBeforeUpdate()`
  - `componentDidUpdate()`

- **卸载阶段**
  - `componentWillUnmount()`

- **错误处理**
  - `static getDerivedStateFromError()`
  - `componentDidCatch()`

### 2. class组件中的方法为什么需要`.bind(this)`？
这其实与 `React` 无关，而是因为 `class`类默认是严格模式，严格模式下普通函数的 `this` 如果不指定就会指向`undefined`，所以要在`constructor`函数中绑定或将类方法写为箭头函数，否则方法中的就无法使用`this`。

### 3. `Hooks`
#### 什么是 Hook?
Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的函数。Hook 不能在 class 组件中使用 —— 这使得你不使用 class 也能使用 React。

#### 实现原理
每个 `useXxx` 的 hook 都有 **创建阶段**（`mountXxx`） 和 **更新阶段**（`updateXxx`） 两个阶段。在创建阶段，函数组件会将内部用到的所有的 hook 通过环状单向链表的形式，保存在组件对应 fiber 节点的 `memoizedState` 属性上。每个 hook 内部都有自己的状态和依赖维护着。在更新阶段，会按照链表顺序依次执行更新函数。

#### 为什么不能在循环、条件或嵌套函数中调用Hook
> 类似问题：为什么不能改变 hook 的执行顺序
一个函数组件中的多个 Hook 是以链表的结构进行保存的，只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联， 在循环、条件或嵌套函数中，有可能会导致hook的调用顺序发生改变，会发生状态错乱的问题。

#### 内置Hook
- `useState`：创建一个state及对应的更新函数
- `useEffect`：操作副作用（数据获取、订阅或者手动修改过 DOM等）
- `useContext`：订阅 React 的 Context
- `useReducer`：管理本地的复杂 state
- [...](https://react.docschina.org/docs/hooks-reference.html)

### 4. 组件间通信
- `props` 父组件 <=> 子组件，父组件可以传递普通数据给子组件，也可以传递函数给子组件，子组件调用该方法即可向父组件传递数据
- `refs` 父组件 <=> 子组件，父组件可以获取子组件实例，从而调用子组件实例上的方法向子组件传递数据，方法返回值可以用于向父组件传递数据（这种比较被动，是由父组件来主动调用，子组件无法主动发送数据）
- `Context` 父组件 => 子孙组件，在`Context.Provider`的所有子组件及后代组件中都可以获取到
- `Redux` 跨组件