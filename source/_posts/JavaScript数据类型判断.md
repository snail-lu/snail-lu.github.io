---
title: JavaScript数据类型判断
date: 2022-04-24 14:27:27
summary: JS中基本数据类型和引用数据类型的常用判别方法
tags:
- JavaScript
- 面试
categories:
- [前端进阶]
---

### 数据类型
- 基本数据类型：`String`、`Number`、`Boolean`、`Undefined`、`Null`、`Symbol`
- 引用数据类型：`Object`

### 数据类型判断方法
- `typeof`
- `instanceof`
- `===`
- `Object.is()`
- `Array.isArray()`
- `isNaN()`

#### `typeof`
对一个值使用`typeof`操作符的返回值（返回值类型为字符串）有以下几种：
- "undefined" --- 值未定义
- "boolean"   --- 布尔值
- "string"    --- 字符串
- "number"    --- 数值
- "object"    --- 对象或null
- "function"  --- 函数

#### `instanceof()`
检测某个变量是否是某个构造函数的实例。常用的一些JS原生引用类型有：
- `o instanceof Object` --- 变量o是否是Object类型的实例
- `o instanceof Array`  --- 变量o是否是Array类型的实例
- `o instanceof RegExp` --- 变量o是否是RegExp类型的实例
- `o instanceof Date`   --- 变量o是否是Date类型的实例
- `o instanceof Function` --- 变量o是否是Function类型的实例
- `o instanceof Boolean` --- 变量o是否是Boolean类型的实例
- `o instanceof String` --- 变量o是否是String类型的实例
- `o instanceof Number` --- 变量o是否是Number类型的实例


#### `===`
判断两个操作数的数据类型和值是否完全一致。
- `"5" === 5` --- false

#### `Object.is()`
弥补了全等运算符`===`存在的两个怪异点：
- `+0 === -0` --- true
- `NaN === NaN` --- false
- `Object.is(+0, -0)` ---  false
- `Object.is(NaN, NaN)` --- true
其他判断结果与`===`结果一致。

#### `Array.isArray()`
判断一个值是否是一个`Array`。解决了网页中存在多个`iframe`（即存在多个多个全局执行环境），`instanceof`无法判断`Array`类型的问题
- `Array.isArray([])` ---  true
- `Array.isArray(Array.prototype)` --- true

示例代码：
```js
// 获取窗口中最后一个框架中的Array构造函数
xArray = window.frames[window.frames.length-1].Array;
var arr = new xArray(1,2,3); // [1,2,3]

console.log(Array.isArray(arr));  // true
console.log(arr instanceof Array); // false
```

#### `isNaN()`
判断指定值是否为数字，是数字返回`false`，否则返回`true`。 `isNaN()` 在判断参数是否为数字之前，会首先使用 `Number()` 对参数进行数字类型的转换。所以 `isNaN(value)` 其实等效于：`isNaN(Number(value))`。当参数 `value` 能被 `Number()` 转换为数字时，结果返回 `false`，否则返回 `true`。
```js
console.log(isNaN('100'))  // false
console.log(isNaN(''))  // false
console.log(isNaN('100px'))  // true
console.log(isNaN(undefined))  // true
console.log(isNaN({})) // true
console.log(isNaN([])) // false
console.log(isNaN(null)) // false
```