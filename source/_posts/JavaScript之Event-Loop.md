---
title: JavaScript之Event Loop
date: 2021-12-14 20:49:49
summary: JavaScript中的事件循环机制及微任务、宏任务。
top: true
tags:
- JavaScript
- Event Loop
- Task
categories:
- [前端进阶]
---

### 一、事件循环
JS中的任务分为`同步任务`和`异步任务`。 
 
同步任务都在主线程（JS引擎线程）上依次执行，会形成一个`执行栈`。

异步任务由浏览器渲染进程中的相应线程（比如定时器线程和异步网络请求线程）管理，当异步任务有了运行结果，事件触发线程就会将异步任务的事件回调放到`任务队列`之中。

一旦主线程`执行栈`中的所有同步任务执行完毕，主线程就会读取`任务队列`中的任务添加到`执行栈`中 ，继续开始执行，执行完毕后，就会再去任务队列中读取，这种循环往复的执行机制就是`事件循环`。

![](https://s1.ax1x.com/2023/02/14/pSTDebT.png)

示例：
```js
let setTimeoutCallBack = function() {
  console.log('定时器回调');
};
let httpCallback = function() {
  console.log('http请求回调');
}

// 同步任务
console.log('同步任务1');

// 异步定时任务
setTimeout(setTimeoutCallBack, 1000);

// 异步http请求任务
ajax.get('/info', httpCallback);

// 同步任务
console.log('同步任务2');
```

代码执行过程：
1. 执行`console.log('同步任务1')`。
2. 执行`setTimeout(setTimeoutCallBack, 1000);`，会将该异步任务移交给定时器线程，定时器线程在1s 后将`setTimeoutCallBack` 这个回调交给事件触发线程处理，事件触发线程收到回调后把它加入到事件触发线程所管理的任务队列中等待执行。
3. 执行`ajax.get('/info', httpCallback);`，会将该异步任务移交给异步http请求线程，http请求线程在请求结束后将`httpCallback`这个回调交由事件触发线程处理，事件触发线程收到 回调后把它加入到事件触发线程所管理的事件队列中等待执行。
4. 执行`console.log('同步任务2')`。
5. 至此执行栈中的任务执行完毕，JS引擎线程已经空闲，开始向事件触发线程发起询问，询问事件触发线程的任务队列中是否有需要执行的回调函数，如果有将任务队列中的回调加入执行栈中，开始执行回调；如果任务队列中没有回调，JS引擎线程会一直发起询问，直到有为止。


### 二、宏任务 & 微任务
#### 1.宏任务(macrotask|task) 
本质: 宿主（Node或浏览器）发起的任务。    
主要包含：
- `整体代码script`
- `setTimeout`
- `setInterval`
- `I/O`
- `UI交互事件`
- `postMessage`
- `MessageChannel`（Web Worker）
- `setImmediate`（setTimeout(fn, 0)）

#### 2.微任务(microtask)
本质：Javascript 引擎发起的任务。  
主要包含: 
- `process.nextTick(Node.js)`
- `Promise.then`
- `catch`、`finally`
- `MutationObserver`(DOM变化观察器)

任务队列中分为`宏任务队列`和`微任务队列`，每执行一次任务都可能注册新的宏任务或微任务到相应的任务队列中，遵循`每执行一个宏任务，就会清空一次任务队列中的所有微任务`这一循环规则。  
大致过程如下：
- 一开始整个脚本作为一个宏任务执行
- 执行过程中同步代码直接执行，宏任务进入宏任务队列，微任务进入微任务队列
- 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
- 微任务执行完成，GUI线程开始渲染工作
- 渲染完毕后，JS线程继续接管，开始下一个宏任务，依次循环

### 三、示例代码
#### 示例1
```js
setTimeout(function(){
    console.log('setTimeout')
});

new Promise(function(resolve){
    console.log('pro');
    for(var i = 0; i < 10000; i++){
        i == 99 && resolve();
    }
}).then(function(){
    console.log('then')
});

console.log('end');

// 打印结果
pro
end
then 
setTimeout
```
代码执行过程：
1. 整段代码作为宏任务执行，遇到`setTimeout()`分配到宏任务队列中；
2. 遇到`new Promise`，其中的代码为同步代码，立即执行。打印`pro`；
3. 将`then`的回调分配到微任务队列中；
4. 遇到同步代码`console.log`，打印`end`；
5. 至此主代码宏任务执行结束；
6. 将微任务队列中的代码提取到主线程开始执行，打印`then`；
7. 微任务队列中执行完毕后，读取宏任务队列中的下一个宏任务开始执行，打印`setTimeout`。

#### 示例2
微任务和宏任务嵌套创建：
```js
console.log('1');

// 记作 set1
setTimeout(function () {
    console.log('2');
    // set4
    setTimeout(function() {
        console.log('3');
    });
    // pro2
    new Promise(function (resolve) {
        console.log('4');
        resolve();
    }).then(function () {
        console.log('5')
    })
})

// 记作 pro1
new Promise(function (resolve) {
    console.log('6');
    resolve();
}).then(function () {
    console.log('7');
    // set3
    setTimeout(function() {
        console.log('8');
    });
})

// 记作 set2
setTimeout(function () {
    console.log('9');
    // 记作 pro3
    new Promise(function (resolve) {
        console.log('10');
        resolve();
    }).then(function () {
        console.log('11');
    })
})

// 代码打印结果
1
6
7
2
4
5
9
10
11
8
3
```
代码执行过程：
1. 整体代码作为第一个宏任务进入执行栈中开始执行。打印`1`；
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
    <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>整体js代码</td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

2. 遇到`set1`, 将`set1回调`分发到宏任务队列中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>整体js代码</td>
        <td>set1回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

3. 遇到`pro1`，打印`6`，将`pro1的then回调`分发至微任务队列。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>整体js代码</td>
        <td>set1回调</td>
        <td>pro1的then回调</td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

4. 遇到`set1`, 将`set2回调`分发到宏任务队列中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>整体js代码</td>
        <td>set1回调</td>
        <td>pro1的then回调</td>
    </tr>
    <tr>
        <td></td>
        <td>set2回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

5. `整体js代码`宏任务至此执行完毕，js引擎读取`微任务队列`到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>pro1的then回调</td>
        <td>set1回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set2回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>
6. 将`pro1的then回调`读取到执行栈中，打印`7`，将`set3的回调`分发至宏任务队列。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>pro1的then回调</td>
        <td>set1回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set2回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set3回调</td>
        <td></td>
    </tr>
</table>

7. `pro1的then回调`执行完毕，且微任务队列中已经无任务，读取宏任务队列，将`set1回调`宏任务添加到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set1回调</td>
        <td>set2回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set3回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

8. 打印`2`，将`set4回调`分发至宏任务队列；遇到`pro2`，打印`4`，将`pro2的then回调`分发至微任务队列。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set1回调</td>
        <td>set2回调</td>
        <td>pro2的then回调</td>
    </tr>
    <tr>
        <td></td>
        <td>set3回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set4回调</td>
        <td></td>
    </tr>
</table>

9. `set1回调`宏任务执行完毕，读取微任务队列到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>pro2的then回调</td>
        <td>set2回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set3回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set4回调</td>
        <td></td>
    </tr>
</table>

10. 执行`pro2的then回调`，打印`5`。执行完毕后，读取下一个宏任务`set2回调`到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set2回调</td>
        <td>set3回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set4回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

11. 打印`9`，执行`pro3`，打印`10`，将`pro3的then回调`分发到微任务队列中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set2回调</td>
        <td>set3回调</td>
        <td>pro3的then回调</td>
    </tr>
    <tr>
        <td></td>
        <td>set4回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

12. `set2回调`执行完毕，将`微任务队列`中的`pro3的then回调`添加到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>pro3的then回调</td>
        <td>set3回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td>set4回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

13. 打印`11`，`pro3的then回调`执行完毕，将宏任务队列中的`set3回调`添加到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set3回调</td>
        <td>set4回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

14. 打印`8`，`set3回调`执行完毕，将宏任务队列中的`set4回调`添加到执行栈中。
<table>
    <tr>
        <th style="background:#ccc">主线程</th>
        <th style="background:#ccc" colspan="2">Event Queue</th>
    </tr>
    <tr>
        <td>执行栈</td>
        <td>宏任务队列</td>
        <td>微任务队列</td>
    </tr>
    <tr>
        <td>set4回调</td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>

15. 打印`3`。

#### 示例3
```js
async function async1() {
  console.log(1);
  const result = await async2();
  console.log(3);
}

async function async2() {
  console.log(2);
}

// 记作pro
Promise.resolve().then(() => {
  console.log(4);
});

// 记作set
setTimeout(() => {
  console.log(5);
});

async1();
console.log(6);

// 打印结果
1
2
6
4
3
5
```
代码执行流程：
1. 执行`pro`，将`pro的then回调`分发到微任务队列中；
2. 执行`set`，将`set回调`添加到宏任务队列；
2. 执行`async1()`，打印`1`；
3. 执行`const result = await async2()`，打印`2`；同时将`console.log(3)`分发到微任务队列;
4. 继续执行主线程中的代码，打印`6`;
5. 执行微任务队列中的回调，依次打印`4`、`3`;
6. 执行宏任务队列中的回调，打印`5`。

#### 示例4
```js
async function t1 () {
    console.log(1)
    //  记作pro2
    new Promise(function(resolve) {
        console.log('promise3')
        resolve();
    }).then( function () {
        console.log('promise4')
    })
    
    // 记作pro3
    await new Promise(function(resolve) {
        console.log('b')
        resolve();
    }).then( function() {
        console.log('t1p')
    })

    // 记作await pro3
    console.log(2)
    // 记作pro5
    new Promise(function(resolve) {
        console.log('promise5')
        resolve();
    }).then(function() {
        console.log('promise6')
    })
}

// 记作set
setTimeout(function() {
    console.log('setTimeout')
}, 0)

async function t2() {
    console.log(3);
    // 记作pro4
    await Promise.resolve().then(() => console.log('t2p'));
    
    // 记作await pro4
    console.log(4);
}

t1();

// 记作pro1
new Promise(function(resolve) {
    console.log('promise1')
    resolve();
}).then(function() {
    console.log('promise2')
})

t2();

console.log('end');

// 打印结果
1 
promise3 
b 
promise1 
3
end
promise4
t1p
promise2
t2p
2
primise5
4
promise6
setTimeout
```
代码执行流程：
1. `set回调`进到宏任务队列；
2. 执行`t1()`，打印`1`，打印`promise3`，将`pro2的then回调`放到微任务队列；
3. 打印`b`，将`pro3的then回调`放到微任务队列，因为`await`，之后的代码在`pro3的then回调`执行后再执行；
4. 打印`promise1`，将`pro1的then回调`放到微任务队列；
5. 执行`t2()`，打印`3`，将`pro4的then回调`放到微任务队列，`pro4`之后的代码暂不执行；
6. 打印`end`。此时的微任务队列为：
<table>
    <tr>
        <th style="background: #ccc;">微任务队列</th>
    </tr>
    <tr><td>pro2的then回调</td></tr>
    <tr><td>pro3的then回调</td></tr>
    <tr><td>pro1的then回调</td></tr>
    <tr><td>pro4的then回调</td></tr>
</table>

7. 接着开始依次执行微任务队列中的回调。执行`pro2的then回调`，打印`promise4`；
8. 执行`pro3的then回调`，打印`t1p`，将`await pro3`代码块放到微任务队列；  
9. 执行`pro1的then回调`，打印`promise2`；  
10. 执行`pro4的then回调`，打印`t2p`，将`await pro4`代码放到微任务队列；  
11. 执行`await pro3`代码块，打印`2`，打印`promise5`，将`pro5的then回调`放到微任务队列；
12. 执行`await pro4`，打印`4`；
13. 执行`pro5的then回调`，打印`promise6`；
14. 微任务队列代码执行完毕，读取宏任务队列，执行`set回调`，打印`setTimeout`。

> **知识点总结**  
> async/await本质上还是基于Promise的一些封装, 可以理解为，await 以前的代码，相当于与 new Promise 的同步代码，await 以后的代码相当于 Promise.then的回调。   
> await之后的代码必须等await语句执行完成后（包括微任务完成），才能执行后面的，也就是说，只有运行完await语句，才把await语句后面的全部代码加入到微任务队列。

### 四、参考文章
- [isboyjc.「硬核JS」一次搞懂JS运行机制.掘金. ](https://juejin.cn/post/6844904050543034376)
- [娟儿juan.浏览器渲染流程.掘金.](https://juejin.cn/post/6844904017907154951)
- [ipromise.深入前端-彻底搞懂JS的运行机制.segmentfault.](https://segmentfault.com/a/1190000019761557)
- [EsunR.总结事件轮询机制，以及宏任务队列与微任务队列.CSDN.](https://blog.csdn.net/u012925833/article/details/89306184)
- [娜姐.宏任务和微任务到底是什么.腾讯云社区.](https://cloud.tencent.com/developer/article/1701427)  
- [MessageChannel](https://developer.mozilla.org/zh-CN/docs/Web/API/MessageChannel)、[postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)、[setImmediate](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setImmediate)、[mutationObserver](https://developer.mozilla.org/zh-cn/docs/web/api/mutationobserver)