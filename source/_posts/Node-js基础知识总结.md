---
title: Node.js基础知识总结
date: 2022-08-27 09:51:43
summary: Node.js基础知识总结
tags:
- Node.js
- JavaScript
categories:
- [读书笔记]
- [前端进阶]
---

### 1. Node.js常用核心模块
#### 1.1 全局对象`global`
全局变量都是全局对象`global`的属性
- `process`全局变量
它是用于描述和控制当前Node.js进程状态的对象。
```js
// 示例用法：
// process属性
console.log('pid', process.pid) // 打印进程号
console.log('version', process.version) // 打印node.js版本
console.log('env', process.env) // 打印环境变量

// process方法
process.stdout.write('hello world') // 输出信息
process.exit() // 退出进程
```
更多API参考[Node.js官方文档之process](https://nodejs.org/dist/latest-v18.x/docs/api/process.html)

#### 1.2 常用工具`util`
- `util.inherits()` 对象间原型继承
- `util.inspect()` 将对象格式化为字符串
- `util.isArray()` 判断是否为数组
- `util.isRegExp()` 判断是否为正则表达式
- `util.isDate()` 判断是否为日期类型

#### 1.3 事件模块`events`
`events`模块对外只提供了一个`EventEmitter`对象。`EventEmitter`对象是对事件触发与事件监听功能的封装。当EventEmitter对象触发一个事件时，所有绑定在该事件上的函数都被同步调用。
```js
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
```
EventEmitter对象的实例方法：

| 方法 | 描述 |
| -- | -- |
|on(event, listener)|为事件event注册一个监听器|
|once(event, listener)|为指定事件注册一个单次监听器，|
|emit(event, [arg1], [arg2], [...])|触发event事件|
|addListener(event, listener)|为指定事件添加一个监听器到监听器数组的尾部。|
|removeListener(event, listener)|移除指定事件的某个监听器，监听器必须是该事件已经注册过的监听器。|
|removeAllListeners([event])|移除所有事件的所有监听器， 如果指定事件，则移除指定事件的所有监听器。|
|listeners(event)|返回指定事件的监听器数组。|

EventEmitter定义了一个特殊的事件`error`，当`error`被触发时，如果没有相应的监听器，Node.js会把它当做异常，退出程序并输出错误信息。

#### 1.4 文件系统`fs`模块
```js
const fs = require('fs');
const path = require('path');

// 异步读取文件
// 文件路径可以为相对路径或绝对路径
fs.readFile(path.join(__dirname, "./tes.txt"), function(err,data){
    if(err) {
        return console.error('error: ' + err)
    }
    console.log('异步读取：'+data.toString())
})

/**
 * 异步写入文件
 */
fs.writeFile('./test.txt', content, function (err) {
    if (err) {
        return console.log(err, 'err')
    }
    console.log('文件写入完成')
})

/**
 * 复制文件
 */
const readerStream = fs.createReadStream('test.txt');
const writeStream = fs.createWriteStream('copyTest.txt');
readerStream.pipe(writeStream);
console.log('文件复制成功');
```
更多API参考[Node.js官方文档之文件系统](https://nodejs.org/dist/latest-v18.x/docs/api/fs.html)

#### 1.5 `http`模块

#### 1.6 `path`模块
用于处理文件的路径。
- `join()` 连接路径
- `resolve()` 将相对路径转换为绝对路径
- `parse()` 解析路径，将URL字符串转换成对象


### 参考资料
1. BYVoid. Node.js开发指南[M]. 人民邮电出版社, 2012.
2. 天津滨海迅腾科技集团有限公司. Node.js项目实战[M]. 1. 天津:南开大学出版社, 2018.
3. [Node.js教程-RUNOOB.COM](https://www.runoob.com/nodejs/nodejs-tutorial.html)
4. [Node.js官方文档](https://nodejs.org/dist/latest-v18.x/docs/api/)