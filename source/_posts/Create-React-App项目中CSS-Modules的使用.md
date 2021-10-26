---
title: Create-React-App项目中CSS Modules的使用
date: 2021-10-26 13:41:53
summary: Create React App项目下如何启用及使用CSS Modules。
tags:
- React
- Create React App
- CSS Modules
categories:
- [随笔]
- [前端]
- [开发配置]
---

### 概述
- 版本：`react: ^16.13.1`、`node-sass: ^4.14.1`

### 一、启用SCSS
1. 安装`node-sass`

``` bash
npm install node-sass --save
```
2. 更改`css`文件后缀并引入  
将`.css`文件后缀改为`.scss`，在`.js`或`.jsx`文件中引入。

``` js
import './App.scss';
```
### 二、启用CSS Modules
使用`Create React App`脚手架创建的项目默认支持`CSS Modules`，需要将模块化的css文件后缀改为`.module.css`或`.module.scss`，然后在`.js`或`.jsx`文件中引入。  
示例代码：
``` jsx
import React, { Component } from 'react';
import styles from './Button.module.css'; // 引入模块化后的css文件
import './another-stylesheet.css'; // 引入普通样式文件

class Button extends Component {
  render() {
    return <button className={styles.error}>Error Button</button>;
  }
}
```
### 三、使用多个类名
在不使用`CSS Modules`时，我们给元素设置多个类名时通常使用`join()`或`ES6模板字符串`，这里给出使用`CSS Modules`时这两种形式的写法：  

``` jsx
// 数组join()形式
<div className={[styles.menu_bar, styles.active].join(" ")}>Menu</div>

// ES6模板字符串形式
<div className={`${styles.menu_bar} ${styles.active}`}>Menu</div>
```

### 四、动态使用类名
有时需要根据某一个变量的值来动态决定类名，这里也同样给出`join()`及`ES6模板字符串`两种形式的写法：

``` jsx
// 数组join()形式
<div className={[styles.menu_bar, show?styles.active:''].join(" ")}>Menu</div>

// ES6模板字符串形式
<div className={`${styles.menu_bar} ${show?styles.active:''}`}>Menu</div>
```
### 五、使用公共样式
有时可能同时需要使用公共样式中的类名，这里也给出`join()`及`ES6模板字符串`两种形式的写法：

``` jsx
// 数组join()形式
<div className={['red_txt', styles.menu_bar, show?styles.active:''].join(" ")}>Menu<div>

// ES6模板字符串形式
<div className={`red_txt ${styles.menu_bar} ${show?styles.active:''}`}>Menu</div>
```


### 参考文档

- [Create React App | Adding CSS Modules](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet)