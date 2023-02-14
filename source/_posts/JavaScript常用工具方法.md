---
title: JavaScript常用工具方法
date: 2022-01-12 13:54:40
summary: 开发中经常用到的JS工具方法
tags: 
- JavaScript
categories:
- [项目开发]
---

### 1. 数组查重
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
### 2. 数组去重
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

### 3. 数组扁平化
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

### 4. 对象数组排序
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
### 5. 日期格式化
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

### 6. 回到顶部
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

### 7. 获取url参数
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

### 8. 全屏
```js
// 判断是否为全屏
export function isFullScreen() {
  return  !!(
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