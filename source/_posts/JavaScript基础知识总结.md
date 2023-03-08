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
**作用域**：变量和函数的有效访问范围。分为`全局作用域`、`函数作用域`和`块作用域`。
**作用域链**：当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的**变量对象**构成的链表就叫做`作用域链`。
💡 [变量对象](https://tsejx.github.io/javascript-guidebook/core-modules/executable-code-and-execution-contexts/execution/variable-object/)：JavaScript执行一段可执行代码时，会创建对应的执行上下文，变量对象是与 执行上下文 相关的数据作用域，存储了在上下文中定义的 `变量` 和 `函数`。
### 2. Promise
**Promise是什么**
`Promise` 是 `ES6` 新增的语法，是一种异步编程的一种解决方案，`Promise` 本质上是一个绑定了回调的对象。 `Promise` 在一定程度上解决了回调函数的书写结构问题，解决了回调地狱的问题。`Promise` 可以看作是一个状态机，它有三种状态：`pending`、`fulfilled`和`rejected`，初始状态是 `pending`，可以通过函数 `resolve()` 把状态变为 `fulfilled`，或者通过函数 `reject()` 把状态变为 `rejected`，状态一经改变就不能再次变化。

**常用API**
- **Promise.all()**: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise。只有当可迭代对象中的所有Promise都完成，返回的Promise才会完成，只要有一个被拒绝，返回的Promise就会被立即拒绝。

- **Promise.race()**: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise，只要有一个被解决（完成或拒绝），返回的Promise就会被立即解决。

- **Promise.any()**: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise，只要有一个被完成，返回的Promise就会变成完成状态，如果所有的promise都被拒绝，返回的promise就会变成拒绝状态。

- **Promise.allSettled()**: 接受含多个受监视Promise的可迭代对象作为唯一参数，返回一个Promise。当可迭代对象中的所有Promise都解决时，返回的Promise会被完成，完成的结果是一个包含所有promise结果的对象数组。

**实现**
[Promise实现原理](https://snail-lu.github.io/2022-04-25-promise-shi-xian-yuan-li.html)
[Promise简易实现](https://jsrun.net/XTcKp/)

### 3. async/await
`async/await` 是 [Generator](https://snaillu.gitee.io/2021-12-19-shen-ru-li-jie-es6-du-shu-bi-ji.html#toc-heading-51) 函数的语法糖，作用是**用同步的方式，执行异步操作**，最常见的用法就是用同步的写法来替代`promise.then()`的嵌套问题。
```js
// 后续异步请求都依赖于前一个请求的结果
function fn() {
    request().then(res1 => {
        request(res1).then(res2 => {
            request(res2).then(res3 => {
                // ...
            })
        })
    })
}


// 替代写法
async function fn() {
    const res1 = await request()
    const res2 = await request(res1);
    const res3 = await request(res2);
    // ...
}
```
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
    // async函数最终返回的是一个promise
    return new Promise(function(resolve, reject) {
        const iterator = genF();
        function step(nextF) {
            let next;
            try {
                next = nextF();
            } catch(e) {
                return reject(e);
            }
            // next.done为true时，说明走到了iterator的最后一步，直接返回结果
            if(next.done) {
                return resolve(next.value);
            }
            // next.value有可能是常量或promise，所以这里采用Promise.resolve处理
            Promise.resolve(next.value).then(function(v) {
                // 递归调用step
                step(function() { 
                    return iterator.next(v); 
                });
            }, function(e) {
                step(function() { 
                    return iterator.throw(e); 
                });
            });
        }
        step(function() { return iterator.next(); });
    });
}
```

### 4. ES6常用新特性
💡 [ES6常用特性总结](https://snaillu.gitee.io/2019-10-30-es6-chang-yong-te-xing-zong-jie.html)
### 5. 数据类型的判断方法
💡[JavaScript数据类型判断](https://snaillu.gitee.io/2022-04-24-javascript-shu-ju-lei-xing-pan-duan.html)
### 6. 继承
💡[JavaScript中的继承](https://snaillu.gitee.io/2022-07-16-javascript-zhong-de-ji-cheng.html)
### 7. 原型链
💡[JavaScript原型与原型链](https://snaillu.gitee.io/2021-09-30-javascript-yuan-xing-yu-yuan-xing-lian.html)

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
[JavaScript之Event Loop](https://snaillu.gitee.io/2021-12-14-javascript-zhi-event-loop.html)

### 10. 深拷贝
**实现方式一：** `JSON.stringify`序列化和`JSON.parse`反序列化
缺点：对象中`undefined`、`function`、`symbol`这三种类型的值会被过滤掉
```js
const obj = {
  a: '123',
  b: 234,
  c: true,
  d: null,
  e: function() {console.log('test')},
  h: new Set([4,3,null]),
  i: Symbol('fsd'),
  k: new Map([ ["name", "test"],  ["title", "Author"]  ])
}
console.log(JSON.stringify(obj)); // {"a":"123","b":234,"c":true,"d":null,"h":{},"k":{}}

const newObj = JSON.parse(JSON.stringify(obj));
```

**实现方式二：** `Object.assign(target, source1, source2)`
缺点：无对对象里面嵌套的对象进行深拷贝，相当于只是对一层引用对象进行深拷贝
```js
const obj = {
  a: '123',
  b: 234,
  c: true,
  d: null
}

const newObj = Object.assign({}, obj);
```

**实现方式三：** ES6扩展运算符
缺点：无对对象里面嵌套的对象进行深拷贝，相当于只是对一层引用对象进行深拷贝
```js
const obj = {
  a: '123',
  b: 234,
  c: true,
  d: null
}

const newObj = {...obj};
```

**实现方式四：** 递归
```js
function deepClone(obj) {
  //判断拷贝的obj是对象还是数组
  const objClone = Array.isArray(obj) ? [] : {};
  if (obj && typeof obj === "object") { //obj不能为空，并且是对象或者是数组 因为null也是object
      for (key in obj) {
          if (obj.hasOwnProperty(key)) {
              if (obj[key] && typeof obj[key] === "object") { //obj里面属性值不为空并且还是对象，进行深度拷贝
                  objClone[key] = deepClone(obj[key]); //递归进行深度的拷贝
              } else {
                  objClone[key] = obj[key]; //直接拷贝
              }
          }
      }
  }
  return objClone;
}
```
### 11. this指向
[JavaScript中的this](https://snaillu.gitee.io/2022-05-01-javascript-zhong-de-this.html)
### 12. 箭头函数与普通函数的区别
- 没有`this`、`super`、`arguments`及`new.target`绑定，`this`、`super`、`arguments`及`new.target`的值由所在的、最靠近的非箭头函数来决定；
- 不能被使用`new`调用；
- 没有原型；
- 不能更改`this`；
- 不允许重复的具名参数，传统的函数中参数可以重名。

### 13. 防抖与节流
#### 防抖
指定时间内函数多次调用都会被重置，只会在最后一次触发结束后延时执行
常用使用场景：
- 按钮多次点击（发送验证码按钮、提交按钮、支付按钮等）
- input输入框输入事件

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

#### 节流
指定时间内，函数多次调用只执行一次
常用使用场景：
- 浏览器窗口`resize`、`scroll`事件
- 鼠标移动`mousemove`事件
- 上传进度事件
- 文档编辑隔一段时间自动保存

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
[在线演示DEMO](https://codepen.io/snaillu/pen/oNPLRRp)
### 14. instanceof原理
```js
function instance_of(left, right) {
  const RP = right.prototype; // 构造函数的原型
  while(true) {
    if (left === null) return false;
    if (left === RP) return true;
    left = Object.getPrototypeOf(left); // 沿着原型链重新赋值
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
        throw new TypeError('Error, caller must be a function') 
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

### 18. 事件委托
事件传播的 3 个阶段：
- 捕获阶段（Capturing phase）—— 从window，document 和根元素开始，事件向下扩散至目标元素的祖先
- 目标阶段（Target phase）—— 事件到达目标元素
- 冒泡阶段（Bubbling phase）—— 事件从元素上开始冒泡，一直到根元素，document 和 window。

#### 为何使用事件委托？
在 JavaScript 中，页面内事件处理程序的个数会直接影响页面的整体性能，因为每个事件处理程序都是对象，对象会占用内存，内存中的对象越多，页面的性能则越差。此外，事件处理程序需要与 DOM 节点进行交互，访问 DOM 的次数越多，引起浏览器重绘和重排的次数也就越多，从而影响页面的性能。

#### 事件委托实现原理
事件委托是利用事件的`冒泡原理`来实现的，大致可以分为三个步骤：
    1. 确定要添加事件元素的父级元素；
    2. 给父元素定义事件，监听子元素的冒泡事件；
    3. 使用 event.target 来定位触发事件冒泡的子元素。


### 19. Generator（生成器）和 Iterator（迭代器）
[迭代器和生成器](https://snaillu.gitee.io/2021-12-19-shen-ru-li-jie-es6-du-shu-bi-ji.html#toc-heading-51)
### 20. 纯函数
#### 定义
一个函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用，我们就把这个函数叫做**纯函数**。

```js
// 纯函数
function sum1(a, b) {
 return a + b
}

// 非纯函数，因为函数执行过程中改变了message的值，被认定产生了副作用
let message = 'hello'
function sum2(a, b) {
 message = 'hi'
 return a + b
}

const nums = [1, 2, 3, 4, 5]
// slice是纯函数
const newNums1 = nums.slice(1, 3)

// splice不是纯函数，因为它改改变了原数组的值
const newNums2 = nums.splice(1, 3);
```

#### 纯函数的价值
纯函数非常**靠谱**，执行一个纯函数你不用担心它会干什么坏事，它不会产生不可预料的行为，也不会对外部产生影响。虽然我们的程序不可避免的会产生副作用，比如 HTTP 调用、IO 操作等，但尽可能多的地方使用纯函数将大大提高程序的可读性、可调试性和可测试性，代码重构时也不必担心没注意到的副作用而搞乱了整个应用。
纯函数在函数式编程中被大量使用，而且诸如 ReactJS 和 Redux 等优质的库都要求使用纯函数。
正确地使用纯函数可以产生更加高质量的代码，并且也是一种更加干净的编码方式。


### 21. Ajax、Axios和fetch的区别
- `Ajax`是指一种无需刷新页面即可向服务器请求数据的技术。核心是`XMLHttpRequest对象`（简称`XHR`）。  
- `Axios`是通过 `Promise` 实现 `XHR` 封装的 `JavaScript` 库，在浏览器和`Node.js`环境中都可以运行。  
- `fetch` 是 `JavaScript` 原生库，是`Ajax`的替代方案，没有使用XMLHttpRequest对象。只可以在浏览器上使用。