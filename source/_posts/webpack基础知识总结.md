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
- **编译**：从 `Entry` 出发，针对每个 `Module` 串行调用对应的 `Loader` 去翻译文件的内容，构建出模块依赖关系图，递归地进行编译处理
- **输出**：将编译后的 `Module` 组合成 `Chunk`，将 `Chunk` 转换成文件，输出到文件系统中

### 2. 热更新原理
`WDS`(**webpack dev server**)与浏览器之间维护了一个 `websocket`通信，当webpack编译器编译完成之后，`WDS` 会向浏览器发送更新通知，并带上构建时的 `hash`。浏览器对比出差异后会向 `WDS` 发起`xxxhash.hot-update.json`的`Ajax` 请求来获取更改内容（文件列表、下次更新的`hash`），客户端就可以再借助这些信息继续向 `WDS` 发起 `xxx/hash.hot-update.js` 的 `jsonp` 请求获取增量更新的文件。拿到需要更新的文件后，`HotModuleReplacementPlugin`将更新后的代码进行替换，并刷新浏览器。
### 3. loader和plugin区别
loader，它是一个转换器，将A文件进行编译成B文件，比如：将A.less转换为A.css，单纯的文件转换过程。
plugin是一个扩展器，它丰富了webpack本身，针对是loader结束后，webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack打包过程中的某些节点，执行广泛的任务

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

### 7. 分包
webpack的分包主要通过`SplitChunksPlugin`来实现

### 8. webpack5 的缓存方式
webpack在开发模式下的打包结果默认会缓存在内存中，不会输出到静态文件。
> 在生产环境下禁用缓存，默认输出到静态文件。
```js
module.exports = {
    // 内存缓存
    cache: true, // dev模式下默认。
    // cache: { type: 'memory' }, // 和上面等价。

    // 文件缓存
    cache: {
        type: 'filesystem', // 编译缓存将保存在文件中，不占用内存
    }
}
```

### 9. sourcemap原理及使用


### 10. 文件指纹
文件指纹是打包后输出的文件名的后缀。