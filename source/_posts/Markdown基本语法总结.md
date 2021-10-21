---
title: Markdown基本语法总结
date: 2020-04-27 17:40:29
summary: Marddown基本语法
keywords:
- markdown
tags:
- markdown
categories:
- [随笔]
top: true
---

## 块级元素

### 1. 段落和换行
**语法1：空行换行**
``` markdown
这是第一个段落......
(这里有一个空行)
这是第二个段落......
```
**实际效果：**  
段落1.....

段落2.....


**语法2：硬换行**
``` markdown
这是第一个段落....(这里有两个及以上的空格)
这是第二个段落....
```
**实际效果:**  
这是第一个段落....  
这是第二个段落....

### 2.标题

Markdown支持两种样式的标题书写形式：Setext和atx。

* **Setext书写形式**

**语法：使用=和-来区分标记级别**
``` markdown
This is an h1
=============
    
this is an h2
-------------
```
注：*其中=和-的长度是任意的*  
**实际效果：**  

This is an h1
=============
    
this is an h2
-------------

* **atx书写形式**

**语法：使用\#来区分标题级别（标题和#号之间要加空格）**   
``` markdown
# 一级标题
## 二级标题
...
###### 六级标题
```

**实际效果：**  
# 一级标题
## 二级标题
...
###### 六级标题


### 3.块引用
**语法：使用>字符引用段落**  
``` markdown
> This is a block quote with tow paragraphs.

> This is the paragraph two.
```
**实际效果：**  
> This is a block quote with tow paragraphs.

> This is the paragraph two.


### 4.列表
* **无序列表**

**语法：使用\*、+或-后接列表项**
``` markdown
* yesterday
* today
* tomorrow
```
等价于：  
``` markdown
+ yesterday
+ today
+ tomorrow
```
等价于：  
``` markdown
- yesterday
- today
- tomorrow
```
**实际效果：**  

* yesterday
* today
* tomorrow


* **有序列表**

**语法：使用数字加句号（英文）后接列表项**
``` markdown
1. Red
2. Green
3. Blue
```
*注：其中的数字序号对实际生成的序号没有影响。*  
**实际效果：**  
1. Red
1. Green
1. Blue

### 5.代码块
**语法：在每一行都缩进4个空格或1个水平制表符**
``` html
    <div>
        hello world
    </div>
```
*注：只有第一个水平制表符或头四个空格会在实际展现时移除，其余的都会在格式中保留下来。*  
**实际效果：**

    <div>
        hello world;
    </div>
    
### 6.水平线
**语法：一行中只有三个及以上的\-、\*或\_。\*和\-之间可以有空格。**
```
* * *
***
---
___
```
**实际效果：**
_ __ _ __
 
 
## 内联元素
### 1.链接
Markdown支持两种链接形式：`内联`和`引用`。   
```
语法：[链接文本](链接url "标题") 
```
实际效果：

This is an [example](http://example.com "example网站")

也可以先给链接配置id，后面统一配置链接地址和连接标题。语法如下：
```
    [链接文本1][链接id1]
    [链接文本2][链接id2]
    
    [id1]: 链接url  "标题"
    [id2]: 链接url  "标题"
```
**实际效果：**
    
This is the [example1][01].  
This is the [example2][02].

[01]: http://example1.com "example1站点"
[02]: http://example2.com "wxample2站点"

### 2.强调
用单`*`或单`_`包裹的文本将会使用`<em>`标签包裹；用双`*`或双`_`包裹的文本将会使用`<strong>`标签包裹。  
**语法如下：** 
```
    *重要内容*
    _重要内容_
    **更重要的内容**
    __更重要的内容__
```
**实际效果：**

这里有 *重要内容*。 


这里有 _重要内容_

这里有**更重要的内容**。

这里有 __更重要的内容__。

### 3.代码
**语法：使用重音符号`包裹代码片段。** 
**实际效果：**

Use `console.log(result)` to print the final result.

如果代码片段中有使用到`` ` ``符号，可以使用多重`` ` ``包裹该符号。语法如下：
```` markdown
    我们可以使用`` ` ``符号来插入代码片段。
````
**实际效果：**

我们可以使用`` ` ``符号来插入代码片段。

### 4.图片
语法类似于链接。  
**语法：**

```
    ![Alt text](/path/to/img.jpg)
    ![Alt text](/path/to/img.jpg "Optional title")
    
    ![Alt text][id]
    [id]: url/to/image "Optional title"
```

这种方式无法设置图片的尺寸，要设定尺寸可以使用`<img>`标签。

## 其他
### 1.自动链接
"自动"创建URL和email地址链接。
**语法：**
```
    个人网址：<http://example.com>
    个人邮箱：<address@example.com>
```

**实际效果：**

个人网址：<http://example.com>  
个人邮箱：<address@example.com>

### 2.反斜杠转义
需要展示一些特殊字符，如果不进行转义，markdown文档会将其视作语法符号。Markdown为下面字符提供反斜杠转义。

```
\  反斜线
`  重音符
*  星号
_  下划线
{} 花括号
[] 方括号
() 圆括号
#  井号
-  连字符
.  句号
!  感叹号
```
**语法：**
```
    +、\-、\*、/是最基本的四则运算。
```

**实际效果：**

+、\-、\*、/是最基本的四则运算。

### 3.表格  

**语法：**  
``` js
|标题1|标题2|标题3|
|----|----|----|
|文本1|文本2|文本3|
|文本4|文本5|文本6|

```

**实际效果：**
|标题1|标题2|标题3|
|----|----|----|
|文本1|文本2|文本3|
|文本4|文本5|文本6|

### 4.目录锚点
**语法：**

```
[1. 目录1](#cate1)  
[2. 目录2](#cate2)  
[3. 目录3](#cate3) 


...


### <span id="cate1">目录1</span>
### <span id="cate2">目录2</span>
### <span id="cate3">目录3</span>
```

**实际效果：**  
[1. 目录1](#cate1)  
[2. 目录2](#cate2)  
[3. 目录3](#cate3)  


...


### <span id="cate1">目录1</span>
### <span id="cate2">目录2</span>
### <span id="cate3">目录3</span>