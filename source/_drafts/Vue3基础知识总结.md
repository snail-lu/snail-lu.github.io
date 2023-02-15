---
title: Vue3基础知识总结
date: 2022-11-29 17:11:13
summary: Vue3常用基础知识总结
tags:
- Vue
- Vue3
- 面试
categories:
- [前端进阶]
- [读书笔记]
---

### 1. Vue3新特性
- [组合式 API](https://cn.vuejs.org/guide/extras/composition-api-faq.html)
- [单文件组件中的组合式 API 语法糖 (`<script setup>`)](https://cn.vuejs.org/api/sfc-script-setup.html)
- [Teleport 组件](https://cn.vuejs.org/guide/built-ins/teleport.html)
- [Fragments 片段（多个根节点的组件）](https://v3-migration.vuejs.org/zh/new/fragments.html)
- [Emits 组件选项](https://cn.vuejs.org/api/options-state.html#emits)
- [单文件组件中的状态驱动的 CSS 变量 (`<style>` 中的 v-bind)](https://cn.vuejs.org/api/sfc-css-features.html#v-bind-in-css)
- [...](https://v3-migration.vuejs.org/zh/)


### 2. 生命周期钩子
> 每个 Vue 组件实例在创建时都需要经历一系列的初始化步骤，比如设置好数据侦听，编译模板，挂载实例到 DOM，以及在数据改变时更新 DOM。在此过程中，它也会运行被称为生命周期钩子的函数，让开发者有机会在特定阶段运行自己的代码。

选项式API | 组合式API | 描述
:---:|:---:|---
beforeCreate | - | 组件实例被创建之初，组件的属性生效之前
created | - | 在组件实例处理完所有与状态相关的选项后调用。 此时响应式数据、计算属性、方法和侦听器已经设置完成，但挂载阶段还未开始，因此 $el 属性仍不可用
beforeMount | onBeforeMount | 在组件被挂载之前调用
mounted | onMounted | 组件挂载完成后调用
beforeUpdate | onBeforeUpdate | 组件数据更新后，DOM更新之前调用
updated | onUpdated | 组件因为响应式状态变更而更新其 DOM 树之后调用
beforeUnmount | onBeforeUnmount | 在组件实例被卸载之前调用
unmounted | onUnmounted | 在组件实例被卸载之后调用
activated | onActivated | keep-alive 专属，组件被激活时调用
deactivated | onDeactivated | keep-alive 专属，组件被移除时调用
errorCaptured | onErrorCaptured | 在捕获了后代组件传递的错误时调用
renderTracked | onRenderTracked | 仅在开发模式下可用，当组件渲染过程中追踪到响应式依赖时调用
renderTriggered | onRenderTriggered | 仅在开发模式下可用，当响应式依赖的变更触发了组件渲染时调用
serverPrefetch | onServerPrefetch | 仅在服务端渲染时可用，在组件实例在服务器上被渲染之前调用
![Vue组件实例的生命周期](https://cn.vuejs.org/assets/lifecycle.16e4c08e.png)