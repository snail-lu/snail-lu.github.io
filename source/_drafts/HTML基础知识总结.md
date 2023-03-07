---
title: HTML基础知识总结
date: 2020-10-21 22:17:16
summary: HTML基础知识总结
tags:
- HTML
- 面试
categories:
- [前端进阶]
---

### 1. HTML、XML、XHTML 的区别
- `HTML`：超文本标记语言，是语法较为松散的、不严格的`Web`语言，主要用于显示数据；
- `XML`：可扩展标记语言，主要用于传输和存储数据，可扩展，可以自定义标签；
- `XHTML`：可扩展超文本标记语言，基于`XML`，作用与`HTML`类似，但语法更严格。

XHTML与HTML的区别
​- `XHTML`标签名必须小写；
- `XHTML`元素必须被关闭；
- `XHTML`元素必须被正确的嵌套；
- `XHTML`元素必须要有根元素。
​
XHTML与HTML5的区别
​- `HTML5`新增了`canvas`绘画元素；
- `HTML5`新增了用于绘媒介回放的`video`和`audio`元素；
- 更具语义化的标签，便于浏览器识别；
- 对本地离线存储有更好的支持；
- `MathML`，`SVG`等，可以更好的`render`；
- 添加了新的表单控件：`calendar`、`date`、`time`、`email`等。
​
HTML、XHTML、HTML5之间的联系
​- `XHTML`是`HTML`规范版本；
- `HTML5`是`HTML`、`XHTML`以及`HTML DOM`的新标准。

### 元素分类
- 行内元素： `a`, `b`, `span`, `img`, `input`, `select`, `strong`;
- 块级元素： `div`, `ul`, `li`, `dl`, `dt`, `dd`, `h1-5`, `p`等；
- 空元素： `<br>`, `<hr>`, `<img>`, `<link>`, `<meta>`;