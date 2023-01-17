---
title: JS模块化规范总结
date: 2022-12-29 17:11:13
summary: JS中的常见模块化规范总结
tags:
- JavaScript
categories:
- [前端进阶]
---

### 1. 概述
| 序号 | 规范 | 适用场景 | 常见实现 |
|:---|:---|:---|:---|
| 1 | CommonJS  | 服务器端 | - |
| 2 | AMD | 浏览器端 | require.js、curl.js |
| 3 | CMD | 服务器端 | sea.js | 
| 4 | UMD | 浏览器端和服务器端 |
| 5 | ES6 Module | 浏览器端和服务器端 |

### 2. CommonJS规范
**Node.js**中所遵循的模块规范，以同步的方式加载模块。
该规范约定一个文件就是一个模块，每个模块都有独立的作用域，模块必须通过 `module.exports` 导出对外的变量或接口，通过 `require()` 来导入其他模块的输出到当前模块作用域中。
#### 基本用法
```js
// moduleA.js
module.exports = function( value ){
    return value * 2;
}

// moduleB.js
var multiplyBy2 = require('./moduleA');
var result = multiplyBy2(4);
```
### 3. AMD
**AMD** 的全称是`Asynchronous Module Definition`，意思是“**异步模块定义**”。它采用异步方式加载模块，模块的加载不影响它后面语句的运行，所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。使用AMD规范进行开发的时候需要引入第三方的库函数，常用的有`require.js`和`curl.js`。
#### 基本用法
以`require.js`为例：
```js
// 定义模块
// moodule1.js
define('module1', [], function() {
    // ...
    return {
        // ...
    }
})

// 加载模块
// index.js
require(['module1'], function(module1) {
    // ...
})
```

### 4. CMD
**CMD** 的全称是`Common Module Definition`，意思是“**同步模块定义**”，属于`CommonJS`的一种规范。使用CMD规范进行开发的时候需要引入第三方的库函数`Sea.js`。

#### 基本用法
```js
// 定义模块
// module1.js
define(function(require, exports, module) {
    // ...
    module.exports = {
        // ...
    }
})

// module2.js
define(function(require, exports, module) {
    var module1 = require('./module1.js')
    // ...
    module.exports = {
        // ...
    }
})

// 加载模块
seajs.use(['module1.js', 'module2.js'], function(module1, module2) {
    // ...
})
```

### 5. UMD
**UMD** 的全称是`Universal Module Definition`，意思是“**通用模块定义**”，他是为了让模块同时兼容`AMD`、`CommonJS`、`CMD`等规范而出现的，多被一些需要同时支持浏览器端和服务端引用的第三方库所使用。

#### 基本用法
```js
(function(root, factory) {
	if (typeof define === 'function' && define.amd) { // 适配AMD
		define(factory);
	} else if (typeof exports === 'object') { // 适配CommonJS
		module.exports = factory();
	} else { // 浏览器全局变量
		root.NProgress2 = factory();
	}
 
})(this, function() { // 模块内容
    // ...
	return {
        // ...
    }
});
```

### 6. ES6 Module
**ES6** 在语言标准的层面上实现的模块功能，有望成为浏览器和服务器通用的模块解决方案。
#### 基本用法
```js
// 导出
export const PI = 3.14;                   // 导出变量
export function func1(){                  // 导出方法
    console.log("this is func1");
}
export let person = { name: "Nike" };       // 导出变量

// 导入
import { PI, func1, person } from './module1.js';
```

### 参考文档
[【深度全面】前端JavaScript模块化规范进化论](https://segmentfault.com/a/1190000023711059)