---
title: webpack基础知识总结
summary: webpack基础知识总结
date: 2021-09-04 20:47:42
tags:
- Webpack
categories:
- [前端进阶]
---

### 1. webpack打包原理
以一个或多个文件作为打包入口，将整个项目的所有文件编译组合成一个或多个文件输出，输出的文件可以直接在浏览器端运行。
打包过程可以简单归结如下：
- **初始化**：启动构建，读取与合并配置参数，加载 `Plugin`，实例化 `Compiler`
- **编译**：从 `Entry` 出发，针对每个 `Module` 串行调用对应的 `Loader` 去翻译文件的内容，再找到该 `Module` 依赖的 `Module`，递归地进行编译处理
- **输出**：将编译后的 `Module` 组合成 `Chunk`，将 `Chunk` 转换成文件，输出到文件系统中

### 2. 热更新原理
客户端从服务端拉去更新后的文件，准确的说是 `chunk diff` (chunk 需要更新的部分)，实际上 `webpack dev server` 与浏览器之间维护了一个 `Websocket`，当本地资源发生变化时，`webpack dev server` 会向浏览器推送更新，并带上构建时的 `hash`，让客户端与上一次资源进行对比。客户端对比出差异后会向 `webpack dev server` 发起 `Ajax` 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 jsonp 请求获取该chunk的增量更新。
后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。
### 3. loader和plugin区别

### 4. 常用的loader和plugin
**loader**:
- `babel-loader`: ES6转ES5
- `sass-loader`：SASS语法转CSS
- `ts-loader`: TS转JS
- `postcss-loader`：css语法降级及增加浏览器前缀等功能
- `eslint-loader`：JS代码语法及风格检查
- `file-loader`：将代码中引入的文件生成到输出目录，并且在JS代码里返回该文件的地址

**plugin**: 
- `webpack-bundle-analyzer`：打包分析插件

### 5. 如何优化包体积

### 6. 如何优化构建速度

### 7. 分包机制

### 8. webpack5 的缓存方式

### 9. sourcemap原理及使用


### 10. 文件指纹
文件指纹是打包后输出的文件名的后缀。