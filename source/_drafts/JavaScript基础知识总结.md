---
title: JavaScript基础知识总结
tags:
---

### 1. 作用域和作用域链
JS中存在`全局作用域`、`函数作用域`以及`块作用域`（ES6新增），各个作用域之间相互嵌套组成了一条作用域链。作用域查找始终从运行时所处的最内部作用域开始，沿着作用域链逐级向上进行查找，直到遇见 第一个匹配的标识符为止。
```js
// 全局作用域
const a = 10;
function foo() {
    // foo函数作用域，可以访问全局作用域中的变量
    const b = a * 2;
    function bar(c) {
        // bar函数作用域，可以访问foo函数作用域及全局作用域中的变量
        console.log(a, b, c)
    }

    bar(30)
}

foo(); // 10, 20, 30
```
### 2. Promise
**Promise是什么**
`Promise` 是 `ES6` 新增的语法，是一种异步编程的一种解决方案，`Promise` 本质上是一个绑定了回调的对象。 `Promise` 在一定程度上解决了回调函数的书写结构问题，解决了回调地狱的问题。`Promise` 可以看作是一个状态机，它有三种状态：`pending`，`fulfilled`，`rejected`，其中初始状态是 `pending`，可以通过函数 `resolve` 把状态变为 `fulfilled`，或者通过函数 `reject` 把状态变为 `rejected`，状态一经改变就不能再次变化。

**常用API**
`Promise.all()`: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise。只有当可迭代对象中的所有Promise都完成，返回的Promise才会完成，只要有一个被拒绝，返回的Promise就会被立即拒绝。

`Promise.race()`: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise，只要有一个被解决（完成或拒绝），返回的Promise就会被立即解决。

`Promise.any()`: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise，只要有一个被完成，返回的Promise就会变成完成状态，如果所有的promise都被拒绝，返回的promise就会变成拒绝状态。

`Promise.allSettled()`: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise。当可迭代对象中的所有Promise都解决时，返回的Promise会被完成，完成的结果是一个包含所有promise结果的对象数组。

**实现**
[Promise实现原理](https://snail-lu.github.io/2022-04-25-promise-shi-xian-yuan-li.html)

### 3. async-await的实现原理
### 4. ES6常用新特性
### 5. 数据类型的判断方法
### 6. 继承
### 7. 原型链
### 8. 闭包
### 9. 事件循环、微任务、宏任务
### 10. 浏览器缓存
### 11. this指向
### 12. 箭头函数和普通函数有什么区别
### 13. 防抖与节流
### 14. instanceof原理
### 15. ===原理
### 16. call、bind、apply原理

### 17. new的原理
使用 `new` 来调用函数，会自动执行下面的操作：
1. 创建一个空对象`{}`
2. 将这个新对象的原型对象指向构造函数的原型属性，以继承原型上的方法
3. 将构造函数中的this指向到新创建的对象并执行构造函数，以获取实例属性
4. 如果构造函数执行后返回了对象，就将该对象作为结果返回；否则就将上面创建的新对象作为结果返回

```js
// 简单实现
function myNew(Fn, ...args) {
    // 原型式继承
    const obj = Object.create(Fn.prototype);
    // 执行构造函数
    const res = Fn.apply(obj, args);
    return res instanceof Object ? res : obj;
}

function Person(name, age) {
    this.name = name;
    this.age = age;
    return this;
}

const student1 = myNew(Person, 'Mike', 20);
console.log(student1); //  {"name":"Mike","age":20}
```