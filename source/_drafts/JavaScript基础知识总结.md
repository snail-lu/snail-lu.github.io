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
### 2. promise的实现原理
promise是什么，常用方法，如何实现promise
### 3. ES6常用新特性
### 4. 数据类型的判断方法
### 5. 继承
### 6. 原型链
### 7. 闭包
### 8. 事件循环、微任务、宏任务
### 9. 防抖与节流
### 10. 浏览器缓存
### 11. this指向
### 12. 箭头函数和普通函数有什么区别
### 13. new的原理
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
### 14. async-await的实现原理
### 15. instanceof原理
### 16. ===原理
### 17. call、bind、apply原理