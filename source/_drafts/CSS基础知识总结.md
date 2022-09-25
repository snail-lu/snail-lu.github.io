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
### 2. 布局


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

