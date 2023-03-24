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
- 块级元素
  `div`, `ul`, `li`, `dl`, `dt`, `dd`, `h1`~`h5`, `p`等；
- 行内元素（内联元素）
  `a`, `b`, `span`, `img`, `input`, `select`, `strong`, `button`等


#### 3. 语义化
HTML标签的语义化，就是用正确的标签做正确的事情，给某块内容用上一个最恰当最合适的标签，使页面有良好的结构，页面元素有含义，无论是谁都能看懂这块内容是什么。
**作用：**
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
<!-- html5中只需要写这个 -->
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

#### 5. HTML5新增了哪些新特性？移除了哪些元素？
HTML5主要是关于图像、位置、存储、多任务等功能的增加：
- 语义化标签，如：`article`、`footer`、`header`、`nav`等
- 视频`video`、音频`audio`
- 画布`canvas`
- 表单控件，`calendar`、`date`、`time`、`email`
- 地理
- 本地离线存储，`localStorage`长期存储数据，浏览器关闭后数据不丢失，`sessionStorage`的数据在浏览器关闭后自动删除
- 拖拽释放

移除的元素：
- 纯表现的元素：`basefont`、`font`、`s`、`strike`、`tt`、`u`、`big`、`center`
- 对可选用性产生负面影响的元素：`frame`、`frameset`、`noframes`

#### 6. SEO相关的标签
TDK，即·`title`、`description`、`keywords`这三个标签，`title`表示标题标签，`description`是描述标签，`keywords`是关键词标签

#### 7. src 和 href 的区别
`src`和`href`都是HTML中特定元素的属性，都可以用来引入外部的资源。两者区别如下：
- `src`：全称`source`，它通常用于`img`、`video`、`audio`、`script`元素，通过 `src` 指向请求外部资源的来源地址，指向的内容会嵌入到文档中当前标签所在位置，在请求`src`资源时，它会将资源下载并应用到文档内，比如说：`js`脚本、`img`图片、`frame`等元素。当浏览器解析到该元素时，会暂停其它资源下载，直到将该资源加载、编译、执行完毕。这也是为什么将`js`脚本放在底部而不是头部的原因。
- `href`：全称`hyper reference`，意味着超链接，指向网络资源，当浏览器识别到它指向的⽂件时，就会并⾏下载资源，不会停⽌对当前⽂档的处理，通常用于`a`、`link`元素。

#### 8. script标签上的 defer 和 async 的区别
- `<script src="main.js"></script>`
  不带`defer`和 `async`，浏览器读取到该标签就会立即加载并执行相应的脚本，会阻塞后续页面文档的解析
- `<script async src="main.js"></script>`
  带`async`属性，异步脚本，即页面文档的解析与js脚本的加载是并行进行的，但是js脚本的执行依然会阻塞页面的解析。多个异步脚本的执行顺序与书写顺序不一定一致。
- `<script defer src="main.js"></script>`
  带`defer`属性，延迟脚本，即读取到该标签时，会去异步加载该脚本，不会阻塞页面文档的解析，脚本会被延迟到整个页面解析完成之后、`DOMContentLoaded`事件触发之前执行。多个延迟脚本，理论上是按照顺序执行的，但是现实中不一定。

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
**`flex`布局**
弹性布局，由父级容器、子元素构成，通过设置主轴和交叉轴来控制子元素的排序方式。这种布局方式是一维布局，适合做局部布局，比如导航栏
父级容器属性：
- `flex-direction`:  row | row-reverse | column | column-reverse;  子元素排列方向
- `flex-wrap`：nowrap | wrap | wrap-reverse;  是否允许换行
- `flex-flow`：  <'flex-direction'>  || <'flex-wrap'> ; `flex-direction`和`flex-wrap`的简写形式，默认值为`row nowrap`
- `justify-content`: flex-start | flex-end | center | space-between | space-around;  子元素在主轴上的对齐方式。
- `align-items`:  flex-start | flex-end | center | baseline | stretch;  子元素在交叉轴上的对齐方式。
- `align-content`:  flex-start | flex-end | center | space-between | space-around | stretch; 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用

子元素属性：
- `order`: 子元素的排列顺序。数值越小，排列越靠前，默认为`0`
- `flex-grow`: 子元素的放大比例，默认为`0`
- `flex-shrink`: 子元素的缩小比例，默认为`1`，空间不足时会缩小该元素，设为`0`禁止缩小
- `flex-basis`: 子元素在主轴方向上的初始大小。
- `flex`: `flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为 `0 1 auto`
- `align-self`: 允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

**`grid`布局**
网格布局， 将网页划分成一个个网格，可以任意组合不同的网格，做出各种各样的布局。网格是一组相交的水平线和垂直线，它定义了网格的列和行。我们可以将网格元素放置在与这些行和列相关的位置上。这种布局方式是二维布局，适合做整个页面的规划。

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
- `display：none` 会让元素完全从渲染树中消失，不占据空间，不可点击，设置时会造成`回流 + 重绘`
- `visibility：hidden` 在文档布局中仍保留原来的空间，只是内容不可见，不可点击，设置时会造成`重绘`
- `opacity: 0` 在文档布局中仍然占据空间，只是内容不可见，可以点击，设置时会造成`重绘`

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

#### 10. 尺寸单位
- px：绝对单位，在同一设备上，每个像素代表的物理长度是固定的，在不同设备上，每个像素代表的物理长度可能是不同的
- em：相对单位，相对于当前元素字体大小或与之最近的已经设置了`font-size`的父级元素的字体大小
- rem：相对单位，相对于根元素`html`字体大小的单位
- %：相对单位，相对于父元素的百分比单位，例如`height`中的`%`是相对于父元素的`height`, `width`、`margin`、`padding`中的`%`是相对于父元素的`width`来计算的
- vh：相对单位，相对于**视口高度**的 `1/100` 单位
- vw：相对单位，相对于**视口宽度**的 `1/100` 单位
- cm：厘米，实际尺寸单位，`1cm = 37.8px`
- mm：毫米，实际尺寸单位，`1mm = 3.78px`
- in：英寸，实际尺寸单位，`1in = 96px`
- pt：点（point），字体尺寸单位，排版印刷时使用
- pc：派卡（pica），字体尺寸单位，`1pc = 16px`， 印刷时使用

📕[一文读懂 CSS 单位](https://www.51cto.com/article/719354.html)

#### 11. 可以继承的样式