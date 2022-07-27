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
- Hash模式：通过监听`hashchange`事件实现
- History模式：基于HTML5的 `history` api实现
- Abstract模式：非浏览器环境下，强制使用此模式

### 5. 动态路由实现权限控制 
1.通过 `router.beforeEach()`进行权限校验，获取角色可访问的路由数据。

2.通过 `vue-router` 的 `addRoute()` 来动态增加路由。