---
title: HTML&CSS基础知识总结
date: 2020-10-21 22:17:16
summary: HTML&CSS基础知识总结
tags:
- HTML
- CSS
categories:
- [前端基础]
- [面试]
---

### 一、HTML基础
#### 1. HTML、XML、XHTML 的区别
- `HTML`：超文本标记语言，是语法较为松散的、不严格的`Web`语言，主要用于显示数据；
- `XML`：可扩展标记语言，主要用于传输和存储数据，可扩展，可以自定义标签；
- `XHTML`：可扩展超文本标记语言，基于`XML`，作用与`HTML`类似，但语法更严格。

XHTML与HTML的区别：
​- `XHTML`标签名必须小写；
- `XHTML`元素必须被关闭；
- `XHTML`元素必须被正确的嵌套；
- `XHTML`元素必须要有根元素。
​
XHTML与HTML5的区别：
​- `HTML5`新增了`canvas`绘画元素；
- `HTML5`新增了用于绘媒介回放的`video`和`audio`元素；
- 更具语义化的标签，便于浏览器识别；
- 对本地离线存储有更好的支持；
- `MathML`，`SVG`等，可以更好的`render`；
- 添加了新的表单控件：`calendar`、`date`、`time`、`email`等。
​
HTML、XHTML、HTML5之间的联系：
​- `XHTML`是`HTML`规范版本；
- `HTML5`是`HTML`、`XHTML`以及`HTML DOM`的新标准。

#### 2. 元素分类
- 行内元素： `a`, `b`, `span`, `img`, `input`, `select`, `strong`;
- 块级元素： `div`, `ul`, `li`, `dl`, `dt`, `dd`, `h1-5`, `p`等；
- 空元素： `<br>`, `<hr>`, `<img>`, `<link>`, `<meta>`;

#### 3. 语义化
HTML标签的语义化，就是用正确的标签做正确的事情，给某块内容用上一个最恰当最合适的标签，使页面有良好的结构，页面元素有含义，无论是谁都能看懂这块内容是什么。
**优点：**
- 代码结构: 使页面在没有css的情况下，也能够呈现出好的内容结构
- 有利于SEO: 爬虫根据标签来分配关键字的权重，因此可以和搜索引擎建立良好的沟通，帮助爬虫抓取更多的有效信息
- 方便其他设备解析： 如屏幕阅读器、盲人阅读器、移动设备等，以有意义的方式来渲染页面
- 便于团体的开发和维护: 语义化使代码更具有可读性，让其他开发人员更加理解你的html结构，减少差异化。遵循 W3C 标准的团队都遵循这个标准。

**常见的语义化标签：**
- header
- nav
- main
- footer
- article
- section
- aside

#### 4. DOCTYPE
`Doctype` 是 `HTML` 的文档类型声明，通过它可以告诉浏览器，使用哪一个 `HTML` 版本标准解析文档。
在浏览器发展的过程中，`HTML`出现过很多版本，不同的版本之间格式书写上略有差异。如果没有事先告诉浏览器，那么浏览器就不知道文档解析标准是什么？此时，大部分浏览器将开启最大兼容模式来解析网页，我们一般称为`怪异模式`，这不仅会降低解析效率，而且会在解析过程中产生一些难以预知的bug，所以文档声明是必须且必要的。
```html
<!-- 标准模式，其他类型的模式现在基本上很少用到 -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body></body>
</html>
```
💡[浏览器如何选择各种模式](https://hsivonen.fi/doctype/)

#### 5. 

### 二、CSS基础
#### 1. 盒模型
盒模型中均包含元素内容`content`、内边距`padding`、边框`border`及外边距`margin`四部分
- **标准盒模型**: 
  - `width = content`
  - 通过 `box-sizing: content-box;` 来使用标准盒模型
- **IE盒模型**：
  - `width = content + padding + border`
  - 通过`box-sizing: border-box;`来使用IE盒模型

#### 2. 布局
- `flex`布局是一维布局，适合做局部布局，比如导航栏
- `grid`布局是二维布局，适合做整个页面的规划

#### 3. 选择器
**优先级：**
1. `!important`会覆盖页面内任何位置的元素样式
2. 内联样式，如 `style="color: green"`，权值为 `1000`
3. ID选择器，如`#app`，权值为 `100`
4. 类、伪类、属性选择器，如`.foo, :first-child, div[class="foo"]`，权值为 `10`
5. 标签、伪元素选择器，如`div::first-line`，权值为 `1`
6. 通配符、子类选择器、兄弟选择器，如`*, >, +`，权值为 `0` 
7. 继承的样式没有权值

#### 4. 定位
- static
- relative
- absolute
- fixed
- sticky

#### 5. BFC
`BFC` 是 `Block Formatting context`：块级格式化上下文。具有 `BFC` 特性的元素可以看作是隔离了的独立容器，拥有不影响外界且不被外界所影响的布局规则。

**布局规则：**
- **包含内部浮动**
    开启了BFC的元素在计算高度时，浮动的元素也包含在内，即父元素会被浮动的子元素撑开。
- **排除外部浮动**
    开启了BFC的元素会阻止自己的文字内容去环绕相邻的浮动元素
- **阻止外边距重叠**
    开启了BFC的元素会阻止自己与相邻元素的`margin-top`或`margin-bottom`发生重叠。
- **阻止外边距塌陷**
    开启了BFC的元素会阻止自己的`margin-top`产生伪作用于父元素的现象。

**触发方式：**
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

**常见应用：**
- 解决浮动元素令父元素高度坍塌的问题
- 解决非浮动元素被浮动元素覆盖问题
- 解决外边距垂直方向重合问题


#### 6. 垂直居中实现方式
- absolute + margin负值
- absolute + transform
- flex
- grid
- `table-cell` + `vertical-align: middle;`
- float + margin

#### 7. CSS3新特性
- RGBA和透明度
- background-image background-origin(content-box/padding-box/border-box) background-size background-repeat
- word-wrap（对长的不可分割单词换行）word-wrap：break-word
- 文字阴影：text-shadow： 5px 5px 5px #FF0000;（水平阴影，垂直阴影，模糊距离，阴影颜色）
- font-face属性：定义自己的字体
- 圆角（边框半径）：border-radius 属性用于创建圆角
- 边框图片：border-image: url(border.png) 30 30 round
- 盒阴影：box-shadow: 10px 10px 5px #888888
- 媒体查询：定义两套css，当浏览器的尺寸变化时会采用不同的属性

#### 8. 隐藏元素的方式
- `display：none` 不显示对应的元素，在文档布局中不再分配空间（回流+重绘）
- `visibility：hidden` 隐藏对应元素，在文档布局中仍保留原来的空间（重绘）

#### 9. 清除浮动
**浮动导致的问题：**
- 父元素的高度无法被撑开
- 与浮动元素同级的非浮动元素（内联元素）会跟随其后
- 若非第一个元素浮动，则该元素之前的元素也需要浮动，否则会影响页面显示的结构。

**清除浮动的方式：**
- 父级元素设置高度
- 最后一个浮动元素后加空div标签 并添加样式`clear:both`。
- 包含浮动元素的父元素设置样式`overflow: hidden`或`overflow: auto`
- 父级元素设置`zoom`