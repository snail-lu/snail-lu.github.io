---
title: webpack基础知识总结
summary: webpack基础知识总结
date: 2021-09-04 20:47:42
tags:
- Webpack
categories:
- [前端进阶]
---

### 1. webpack构建流程
以一个或多个文件作为打包入口，将整个项目的所有文件编译组合成一个或多个文件输出，输出的文件可以直接在浏览器端运行。
构建流程可以简单归结如下：
- **初始化**：启动构建，读取与合并配置参数，加载 `Plugin`，实例化 `Compiler`对象
- **编译**：从 `Entry` 出发，调用对应的 `Loader` 去编译文件的内容，编译好的文件内容解析出 `AST` 语法树，从语法树中得到模块依赖关系图，递归地进行编译处理
- **输出**：将编译后的 `Module` 组合成 `Chunk`，将 `Chunk` 根据配置的输出路径和文件名转换成文件并输出到文件系统中

### 2. 热更新原理
- 当某一个文件或模块发生变化时，webpack 监听到文件变化会对文件重新编译打包，并生成下次热更新的 `hash`标识。
- `WDS`(**webpack dev server**)与浏览器之间维护了一个 `websocket` 通信，当 webpack 编译完成之后，`WDS` 会向浏览器发送更新通知，并带上下次热更新的 `hash`值。
- 浏览器收到更新通知后，会使用上一次的 `hash` 值向 `WDS` 发起 `hash.hot-update.json` 请求来获取更改内容（文件列表、下一次更新的`hash`），之后浏览器再借助这些信息继续向 `WDS` 发起 `hash值.hot-update.js` 请求获取增量更新的内容。拿到需要更新的内容后，`HotModuleReplacementPlugin` 将更新后的代码进行替换，并局部刷新浏览器。

### 3. loader和plugin区别
- `loader` 是文件加载器，帮助 webpack 处理那些非 JavaScript 文件，如css文件、图片等。loader 其实就是一个 function，接收一个参数 source，就是当前的文件内容，直接对文件进行一些处理，将文件转换为适合webpack模块系统处理的格式。Loader的配置项有`test`、`include`、`exclude`、`use`等属性。
- `plugin` 是插件，用来增强webpack的功能，本质就是一个类（或构造函数），针对的是webpack打包的整个过程，它并不直接操作文件，而是基于事件机制工作，会监听webpack执行流程中的生命周期中的事件，当特定的事件发生时，执行相应的操作，比如生成html文件、提取css、压缩js代码等。Plugin额配置选项包括插件的实例化参数和插件的应用范围等。

### 4. 常用的loader和plugin
**loader**:
- `babel-loader`: ES6转ES5
- `sass-loader`：SASS语法转CSS
- `css-loader`：解析css文件，转换为js模块
- `style-loader`：将css-loader解析后的内容通过js脚本的方式创建style标签注入到html页面中
- `ts-loader`: TS转JS
- `postcss-loader`：css语法降级及增加浏览器前缀等功能
- `eslint-loader`：JS代码语法及风格检查
- `file-loader`：将代码中引入的文件生成到输出目录，并且在JS代码里返回该文件的地址

**plugin**: 
- `clean-webpack-plugin`：打包前清除旧的打包文件
- `html-webpack-plugin`：生成HTML文件，自动引入webpack打包出的bundle
- `copy-webpack-plugin`：复制文件，一般用于将`public`目录的内容复制到输出目录
- `mini-css-extract-plugin`: css文件提取到单独的文件，而非使用`style-loader`插入到html代码中
- `optimize-css-assets-webpack-plugin`：压缩css代码
- `terser-webpack-plugin`：压缩js代码
- `webpack-bundle-analyzer`：打包体积分析
- `speed-measure-webpack-plugin`：打包速度分析
- `webpack-dev-server`：开发环境自动打包并实时预览、自动刷新浏览器

### 5. 如何优化打包结果
- `压缩代码`：使用`UglifyJsPlugin`压缩js代码，`CssMinimizerWebpackPlugin`压缩css代码，`HtmlWebpackPlugin`压缩HTML代码等
- `CDN加速`：静态资源尽量改用CDN加速，无法使用CDN加速的，尽可能使用插件进行压缩优化，比如使用`image-webpack-loader`压缩图片
- `Code Splitting`: 使用`splitChunksPlugin`将代码按照路由维度或组件分包，这样可以做到按需加载，同时提取公用代码及三方库到单独的包，浏览器就可以长期缓存这些无需频繁改动的公共代码
- `Tree Shaking`: 删除无用代码

### 6. 如何优化构建速度
- **搜索时间优化**，即搜索依赖项，以确定依赖关系的时间
  - 使用 loader 时通过 `test`、`include`、`exclude`配置项来尽可能精准命中文件
  - `resolve.extensions` 列表尽可能小，频率出现最高的文件后缀优先放到前面，以减小文件检索的尝试过程
- **解析时间优化**，即使用各种loader对文件进行解析的时间
  - 使用`thread-loader`（替代 happypack ）对打包较耗时的 loader 开启多进程打包
- **压缩时间优化**，即将各种依赖压缩生成到输出目录的时间
  - `terser-webpack-plugin` 启动多进程以提升构建速度（webpack5默认已开启）
- **二次打包时间优化**，即更改部分代码后，重新打包的时间
  - 在一些开销较大的 loader 之前使用 `cache-loader` 进行缓存
  - 使用 `hard-source-webpack-plugin` 进行中间缓存

### 7. Code Splitting
webpack的分包主要通过`SplitChunksPlugin`来实现。实现的分包方式主要有两种：
- 根据业务不同配置多个打包入口，输出多个打包结果（适用于多页应用程序）
- 结合`ES Modules`的动态导入特性，按需加载模块

### 8. webpack5 的缓存方式
webpack在开发模式下的打包结果默认会缓存在内存中，不会输出到静态文件。
> 在生产环境下禁用缓存，默认输出到静态文件。
```js
module.exports = {
    // 内存缓存
    cache: true, // development模式下默认。
    // cache: { type: 'memory' }, // 和上面等价。

    // 文件缓存
    cache: {
        type: 'filesystem', // 编译缓存将保存在文件中，不占用内存
    }
}
```

### 9. `source map`使用
`source map`，即编译后的代码与源代码的映射。
在`webpack.config.js`配置文件中使用`devtools`配置项来选择`source map`模式。
![](https://s1.ax1x.com/2022/09/15/vx7HTs.png)
在打包生产环境的代码时，为了避免向外暴露源码，一般选用`hidden-source-map`或`nosources-source-map`模式。
- `hidden-source-map`
仍然会产生完整的map文件，但是不会在 `bundle` 文件中添加对于 map 文件的引用。这样当打开浏览器开发者工具时，是无法看到map文件的，自然也就无法解析。如果我们自己想要追溯源码，可使用一些第三方服务，将map文件上传到第三方服务中（比如`Sentry`）。
- `nosources-source-map`
当打包部署后，我们可以在浏览器开发者工具的sources选项卡中看到源码的目录结构，但是文件内容会被隐藏起来。对于错误，仍然可以在console控制台中查看源代码的错误栈，或者console日志的准确行数。

### 10. webpackBootstrap做了什么？
1. 使用立即执行函数封装我们的代码，避免全局污染
2. 缓存每个模块的输出值并自执行函数
3. 定义了一些处理 `import`, `export` 或者是 `require` 的方法，做到不同规范的兼容

### 11. Tree Shaking机制的原理
`Tree-Shaking` 是一种基于 `ES Module` 规范的 `Dead Code Elimination` 技术，它会在运行过程中静态分析模块之间的导入导出，确定 `ESM` 模块中哪些导出值未被其它模块使用，并将其删除，以此实现打包产物的优化。
生产环境打包默认会开启 `Tree Shaking`，开发环境启动 `Tree Shaking` 需要同时满足三个条件：
- 使用 `ESM` 规范编写模块代码
- 配置 `optimization.usedExports = true`, 即开启标记未被使用的导出内容，`terser`压缩代码时会将这部分代码删除
- 满足下列条件之一：
    - 配置 `optimization.minimize = true`
    - 配置 `optimization.minimizer`

对于包含无副作用代码的模块，想要使用 `Tree Shaking` 跳过该模块，需要如下设置：
- 在`webpack.config.js`中设置`optimization.sideEffects = true`（生产环境打包无需配置，默认为 true），以告知 `webpack` 去辨识 `package.json` 中的 副作用 标记 `sideEffects`
- 在无副作用的模块的 `package.json` 中设置副作用标记 `sideEffects = false`，表明该模块没有副作用，未使用时可以被跳过不打包进 `bundle`
- 对于项目，如果确认自己项目中没有副作用代码，希望 `Tree Shaking` 时跳过没用到的js模块代码，同样可以在项目的`package.json` 中设置 `sideEffects = false`

### 12. 开发plugin
- 一个 JavaScript 命名函数或 JavaScript 类。
- 原型上定义一个 apply 方法。
- 指定一个绑定到 webpack 自身的事件钩子。
- 处理 webpack 内部实例的特定数据。
- 功能完成后调用 webpack 提供的回调。
```js
// 一个 JavaScript 类
class MyExampleWebpackPlugin {
  // 在插件函数的 prototype 上定义一个 `apply` 方法，以 compiler 为参数。
  apply(compiler) {
    // 指定一个挂载到 webpack 自身的事件钩子。
    compiler.hooks.emit.tapAsync(
      'MyExampleWebpackPlugin',
      (compilation, callback) => {
        console.log('这是一个示例插件！');
        console.log(
          '这里表示了资源的单次构建的 `compilation` 对象：',
          compilation
        );

        // 用 webpack 提供的插件 API 处理构建过程
        compilation.addModule(/* ... */);

        callback();
      }
    );
  }
}
```
> 备注：Compiler 和 Compilation的区别
> Compiler 是每次 Webpack 全部生命周期的对象，而 Compilation 是 Webpack 中每次构建过程的生命周期对象，Compilation 是通过 Compiler创建的实例。两个类都有自己生命周期，即有自己不同的 Hook，通过添加对应 Hook 事件，可以拿到各自生命周期关键数据和对象。Compilation 有个很重要的对象是 Stats 对象，通过这个对象可以得到 Webpack 打包后的所有 module、chunk 和 assets 信息，通过分析 Stats对象可以得到很多有用的信息，比如 webpack-bundle-analyzer 这类分析打包结果的插件都是通过分析 Stats 对象来得到分析报告的。


### 13. Tapable事件流