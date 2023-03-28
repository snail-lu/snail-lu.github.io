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
`Hook` 是一些可以让你在函数组件里“**钩入**” React state 及生命周期等特性的函数。`Hook` 不能在 `class` 组件中使用 —— 这使得你不使用 `class` 也能使用 `React`。

#### 实现原理
每个 `useXxx` 的 `hook` 都有 **创建阶段**（`mountXxx`） 和 **更新阶段**（`updateXxx`） 两个阶段。在创建阶段，函数组件会将内部用到的所有的 `hook` 通过环状单向链表的形式，保存在组件对应 `fiber` 节点的 `memoizedState` 属性上。每个 `hook` 内部都有自己的状态和依赖维护着。在更新阶段，会按照链表顺序依次执行更新函数。

#### 为什么不能在循环、条件或嵌套函数中调用Hook
💡类似问题：为什么不能改变 hook 的执行顺序
一个函数组件中的多个 `Hook` 是以链表的结构进行保存的，只要 `Hook` 的调用顺序在多次渲染之间保持一致，`React` 就能正确地将内部 `state` 和对应的 `Hook` 进行关联， 在循环、条件或嵌套函数中，有可能会导致`hook`的调用顺序发生改变，会发生状态错乱的问题。

#### 内置Hook
- `useState`：创建一个state及对应的更新函数
- `useEffect`：操作副作用（数据获取、订阅或者手动修改过 DOM等），类似于componentDidMount 和 componentDidUpdate生命周期钩子
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
虚拟 `DOM` 也就是常说的虚拟节点，它是真实`DOM`的`js`对象表示。
频繁的操作真实`DOM` ，或大量造成页面的重绘和回流，十分消耗性能。有了虚拟 `DOM`，可以利用 js 实现的`DOM Diff` 算法进行新旧虚拟 `DOM` 的比较，从而更高效快速地确定了被更改的`DOM`，避免了没有必要的 `DOM` 操作，从而提高性能。
大致工作流程：
- 当数据发生变化，比如setState时，会引起组件重新渲染，整个UI都会以virtual dom的形式重新渲染
- 然后收集差异也就是diff新的virtual dom和老的virtual dom的差异
- 最后把差异队列里的差异，比如增加节点、删除节点、移动节点更新到真实的DOM上

### 7. Diff 算法
把树形结构按照层级分解，只比较同级元素，给列表结构的每个单元添加唯一的 key 值，方便比较。
基于三个策略：
- **Tree Diff**: web UI 中 dom 节点跨层级的移动操作特别少，可以忽略不计
- **Component Diff**: 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件会生成不同的树形结构
- **Element Diff**: 对于同一层级的一组子节点，他们可以通过唯一的 id 进行区分

### 8．对组件的理解
可组合，可复用，可维护，可测试

### 9．调用 setState 之后发生了什么？
`React` 在调用 `setState` 后， 会将传入的参数对象和组件当前的状态合并，触发调和过程。在调和过程中， React 会根据新的状态构建 React 元素树，在得到元素树之后， React 会自动计算新老节点的差异，根据差异对界面进行最小化重新渲染。
`setState`的实现机制类似于`Vue`的`$nextTick`和浏览器的事件循环机制，每个`setState`都会被 `React` 加入到任务队列，多次对同一个`state`使用 `setState` 只会返回最后一次的结果，因为它不是立刻就更新，而是先放在队列中，等时机成熟在执行批量更新。React18以后，使用了 `createRoot` Api后，所有`setState`都是异步批量执行的

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


### 12. Redux工作原理
Redux是一个状态管理库，使用场景：
- 跨层级组件数据共享与通信
- 一些需要持久化的全局数据，比如用户登录信息

使用单例模式实现
- Store 一个全局状态管理对象
- Reducer 一个纯函数，根据旧state和props更新新state
- Action 改变状态的唯一方式是dispatch action

### 13. React Router工作原理
路由负责定义路径和组件的映射关系
导航负责触发路由的改变
路由器根据Route定义的映射关系为新的路径匹配对应的逻辑
`BrowserRouter`使用HTML5的 `history` Api实现路由跳转
`HashRouter`使用URL的`hash`属性控制路由跳转


### 14. React事件机制
#### 合成事件
React基于浏览器的事件机制实现了一套自身的事件机制，它符合W3C规范，包括事件触发、事件冒泡、事件捕获、事件合成和事件派发等，虽然合成事件不是原生DOM事件，但它包含了原生DOM事件的引用，可以通过e.nativeEvent访问

#### 事件机制
React16的事件绑定在`document`上， React17以后事件绑定在`container`上（指 ReactDOM.render(app,container)中的`container`）
- React所有的事件绑定在container上(react17以后),而不是绑定在DOM元素上（作用：减少内存开销，所有的事件处理都在container上，其他节点没有绑定事件）
- React自身实现了一套冒泡机制，不能通过`return false`阻止冒泡
- React通过`SytheticEvent`实现了事件合成

### 15. 函数组件和class组件的区别
#### class组件
特点：
- 有组件实例
- 有生命周期
- 有 state 和 setState
  
缺点：
- 大型组件很难拆分和重构，变得难以测试
- 相同业务逻辑分散到各个方法中，可能会变得混乱
- 复用逻辑可能变得复杂，如 HOC 、Render Props

#### 函数组件
特点：
- 没有组件实例
- 没有生命周期
- 没有 state 和 setState，只能接收 props
- 函数组件是一个纯函数，执行完即销毁，无法存储 state

`React` 中更提倡函数式编程，因为函数更灵活，更易拆分，但函数组件太简单，所以出现了`hook`，`hook`就是用来增强函数组件功能的。