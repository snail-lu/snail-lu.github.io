---
title: JS模块化规范总结
tags:
---

### CommonJS规范
Node.js中所遵循的模块规范，同步的方式加载模块
该规范约定一个文件就是一个模块，每个模块都有独立的作用域，通过`module.exports`导出成员，再通过`require`函数载入模块。

### AMD
`Asynchronous Module Definition`，浏览器端所遵循的模块规范，异步模块定义规范
AMD，CMD，commonJS，UMD，es6