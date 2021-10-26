---
title: Emmet常用语法总结
date: 2020-08-02 21:48:03
summary: Emmet常用语法总结。
top: true
tags:
- Emmet
- HTML
- CSS
categories:
- [随笔]
- [前端]
---

### 概述
> Emmet is a web-developer's toolkit that can greatly improve your HTML & CSS workflow.

`VS Code`编辑器默认支持`Emmet`语法。


### 一、HTML缩写
#### 嵌套运算符
常用嵌套运算符有：`>`、`+`、`^`、`*`、`()`
##### 逐级向下生成嵌套的元素：`>`  
使用示例：  

``` html
div>ul>li
```
输出为：

``` html
<div>
    <ul>
        <li></li>
    </ul>
</div>
```
##### 生成同级相邻的元素：`+` 
使用示例：
``` html
div+p+bq
```
输出为：
```html
<div></div>
<p></p>
<blockquote></blockquote>
```
##### 向上一个层级：`^`
与`>`作用效果刚好相反，可以连用。使用示例：
```html
div>p>span+em^p>a
```
输出为：
```html
<div>
    <p><span></span><em></em></p>
    <p><a href=""></a></p>
</div>
```
##### 生成多个重复元素：`*`
使用示例：
``` emmet
ul>li*5
```
输出为：
``` html
<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```
##### 改变元素的分组结合顺序：`()` 
使用示例：
``` emmet
div>(header>ul>li*2>a)+footer>p
```
输出为：
``` html
<div>
    <header>
        <ul>
            <li></li>
            <li></li>
        </ul>
    </header>
    <footer>
        <p></p>
    </footer>
</div>
```

#### 属性运算符
##### 给元素设置id和class  
使用示例：
``` emmet
div#header+div.page+div#footer.class1.class2.class3
```
输出为：
```html
<div id="header"></div>
<div class="page"></div>
<div id="footer" class="class1 class2 class3"></div>
```

##### 设置其他属性
属性值中没有空格的时候可以不用输引号。使用示例：
``` html
input[type=text placeholder=请输入用户名]
```
输出为：
``` html
<input type="text" pleceholer="请输入用户名">
``` 

##### 自动编号：`$`

使用示例：
```
// 示例1:
ul>li.item$*5

// 示例2：指定最小序号
ul>li.item$@3*5

// 示例3：降序
ul>li.item$@-*5

// 示例4：指定降序最小序号
ul>li.item$@-3*5
```
输出为：
```html
<!-- 示例1 -->
<ul>
    <li class="item1"></li>
    <li class="item2"></li>
    <li class="item3"></li>
    <li class="item4"></li>
    <li class="item5"></li>
</ul>

<!-- 其他输出结果省略 -->
```

##### 元素内插入文本：`{}` 

使用示例：
```
a{Click me}
```
输出为：
```html
<a href="">Click me</a>
```

#### "Lorem Ipsum"生成器
随机文本填充，使用示例：  
```
// 示例1：默认会生成一段30个单词的文本
lorem

// 示例2：指定文本中单词数
lorem100

// 示例3：重复生成
ul>li>lorem10

```

### 二、CSS缩写
#### 属性缩写  
```
m10 --> margin: 10px;
m10-20 --> margin: 10px 20px;
m-10--20 --> margin: -10px -20px;
mr10 --> margin-right: 10px;
```

#### 常用别名
```
p --> %
e --> em
x --> ex

// 示例
w100p --> width: 100%;
```

#### 色值
```
#1 --> #111111
#e0 --> #e0e0e0
#fc0 --> #ffcc00
```

#### !important
```
! --> !important
```

### 参考文档及网站链接
[Emmet Documentation](https://docs.emmet.io)  
