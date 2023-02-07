---
title: JavaScript基础知识总结
date: 2021-05-01 22:17:16
summary: JavaScript知识进阶
tags:
- JavaScript
- 面试
categories:
- [前端进阶]
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

### 3. async/await
`async/await` 是 `Generator`函数的语法糖，作用是**用同步的方式，执行异步操作**。
`async` 函数的实现原理，就是将 `Generator` 函数和自动执行器，包装在一个函数里。
**实现**
```js
async function fn(args) {
  // ...
}

// 等同于

function fn(args) {
    // spawn是自动执行器函数，自动调用generator的next方法
    return spawn(function* () {
        // ...
    });
}

// spawn函数实现
function spawn(genF) {
    return new Promise(function(resolve, reject) {
        const iterator = genF();
        function step(nextF) {
            let next;
            try {
                next = nextF();
            } catch(e) {
                return reject(e);
            }
            if(next.done) {
                return resolve(next.value);
            }
            Promise.resolve(next.value).then(function(v) {
                step(function() { 
                    return iterator.next(v); 
                });
            }, function(e) {
                step(function() { 
                    return iterator.throw(e); 
                });
            });
        }
        step(function() { return iterator.next(undefined); });
    });
}
```

### 4. ES6常用新特性
[详解](https://snaillu.gitee.io/2019-10-30-es6-chang-yong-te-xing-zong-jie.html)
### 5. 数据类型的判断方法
[JavaScript数据类型判断](https://snaillu.gitee.io/2022-04-24-javascript-shu-ju-lei-xing-pan-duan.html)
### 6. 继承
[JavaScript中的继承](https://snaillu.gitee.io/2022-07-16-javascript-zhong-de-ji-cheng.html)
### 7. 原型链
[JavaScript原型与原型链](https://snaillu.gitee.io/2021-09-30-javascript-yuan-xing-yu-yuan-xing-lian.html)

### 8. 闭包
`闭包`是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式，就是在一个函数内部创建另一个函数。
```js
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0](); // 3
data[1](); // 3
data[2](); // 3

// 改善输出结果
for (var i = 0; i < 3; i++) {
  data[i] = (function (i) {
    return function() {
        console.log(i);
    }
  })(i);
}
data[0](); // 0
data[1](); // 1
data[2](); // 2
```
### 9. 事件循环、微任务、宏任务
[JavaScript中的微任务与宏任务](https://snaillu.gitee.io/2021-12-14-javascript-zhong-de-wei-ren-wu-yu-hong-ren-wu.html)

### 10. 浏览器缓存
[](https://snaillu.gitee.io/2022-07-15-liu-lan-qi-yuan-li-zhi-shi-zong-jie.html#toc-heading-6)
### 11. this指向
[JavaScript中的this](https://snaillu.gitee.io/2022-05-01-javascript-zhong-de-this.html)
### 12. 箭头函数和普通函数有什么区别
- 没有`this`、`super`、`arguments`及`new.target`绑定，`this`、`super`、`arguments`及`new.target`的值由所在的、最靠近的非箭头函数来决定；
- 不能被使用`new`调用；
- 没有原型；
- 不能更改`this`；
- 不允许重复的具名参数，传统的函数中参数可以重名。

### 13. 防抖与节流
**防抖**：事件持续触发不执行，停止触发指定时间之后才执行一次。
```js
/**
 * @fn : 要执行的函数
 * @delay : 执行函数的时间间隔（毫秒）
 */ 
 
function debounce(fn, delay) {
    let timer;
    return function(...args) {    
        timer && clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(this, args);
        }, delay)
    }
}
```
**节流**：事件重复触发，不会每次都执行，每间隔一段时间执行一次。
```js
/**
 * @fn : 要执行的函数
 * @delay : 每次函数的时间间隔
 */  
function throttle(fn, delay) {
    let timer;    // 定时器
 
    return function(...args) {
        if(timer) return;
        timer = setTimeout(() => {
            timer = null;
            fn.apply(this, args);
        }, delay);
    }
}
```
### 14. instanceof原理
```js
function instance_of(left, right) {
  const RP = right.prototype; // 构造函数的原型
  while(true) {
    if (left === null) {
      return false;
    }
    if (left === RP) { // 一定要严格比较
      return true;
    }
    left = left.__proto__; // 沿着原型链重新赋值
  }
}
```
### 15. `==` 和 `===`
- `==` 和 `!=` --- 先转换数据类型再比较
- `===` 和 `!==` --- 仅比较而不转换数据类型

`==`和 `!=` 遵循下列转换规则：
- 如果有一个操作数是**布尔值**，则在比较相等性之前先将其转换为数值：**false转换为0，true转换为1**
- 一个是字符串，一个是数值，则在比较相等性之前先将字符串转换为数值
- 一个是对象，一个不是对象，则调用对象的 `valueOf()` 方法，得到基本数据类型后再按前面的规则比较
- `null` 和 `undefined` 不能转换为其他任何值

`==`和`!=` 在比较时需要遵循下列规则：
- `null` 和 `undefined` 是相等的
- `NaN` 与任何值都不相等，包括它自己
- 两个操作数都是对象，如果两个操作数指向同一个对象，则为`true`，否则为`false`

特殊情况比较结果：
|表达式|值|
|---|---|
|null == undefined|true|
|NaN == NaN|false|
|false == 0|true|
|true == 1|true|
|true == 2|false|
|undefined == 0|false|
|null == 0|false|
### 16. call、bind、apply原理
> MDN:
> call() 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。
```js
/**
 * call的模拟实现
 */
Function.prototype.myCall = function(context) {
    // this指向调用myCall方法的函数
    if (typeof this !== 'function') { 
        throw new TypeError('Error') 
    }
    context = context || window;

    // 将调用myCall的函数赋值为context对象的方法
    context.fn = this;
    const args = [...arguments].slice(1);
    // 利用context.fn()来执行原函数，原函数中的this就指向了context
    const result = context.fn(...args) ;
    delete context.fn ;
    return result;
}
```
> MDN:
> apply() 方法调用一个具有给定 this 值的函数，以及以一个数组（或一个类数组对象）的形式提供的参数。
```js
/**
 * apply的模拟实现
 * 原理与call一样，区别仅在于参数的处理上
 */
Function.prototype.myApply = function(context) { 
    if (typeof this !== 'function') { 
        throw new TypeError('Error') 
    } 
    context = context || window;
    context.fn = this;  
    let result // 处理参数和 call 有区别  
    if (arguments[1]) { 
        result = context.fn(...arguments[1]) 
    } else { 
        result = context.fn() 
    } 
    delete context.fn;
    return result; 
}
```
> MDN:
> bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
```js
/**
 * bind的模拟实现
 * 
 */
Function.prototype.myBind = function (context) {
    if (typeof this !== 'function') {
        throw new TypeError('Error')
    }
    const _this = this
    // 保存调用bind时传递的参数
    const args = [...arguments].slice(1)
    // 返回一个函数
    return function F() {
        // 因为返回了一个函数，我们可以 new F()，所以需要判断
        if (this instanceof F) {
            return new _this(...args, ...arguments)
        }
        // 修改this及合并参数
        return _this.apply(context, args.concat(...arguments))
    }
}
```
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