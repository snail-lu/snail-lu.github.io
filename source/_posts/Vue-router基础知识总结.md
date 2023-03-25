---
title: Vue Router基础知识总结
date: 2021-05-07 20:16:35
summary: Vue Router V3.x 基础知识
tags:
- Vue
- Vue Router
- 面试
categories:
- [前端进阶]
- [读书笔记]
---

### 1. 导航守卫
- 全局守卫
  - `beforeEach` 全局前置守卫 
  - `beforeResolve` 全局解析守卫 
  - `afterEach` 全局后置钩子
- 路由独享的守卫 `beforeEnter`
- 组件内守卫
  - `beforeRouteEnter` 组件的对应路由被验证前调用
  - `beforeRouteUpdate` 在路由改变且组件被复用时调用
  - `beforeRouteLeave` 离开渲染组件的对应路由时调用


### 2. 导航守卫解析流程
1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫(2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫(2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

### 3. route和router的区别
- `router` 是 `VueRouter` 的实例，是一个全局路由对象，包含了路由跳转的方法、钩子函数等。
- `route` 是一个当前正在跳转的路由对象，每一个路由都会有一个`route`对象，是一个局部对象，包含`path`,`params`,`hash`,`query`,`fullPath`,`matched`,`name`等路由信息参数。

### 4. 路由模式
#### Hash模式
`Hash` 模式是一种把前端路由的路径用 `#` 拼接在真实 `url` 后面的模式。当 `#` 后面的路径发生变化时，浏览器并不会重新发起请求，而是会触发 `onhashchange` 事件。

**特点：**
- hash变化会触发网页跳转，即浏览器的前进和后退。
- hash 可以改变 url ，但是不会触发页面重新加载（hash的改变是记录在 window.history 中），即不会刷新页面。也就是说，所有页面的跳转都是在客户端进行操作。因此，这并不算是一次 http 请求，所以这种模式不利于 SEO 优化。hash 只能修改 # 后面的部分，所以只能跳转到与当前 url 同文档的 url 。
- hash 通过 window.onhashchange 的方式，来监听 hash 的改变，借此实现无刷新跳转的功能。
- hash 永远不会提交到 server 端（可以理解为只在前端自生自灭）。

####  History模式
`history` API 是 HTML5 提供的新特性，允许开发者直接更改前端路由，即更新浏览器 URL 地址而不重新发起请求。

`history`对象的属性和方法：
- `length`: 表示历史记录中元素的数量
- `state`: 当前页面的state对象
- `pushState()`：往历史记录堆栈顶部添加一条记录，会触发`onpopstate`事件
- `replaceState()`：更改当前的历史记录，会触发`onpopstate`事件
- `go()`：到达指定历史记录
- `forward()`：页面前进，相当于`go(1)`
- `back()`：页面回退，相当于`go(-1)`

**特点：**
- 新的 url 可以是与当前 url 同源的任意 url ，也可以是与当前 url 一样的地址，但是这样会导致的一个问题是，会把重复的这一次操作记录到栈当中。
- 通过 history.state ，添加任意类型的数据到记录中。
- 可以额外设置 title 属性，以便后续使用。
- 通过 pushState 、 replaceState 来实现无刷新跳转的功能。

💡`history`模式会存在刷新报404的问题，需要服务器端配置来解决。

### 5. 动态路由实现权限控制 
1.通过 `router.beforeEach()`进行权限校验，获取角色可访问的路由数据。
2.通过 `vue-router` 的 `addRoute()` 来动态增加路由。