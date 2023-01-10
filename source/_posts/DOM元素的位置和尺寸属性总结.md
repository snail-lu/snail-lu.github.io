---
title: DOM元素的位置和尺寸属性总结
date: 2022-05-01 20:05:03
summary: DOM元素的几何属性
tags:
- JavaScript
- DOM
categories:
- [前端进阶]
---

### DOM元素的位置
| 属性 | 含义 |操作|
| :---- | :---- | :---- |
| offsetLeft | 当前元素左上角相对于其 `offsetParent` 节点的左边界偏移的像素值。| 只读 |
| offsetTop | 当前元素相对于其 `offsetParent` 元素的顶部内边距的距离。| 只读 |
| scrollLeft | 读取或设置元素滚动条到元素左边的距离。| 读写 |
| scrollTop | 获取或设置一个元素的内容垂直滚动的像素数。| 读写 |
| getBoundingClientRect() | 返回一个 DOMRect 对象，其提供了元素的大小及其相对于视口的位置。|[示意图](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)|

### DOM元素的尺寸
| 属性 | 含义 |操作|
| :---- | :---- | :---- |
| offsetWidth | 元素的`border-box`宽度。包括边框 (border)、内边距 (padding)、竖直方向滚动条 (scrollbar)（如果存在）。| 只读，整数 |
| offsetHeight | 元素的`border-box`高度度。包括边框 (border)、内边距 (padding)、水平方向滚动条 (scrollbar)（如果存在）。| 只读，整数 |
| scrollWidth | 元素的内容宽度，包括由于 overflow 溢出而在屏幕上不可见的内容。包含元素的内边距，但不包括边框，外边距或垂直滚动条（如果存在） | 只读，整数 |
| scrollHeight | 元素的内容高度，包括由于 overflow 溢出而在屏幕上不可见的内容。包含元素的内边距，但不包括边框，外边距或水平滚动条（如果存在） | 只读，整数 |
| clientWidth |元素内部的宽度（以像素为单位）。包括内边距（padding），但不包括边框（border）、外边距（margin）和垂直滚动条（如果存在）。| 只读，整数 |
| clientHeight |元素内部的高度（以像素为单位），包含内边距，但不包括边框、外边距和水平滚动条（如果存在）。| 只读，整数 |
| clientLeft | 左边框的宽度 | 只读，整数 |
| clientTop | 上边框的宽度 | 只读，整数 |
| getBoundingClientRect() | 返回一个 DOMRect 对象，其提供了元素的大小及其相对于视口的位置。|[示意图](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)|