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

### 2. 事件处理为什么要绑定`this`？
这其实与 `React` 无关，而是因为 `class`类默认是严格模式，严格模式下普通函数的 `this` 如果不指定就会指向`undefined`，所以要在`constructor`函数中绑定或将类方法写为箭头函数，否则方法中的就无法使用`this`。

绑定`this`的方式：
- 在constructor中使用`this.xxx = this.xxx.bind(this)`绑定；
- 函数改用箭头函数的形式定义`xxx = () => {}`
- 绑定事件的时候改用箭头函数的形式`onClick={ e => this.xxx(e) }`
- 绑定事件的时候使用bind的形式`onClick={ this.xxx.bind(this) }`

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

### 5. 与Vue的异同点
#### 相同之处
- 都支持服务端渲染
- 都有虚拟DOM
- 都有组件化思想
- 都是数据驱动视图
- 都有状态管理（React -> Redux, Vue -> Vuex）
- 都有native方案（React -> React Native, Vue -> Weex）

#### 不同之处
- React严格上只针对 MVC 的 view 层， Vue 是 mvvm 模式
- 虚拟 DOM 不一样， Vue 会跟踪每一个组件的依赖关系，不需要重新渲染整个 DOM 组件树，而 react 不同，当应用的状态被改变时，全部组件都会重新渲染，所以 react 中用 shouldcomponentupdate 这个生命周期的钩子函数来控制
- 组件写法不一样， React 是 JSX 和 inline style ，就是把 html 和 css 全写进 js 中， vue 则是 html , css , js 在同一个文件
- 数据绑定不一样， Vue 实现了数据双向绑定， react 数据流动是单向的
- 在 React 中， state 对象需要用 setstate 方法更新状态，在 vue 中, 数据由 data 属性在 vue 对象中管理


### 6. 虚拟DOM
虚拟 DOM 也就是常说的虚拟节点，它是通过 js 的 object 对象模拟 DOM 中的节点，然后再通过特定的渲染方法将其渲染成真实的 DOM 节点。
频繁的操作 DOM ，或大量造成页面的重绘和回流，十分消耗性能，而虚拟 dom 相当于在 js 和真实 dom 中间加了一个缓存，利用 dom diff 算法避免了没有必要的 dom 操作，从而提高性能。

### 7. Diff 算法
把树形结构按照层级分解，只比较同级元素，给列表结构的每个单元添加唯一的 key 值，方便比较。
基于三个策略：
- **Tree Diff**: web UI 中 dom 节点跨层级的移动操作特别少，可以忽略不计
- **Component Diff**: 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件会生成不同的树形结构
- **Element Diff**: 对于同一层级的一组子节点，他们可以通过唯一的 id 进行区分

### 8．对组件的理解
可组合，可复用，可维护，可测试

### 9．调用 setState 之后发生了什么？
React 在调用 setstate 后， react 会将传入的参数对象和组件当前的状态合并，触发调和过程。在调和过程中， react 会根据新的状态构建 react 元素树重新渲染整个 UI 界面，在得到元素树之后， react 会自动计算新老节点的差异，根据差异对界面进行最小化重新渲染

### 10. state 与 props 区别
props 是一个从外部传进组件的参数，主要作用就是父组件向子组件传递数据，但是 props 对于使用它的组件来说是只读的，一旦赋值不能修改，只能通过外部组件主动传入新的 props 来重新渲染子组件
state 一个组件的显示形态可以由数据状态和外部参数决定，外部参数是 props ，数据状态就是 state ，首先，在组件初始化的时候，用 this . state 给组件设定一个初始的 state ，在第一次渲染的时候就会用这个数据来渲染组件， state 不同于 props 一点时， state 可以修改，通过 this.setState() 方法来修改 state 

### 11. shouldComponentUpdate是做什么的
这个生命周期钩子是来解决这个问题：
在更新数据的时候用 setState 修改整个数据，数据变了之后，遍历的时候所有内容都要被重新渲染，数据量少还好，数据量大就会严重影响性能。
解决办法：
1.shouldcomponentupdate 在渲染前进行判断组件是否更新，更新了再渲染
2.purecomponent（纯组件）省去了虚拟 dom 生成和对比的过程，在类组件中使用
3.react.memo() 类似于纯组件在无状态组件中使用
