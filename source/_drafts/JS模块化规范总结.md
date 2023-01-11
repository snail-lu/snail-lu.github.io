---
title: JS模块化规范总结
tags:
---

### 概述
| 序号 | 规范 | 适用场景 | 常见实现 |
| 1 | AMD | 浏览器端 | require.js、curl.js |
| 2 | CommonJS  | 服务器端 | - |
| 3 | CMD | 服务器端 | - | 
| 4 | UMD | 浏览器端和服务器端 |
| 5 | ES6 Module | 浏览器端和服务器端 |

### AMD
`Asynchronous Module Definition`，浏览器端所遵循的模块规范，异步模块定义规范
### CommonJS规范
Node.js中所遵循的模块规范，以同步的方式加载模块。

该规范约定一个文件就是一个模块，每个模块都有独立的作用域，模块必须通过 `module.exports` 导出对外的变量或接口，通过 `require()` 来导入其他模块的输出到当前模块作用域中。
```js
// moduleA.js
module.exports = function( value ){
    return value * 2;
}

// moduleB.js
var multiplyBy2 = require('./moduleA');
var result = multiplyBy2(4);
```




### 参考文档
[【深度全面】前端JavaScript模块化规范进化论](https://segmentfault.com/a/1190000023711059)