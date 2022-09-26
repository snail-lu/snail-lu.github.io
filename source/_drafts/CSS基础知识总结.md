---
title: CSS基础知识总结
tags:
---

### 1. 盒模型
盒模型中均包含元素内容`content`、内边距`padding`、边框`border`及外边距`margin`四部分
- **标准盒模型**: 
  - `width = content`
  - 通过 `box-sizing: content-box;` 来使用标准盒模型
- **IE盒模型**：
  - `width = content + padding + border`
  - 通过`box-sizing: border-box;`来使用IE盒模型
### 2. flex和grid的区别
- `flex`布局是一维布局，适合做局部布局，比如导航栏
- `grid`布局是二维布局，适合做整个页面的规划

### 3. BFC
#### 概念
**BFC(Block Formatting context)**：块级格式化上下文。具有 `BFC` 特性的元素可以看作是隔离了的独立容器，拥有不影响外界且不被外界所影响的布局规则。

#### 触发BFC
- 根元素（`html`）
- 浮动元素（`float` 值不为 `none`）
- 绝对定位元素（`position` 值为 `absolute` 或 `fixed`）
- 行内块元素（`display` 值为 `inline-block`）
- 表格单元格（`display` 值为 `table-cell`，HTML 表格单元格默认值）
- 表格标题（`display` 值为 `table-caption`，HTML 表格标题默认值）
- 匿名表格单元格元素（`display` 值为 `table`、`table-row`、 `table-row-group`、`table-header-group`、`table-footer-group`（分别是 HTML table、tr、tbody、thead、tfoot 的默认值）或 `inline-table`）
- `overflow` 值不为 `visible`、`clip` 的块元素
- `display` 值为 `flow-root` 的元素
- `contain` 值为 `layout`、`content` 或 `paint` 的元素
- 弹性元素（`display` 值为 `flex` 或 `inline-flex` 元素的直接子元素），如果它们本身既不是 `flex`、`grid` 也不是 `table` 容器
- 网格元素（`display` 值为 `grid` 或 `inline-grid` 元素的直接子元素），如果它们本身既不是 `flex`、`grid` 也不是 `table` 容器
- 多列容器（`column-count` 或 `column-width (en-US)` 值不为 `auto`，包括`column-count` 为 `1`）
- `column-span` 值为 `all` 的元素始终会创建一个新的 BFC，即使该元素没有包裹-在一个多列容器中 (规范变更, Chrome bug)
#### BFC布局规则
- **包含内部浮动**
    开启了BFC的元素在计算高度时，浮动的元素也包含在内，即父元素会被浮动的子元素撑开。
- **排除外部浮动**
    开启了BFC的元素会阻止自己的文字内容去环绕相邻的浮动元素
- **阻止外边距重叠**
    开启了BFC的元素会阻止自己与相邻元素的`margin-top`或`margin-bottom`发生重叠。
- **阻止外边距塌陷**
    开启了BFC的元素会阻止自己的`margin-top`产生伪作用于父元素的现象。

### 4. 选择器的优先级
1. `!important`会覆盖页面内任何位置的元素样式
2. 内联样式，如 `style="color: green"`，权值为 `1000`
3. ID选择器，如`#app`，权值为 `100`
4. 类、伪类、属性选择器，如`.foo, :first-child, div[class="foo"]`，权值为 `10`
5. 标签、伪元素选择器，如`div::first-line`，权值为 `1`
6. 通配符、子类选择器、兄弟选择器，如`*, >, +`，权值为 `0` 
7. 继承的样式没有权值

### 5. 垂直居中实现方式
- absolute + margin负值
- absolute + transform
- flex
- grid
- `table-cell` + `vertical-align: middle;`
- float + margin

### 6. 回流和重绘
#### 概念
- **回流(reflow)**：`Render Tree`中部分或全部元素的几何尺寸发生改变，浏览器重新计算元素的几何属性并调整布局的过程，也称**重排**。
- **重绘(repaint)**：`Render tree`中的一些元素只发生样式的改变并不影响它在文档流中的位置时，浏览器不需重新计算元素的几何属性、直接为元素绘制新的样式的过程。

#### 区别：
回流必将引起重绘，重绘不一定会引起回流。

#### 回流的触发场景
- 页面首次渲染
- 浏览器的窗口尺寸变化
- 元素的位置/尺寸/内容/字体大小发生变化
- 添加或删除可见的DOM元素
- 查询一些属性或方法
  - clientTop、clientLeft、clientWidth、clientHeight
  - offsetTop、offsetLeft、 offsetWidth、offsetHeight
  - scrollTop、scrollLeft、scrollWidth、scrollHeight
  - scrollIntoView()、scrollIntoViewIfNeeded()、getComputedStyle()、getBoundingClientRect()、scrollTo()

#### 重绘的触发场景
- 回流
- 一些CSS属性的改变
  - color、border-style、visibility、background、text-decoration、background-image、background-position、background-repeat、outline-color、outline、outline-style、border-radius、outline-width、box-shadow、background-size

### 7. CSS3新特性
- RGBA和透明度
- background-image background-origin(content-box/padding-box/border-box) background-size background-repeat
- word-wrap（对长的不可分割单词换行）word-wrap：break-word
- 文字阴影：text-shadow： 5px 5px 5px #FF0000;（水平阴影，垂直阴影，模糊距离，阴影颜色）
- font-face属性：定义自己的字体
- 圆角（边框半径）：border-radius 属性用于创建圆角
- 边框图片：border-image: url(border.png) 30 30 round
- 盒阴影：box-shadow: 10px 10px 5px #888888
- 媒体查询：定义两套css，当浏览器的尺寸变化时会采用不同的属性

### 8. `display: none`和`visibility: hidden`的区别
- `display：none` 不显示对应的元素，在文档布局中不再分配空间（回流+重绘）
- `visibility：hidden` 隐藏对应元素，在文档布局中仍保留原来的空间（重绘）

### 9. 清除浮动
浮动带来的问题：
- 父元素的高度无法被撑开
- 与浮动元素同级的非浮动元素（内联元素）会跟随其后
- 若非第一个元素浮动，则该元素之前的元素也需要浮动，否则会影响页面显示的结构。

清除浮动的方式：
- 父级元素设置高度
- 最后一个浮动元素后加空div标签 并添加样式`clear:both`。
- 包含浮动元素的父元素设置样式`overflow: hidden`或`overflow: auto`
- 父级元素设置`zoom`