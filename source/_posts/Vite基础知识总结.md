---
title: Vite基础知识总结
summary: Vite构建工具知识总结
date: 2022-08-08 09:58:08
tags:
- Vite
categories:
- [前端进阶]
---

### 1. 前端构建工具的常见用途
- TypeScript编译: tsc
- React/Vue语法编译: react-compiler / vue-compiler
- SCSS/LESS语法编辑: sass-loader / less-loader
- 语法降级: babel
- 语法校验
- 代码格式化
- 代码压缩
- 热更新
- ......

### 2. 与webpack的区别
- `webpack` 是一个纯打包工具，`vite` 是更上层的工具链解决方案，类似（webpack + web 常用配置 + webpack-dev-server）
- 开发服务器的启动速度上 `vite` 会明显快于 `webpack`。这是因为 `webpack` 会先打包，然后再启动开发服务器，请求服务器时直接给予打包结果。而 `vite` 是直接启动开发服务器，请求哪个模块再对该模块进行实时编译。
由于现代浏览器本身就支持 `ES Module`，会自动向依赖的 `Module` 发出请求。`vite`充分利用这一点，将开发环境下的模块文件，就作为浏览器要执行的文件，而不是像 `webpack` 那样进行打包合并。
由于 `vite` 在启动的时候不需要打包，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快。当浏览器请求某个模块时，再根据需要对模块内容进行编译。这种按需动态编译的方式，极大的缩减了编译时间，项目越复杂、模块越多，`vite` 的优势越明显。
- 在 `HMR` 方面，当改动了一个模块后，仅需让浏览器重新请求该模块即可，不像 `webpack`那样需要把该模块的相关依赖模块全部编译一次，效率更高。
- 当需要打包到生产环境时，`vite`使用传统的 `rollup` 进行打包，因此，vite的主要优势在开发阶段
- 由于 `vite` 利用的是 `ES Module`，因此在代码中不可以使用 `CommonJS`规范引入依赖，（对于使用非ES module规范的第三方依赖，vite会使用esbuild进行依赖预构建，将其转换为es moudle规范）；而 `Webpack`构建的项目则默认支持多种模块化规范


### 3. vite的依赖预构建
首先vite会找到对应的依赖，调用`esbuild`（对js语法进行处理的一个库），将其他规范的代码转换为`ES module`规范的代码，然后将其放到当前目录下的 `node_modules/.vite/deps`下，同时对`ES module`规范的各个模块进行统一集成（将分散的多个模块改写到一个或几个文件）。

vite的依赖预构建解决了三个问题：
- 第三方依赖规范不统一的问题（esbuild）
- 对引入路径可以直接使用`.vite/deps`，方便代码中的路径重写
- 网络多包传输的性能问题（浏览器在解析`ES module`依赖时，一个导入会发出一个请求，第三方依赖中肯定会有很多不同的导入，请求过多会增加浏览器的性能负担，vite通过模块集成，将分散的导入集成到了一个或几个文件，减少了网络请求次数）

### 4. 环境变量
`vite`底层读取环境变量文件使用到的是第三方库`dotenv`，根据命令中带的`mode`去解析对应文件中的环境变量，放入到`process`对象下。比如执行命令`vite build --mode staging`，vite会读取并解析`.env.staging`文件。
- `.env`: 所有环境都要使用到的环境变量
- `.env.development`: 开发魂晶需要使用到的环境变量（vite默认给开发环境取名为development）
- `.env.production`: 生产环境需要使用到的环境变量（vite默认给生产环境取名为production）
- `.env.[mode]`: 自定义其他模式

在实际客户端开发中，vite提供了`import.meta.env`对象，可以从该对象上获取对应的环境变量。需要注意在环境变量文件中书写环境变量时，需要使用前缀`VITE_`开头。该前缀可以在`vite.config.js`中使用`envPrefix`自定义。