---
title: Vuex基础知识总结
date: 2021-05-06 21:07:03
summary: Vuex基础知识
tags:
- Vue
- Vuex
- 面试
categories:
- [前端进阶]
- [读书笔记]
---


### 1. `Vuex`的工作原理
![Vuex工作原理图](https://vuex.vuejs.org/vuex.png)

1. vue组件中利用`dispatch`或`mapActions`触发`action`
2. `action`方法中执行异步操作，异步回调中利用`commit`触发`mutaion`
3. `mutation`方法中对`state`对象进行修改
4. `state`数据修改后，页面进行更新渲染


### 2. `Vuex`的实现原理
- Vuex的数据的响应式是通过`new Vue`实现的
- 组件中的`$store`属性是通过 `Vue.mixin` 在所有组件的`beforeCreate`生命周期内注入的

### 3. 核心概念
- **state**：vuex的基本数据，用来存储变量

- **getter**：从`state`派生的数据，相当于`state`的计算属性

- **mutation**：更新数据的同步函数（必须是同步的）。接受 state 作为第一个参数，提交载荷作为第二个参数。

- **action**：和`mutation`的功能大致相同，不同之处在于：`Action` 通过提交`mutation`来修改数据；`Action`可以包含任意异步操作。

- **modules**：模块化`Vuex`，可以让每一个模块拥有自己的`state`、`mutation`、`action`、`getters`，从而使结构清晰，方便管理。

### 4. Vuex数据持久化
- `vuex-persistedstate`插件
- `vuex-persist`插件
- `sessionStorage`和`localStorage`