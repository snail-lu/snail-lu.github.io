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
### 生命周期调用顺序
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
这其实与React无关，而是因为 class 类 默认是严格模式，普通函数的this如果不指定就会指向`undefined`，所以要在`constructor`函数中绑定或将类方法写为箭头函数。
### 3. `hooks`有哪些？如何自定义？
