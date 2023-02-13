---
title: JavaScript常用功能实现
date: 2022-01-12 13:54:40
summary: JavaScript常用功能实现
tags: 
- JavaScript
categories:
- [项目开发]
---

### 数组操作
#### 查重
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
#### 去重
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

#### 扁平化
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

### 防抖

事件持续触发不执行，停止触发指定时间之后才执行一次。
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

### 节流
事件重复触发，不会每次都执行，每间隔一段时间执行一次。
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
[debounce和throttle可视化比较](http://demo.nimius.net/debounce_throttle/)

### 日期格式化
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

### 深拷贝
**实现方式一：**
原理：利用`JSON.stringify`序列化和`JSON.parse`反序列化
缺点：对象中`undefined`、`function`、`symbol`这三种类型的值会被过滤掉
```js
const obj = {
  a: '123',
  b: 234,
  c: true,
  d: null,
  e: function() {console.log('test')},
  h: new Set([4,3,null]),
  i:Symbol('fsd'),
  k: new Map([ ["name", "test"],  ["title", "Author"]  ])
}
console.log(JSON.stringify(obj)); // {"a":"123","b":234,"c":true,"d":null,"h":{},"k":{}}

const newObj = JSON.parse(JSON.stringify(obj));
```