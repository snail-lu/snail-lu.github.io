---
title: JavaScript常用手写实现总结
date: 2022-01-12 13:54:40
summary: 开发中经常用到的JS工具方法及算法实现
tags: 
- JavaScript
categories:
- [项目开发]
---

### 1. 算法
#### 冒泡排序
**实现原理：**
长度为 n 的数组，每次比较相邻两个数的大小并根据大小来交换位置，这样第一轮就可以选出一个最大或最小的数放到最后。总共经过 n-1 轮可以完成排序。

**代码实现：**
```js
function bubbleSort (arr) {
    if(arr.length <= 1) {
        return arr
    }
    // 总共进行arr.length - 1轮排序
    for (let i = 0, max = arr.length - 1; i < max; i++) {
        // 排序完成标志位，
        // 当没有相邻数据交换时证明排序完成，反之，则证明排序尚未完全结束
        let done = true;

        // 每轮的两两比较只需要进行到 max-i 即可，因为max-i后面的数据都是之前排好的
        for (let j = 0; j < max - i; j++) {
            // 这里是升序排序
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j+1]] = [arr[j+1], arr[j]]
                done = false;
            }
        }

        // 排序完成，提前终止，减少不必要的循环
        if (done) {
            break;
        }
    }
    return arr;
}
```

#### 快速排序
**实现原理：**
从数组中选择一个元素作为基准点，遍历数组，将数组所有比基准值小的元素摆放在左边的数组中，而大于基准值的摆放在右边的数组中。每次分割结束以后基准值会插入到中间去。
最后利用递归，将左边的数组和右边的数组再进行一次上述操作。

**代码实现：**
```js
function quickSort(arr) {
    // 递归终止条件
    if(arr.length <= 1) {
        return arr
    }

    // 获取数组中间的元素下标,将该元素作为基准值
    const pivotIndex = Math.floor(arr.length / 2);
    // 将该基准值从原数组中剪切出来
    const pivot = arr.splice(pivotIndex, 1)[0];

    // 定义两个空数组，分别用于存放小于基准值的元素和大于基准值的元素
    const left = [];
    const right = [];

    // 遍历元素进行数据分组
    for (let i = 0; i < arr.length; i++){
        if (arr[i] < pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }

    // 递归，对左右两个数组排序，并拼接，得到最终排序好的结果
    return quickSort(left).concat([pivot], quickSort(right));
}
```

#### 归并排序
**实现原理：**
递归将数组两两分开直到包含一个元素，然后将数组排序合并，最终合并为排序好的数组。

**代码实现：**
```js
// 采用自上而下的递归方法
function mergeSort(arr) {
    const len = arr.length;
    if(len <= 1 ) {
        return arr;
    }

    // 获取中间元素下标，并将数组拆分为left部分和right部分
    const middle = Math.floor(len / 2),
    left = arr.slice(0, middle),
    right = arr.slice(middle);

    // 递归实现排序
    return merge(mergeSort(left), mergeSort(right));
}

// 将各自有序的两个数组进行排序合并
function merge(left, right)
{
    const result = [];

    // 比较左右数组的头部元素，将值小的元素添加到result尾部
    while (left.length && right.length) {
        if (left[0] <= right[0]) {
        result.push(left.shift());
        } else {
        result.push(right.shift());
        }
    }

    // 只有左数组时，将元素依次添加至result尾部
    while (left.length) {
        result.push(left.shift());
    }

    // 只有右数组时，将元素依次添加至result尾部
    while (right.length) {
        result.push(right.shift());
    }

    // 返回排序后的数组
    return result;
}
```

#### 阶乘
```js
// 常规递归实现，复杂度O(n) 
function factorial(n) { 
    if(n === 1) return 1; 
    return n * factorial(n-1); 
} 

// 尾递归优化，复杂度O(1) 
function factorial(n, result = 1) { 
    if(n === 1) return result; 
    return factorial(n-1, n * result); 
}

// 递推实现
function factorial(n) {
    let res = 1;
    for (let i = 1; i <= n; i++) {
        res = res * i;
    }
    return res;
}

```

#### 斐波那契数列
初始前两项为1，后续各项等于前两项之和：
```js
// 常规递归实现
function fibonacci(n) {
    if (n <= 2) {
        return 1
    }

    return fibonacci(n-1) + fibonacci(n-2)
}

// 尾递归优化
function fibonacci(n, prev=1, cur=1) {
    if (n <= 2) {
        return cur
    }
    return fibonacci(n-1, cur, cur+prev )
}

// 递推实现
function fibonacci(n) {
    let cur = 1;
    let next = 1;
    for (let i = 1; i < n; i++) {
        [cur, next] = [next, cur + next]
    }
    return cur;
}
```

#### 洗牌算法（乱序）
**实现原理：**
从最后一个元素开始，从数组中随机选出一个位置，交换，直到第一个元素。
```js
function disorder(arr) {
    for(let len=arr.length, i = len-1; i>-1; i--) {
        let random = Math.floor(len * Math.random());
        [arr[i], arr[random]] = [arr[random], arr[i]];
    }
    return arr;
}

```

### 2. JS底层方法手写
#### new
使用 `new` 来调用函数，会自动执行下面的操作：
1. 创建一个空对象`{}`
2. 将这个新对象的原型对象指向构造函数的原型对象，以继承原型上的方法
3. 将构造函数中的this指向到新创建的对象并执行构造函数，以获取实例属性
4. 如果构造函数执行后返回了对象，就将该对象作为结果返回；否则就将上面创建的新对象作为结果返回

```js
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

#### call()
`call()` 方法使用一个指定的 `this` 值和单独给出的一个或多个参数来调用一个函数。
```js
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
#### apply()
`apply()` 方法调用一个具有给定 `this` 值的函数，以及以一个数组（或一个类数组对象）的形式提供的参数。
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

#### bind()
`bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
```js
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

#### instanceof
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

#### async/await
`async` 函数的实现原理，就是将 `Generator` 函数和自动执行器，包装在一个函数里。
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

#### 用setTimeout来实现setInterval
**setInterval机制:**
- 延时一段时间后，将任务push到任务队列中排队执行；
- 在每次把任务 push 到任务队列前，都要进行一下判断(看上次的任务是否仍在队列中，如果有则不添加，没有则添加)。

基于该原理，会导致两个问题：
- 前一任务结束到当前任务开始的时间间隔与设置的`delay`值不符。
- 可能出现某些任务被跳过的情况

**setTimeout机制：**
`setTimeout` 不管上次异步任务是否完成，它都会将当前异步任务推入队列（很容易理解，setTimeout本身就是一次调用一次执行），`setTimeout` 保证调用的时间间隔是一致的，`setInterval` 的设定的间隔时间包括了执行回调的时间。

**简易实现：**
```js
function mySetInterval(cb, delay) {
    mySetInterval.timer = setTimeout(() => {
        cb()
        mySetInterval(cb, delay)
    }, delay)
}

mySetInterval.clear = function() {
    clearTimeout(mySetInterval.timer)
}

// 测试
mySetInterval(() => {
    console.log('我进入了循环')
}, 1000)

setTimeout(() => {
    mySetInterval.clear()
}, 5000)
```
### 3. 工具方法
#### 防抖
指定时间内函数多次调用都会被重置，只会在最后一次触发结束后延时执行
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

#### 深拷贝
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
缺点：无法对对象里面嵌套的对象进行深拷贝，相当于只是对一层引用对象进行深拷贝
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
    // 判断拷贝的obj是对象还是数组
    const objClone = Array.isArray(obj) ? [] : {};
    if (obj && typeof obj === "object") { // obj不能为空，并且是对象或者是数组，因为null也是object
        for (key in obj) {
            if (obj.hasOwnProperty(key)) {
                if (obj[key] && typeof obj[key] === "object") { // obj里面属性值不为空并且还是对象，进行深度拷贝
                    objClone[key] = deepClone(obj[key]); // 递归进行深度的拷贝
                } else {
                    objClone[key] = obj[key]; // 直接拷贝
                }
            }
        }
    }
    return objClone;
}
```

#### 数组查重
```js
function checkDuplicates(arr) {
    const o = {};

    arr.forEach(item => {
        if(!o[item]) {
            o[item] = 1;
        } else {
            o[item]++;
        }
    })
    
    return o;
}
```
#### 数组去重
```js
// 利用Object中key的唯一性来筛选掉重复元素
function deduplicate(arr) {
    const o = {};
    const newArr = [];

    arr.forEach(item => {
        if(!o[item]) {
            o[item] = true;
            newArr.push(item);
        }
    })
    
    return newArr;
}

// 利用Set去重
function deduplicate(arr) {
    return [...new Set(arr)];
}
```

#### 数组扁平化
多维数组转换成一维数组
```js
// 常规实现
function flat(arr) {
    let result = [];
    arr.forEach(item => {
        if(Array.isArray(item)) {
            result = result.concat(flat(item))
        } else {
            result.push(item)
        } 
    })
    return result;
}

// reduce实现
function flat(arr) {
    return arr.reduce((result, current)=>{
        if(Array.isArray(current)) {
            return result.concat(flat(current))
        } else {
            return result.concat(current)
        }
    }, [])
}
```

#### 对象数组排序
```js
/**
 * 排序函数
 * @param {string} key - 要排序的键名
 * @param {string} order - asc:升序 desc:降序
 * @return {function}
 */
function objectArraySort(key, order='asc') {
	return function (object1, object2) {
		const a = object1[key];
		const b = object2[key];
		if(order==='asc') {
			return a>b ? 1 : -1;
		} else {
			return a<b ? 1 : -1;
		}

	}
}

// 使用示例：
const obj = [
	{ name: '张三', age: 12 },
	{ name: '李四', age: 10 },
	{ name: '王五', age: 18 },
	{ name: '赵六', age: 15 },
]
obj.sort(objectArraySort('age'))
```
#### 日期格式化
```js
/**
 *
 * @fmt : 输出格式
 * @date : 日期
 *
 */
function dateFormat(fmt, date) {
	let ret;
	const opt = {
		'Y+': date.getFullYear().toString(), // 年
		'm+': (date.getMonth() + 1).toString(), // 月
		'd+': date.getDate().toString(), // 日
		'H+': date.getHours().toString(), // 时
		'M+': date.getMinutes().toString(), // 分
		'S+': date.getSeconds().toString() // 秒
		// 有其他格式化字符需求可以继续添加，必须转化成字符串
	};
	for (let k in opt) {
		ret = new RegExp('(' + k + ')').exec(fmt);
		if (ret) {
			fmt = fmt.replace(ret[1], ret[1].length == 1 ? opt[k] : opt[k].padStart(ret[1].length, '0'));
		}
	}
	return fmt;
}
```
使用示例：
```js
const today = dateFormat('YYYY-mm-dd HH:MM:SS', new Date());
console.log(today); // "2021-01-12 14:25:43"
```

[日期格式化（全能版）源码地址](https://stevenlevithan.com/assets/misc/date.format.js) | 使用示例：
```js
console.log(dateFormat(new Date(), 'dS mmm. yyyy HH:MM:ss')); // "12th Jan. 2021 14:28:09"
console.log(dateFormat(new Date(), 'shortDate')); // "1/12/21"
console.log(dateFormat(new Date(), 'yyyy-mm-dd HH:MM:ss')); // "2022-01-12 14:32:13"
```

#### 回到顶部
```js
function backToTop() {
	let top = document.documentElement.scrollTop || document.body.scrollTop;
	const timeTop = setInterval(() => {
		document.body.scrollTop = document.documentElement.scrollTop = top -= 400;
		if (top <= 0) {
			clearInterval(timeTop);
		}
	}, 10);
}
```

#### 获取url参数
```js
//解析参数
analysisUrl() {
	let url = window.location.href
	var obj = {}
	var str = url.split('?')[1]
	var arr = str.split('&')
	arr.forEach((element) => {
		var item = element.split('=')
		var a = item[0]
		var b = item[1]
		obj[a] = b
	})
	return obj
}
```

#### 全屏
```js
// 判断是否为全屏
export function isFullScreen() {
    return !!(
        document.fullscreen || 
        document.mozFullScreen ||                         
        document.webkitIsFullScreen ||       
        document.webkitFullScreen || 
        document.msFullScreen 
    );
}
// 全屏/退出全屏操作
function handleFullScreen() {
    let element = document.documentElement;
    // 退出全屏
    if (isFullScreen()) {
        if (document.exitFullscreen) {
            document.exitFullscreen();
        } else if (document.webkitCancelFullScreen) {
            document.webkitCancelFullScreen();
        } else if (document.mozCancelFullScreen) {
            document.mozCancelFullScreen();
        } else if (document.msExitFullscreen) {
            document.msExitFullscreen();
        }
    } else {
        if (element.requestFullscreen) {
            element.requestFullscreen();
        } else if (element.webkitRequestFullScreen) {
            element.webkitRequestFullScreen();
        } else if (element.mozRequestFullScreen) {
            element.mozRequestFullScreen();
        } else if (element.msRequestFullscreen) {
            element.msRequestFullscreen();
        }
    }
}
```