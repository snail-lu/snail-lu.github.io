---
title: 前端常用js工具函数
date: 2022-01-12 13:54:40
summary: 前端常用js工具函数汇总
tags: 
- JavaScript
categories:
- [项目开发]
---

### 日期格式化（简易版）
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

### 防抖函数（简易版）
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

### 节流函数（简易版）
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