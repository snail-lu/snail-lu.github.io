---
title: SCSS常用语法总结
date: 2020-06-03 21:16:00
summary: SCSS基本语法。
tags:
- CSS
- SCSS
categories:
- [随笔]
- [前端]
top: true
---

### <span id="cate1">一、CSS扩展</span>
#### <span id="cate1_1">嵌套规则</span>
为了避免像CSS那样重复输入父选择器，SCSS允许代码CSS样式嵌套，内层的样式将其外层的选择器作为父选择器。
```scss
.home {
    color: #00ff00;
    font-size: 12px;

    .header {
        color: #ffffff;
        font-size: 14px;
    }
}
```
编译为：  
```css
.home {
    color: #00ff00;
    font-size: 12px;
}

.home .header {
    color: #ffffff;
    font-size: 14px;
}
```

#### <span id="cate1_2">父选择器``&``</span>
可以用 ``&`` 代表嵌套规则外层的父选择器，编译后的CSS文件中``&``会被替换为外层的父选择器。  
```scss
a {
    font-weight: bold;
    color: #000;
    text-decoration: none;
    
    // 示例1：伪类
    &:hover {
        text-decoration: underline;
    }
    
    // 示例2
    .header & {
        color: #f00;
    }
    
    // 示例3：拼接后缀（&必须放在首位）
    &-green {
        color: #0f0;
    }
}
```
编译为：
```css
a {
    font-weight: bold;
    color: #000;
    text-decoration: none;
}
a:hover {
    text-decoration: underline;
}
.header a {
    color: #f00;
}
a-green {
    color: #0f0;
}
```

#### <span id="cate1_3">属性嵌套</span>
像background、font、border、outline这些复合属性，都是具有多个单属性的。在可能仅需要个别单属性的情况下，可以使用属性嵌套来简化代码。
```scss
.box {
  border: {
    style: solid;
    color: red;
    width: 1px;
    radius: 10px;
  }
}
```
编译为：
```css
.box {
    border-style: solid;
    border-color: red;
    border-width: 1px;
    border-radius: 10px;
}
```

#### <span id="cate1_4">占位符选择器``%``</span>
单独使用占位符选择器时（未通过@extend调用），不会被编译到CSS中，仅当```@extend```指令调用，样式才会被编译到CSS中。
```scss
#context a%extreme {
    color: blue;
    font-weight: bold;
    font-size: 2em;
}


// 仅当使用了下列代码，scss才会编译出对应的css
.notice {
    @extend %extreme
}
```
编译为：
```css
#context a.notice {
    color: blue;
    font-weight: bold;
    font-size: 2em;
}
```


### <span id="cate2">二、注释</span>
多行注释``/* */``（会被编译到CSS文件中）
```scss
/* 
 *This comment is
 * several lines long.
 * since it uses the CSS comment syntax,
 * it will appear in the CSS output.
 */
```
单行注释``//``（不会被编译到CSS文件中）
```scss
// These comments are only one line long each.
// They won't appear in the CSS output.
```

### <span id="cate3">三、SassScript</span>
#### <span id="cate3_1">变量<code>$</code></span>  
变量以<code>$</code>开头，赋值方法与CSS写法一样。
```scss
// 全局变量
$color: red;

.header {
    color: $color;
}

.content {
    color: $color;
}

.box1 {
    // 局部变量，只能在该嵌套内部使用
    $width: 100px;
    
    // 局部变量转换为全局变量
    $size: 15px !global;
    width: $width;
    
    .inner {
        width: $width;
    }
}

.box2 {
    font-size: $size;
}
```

#### <span id="cate3_2">数据类型</span>
支持的数据类型有：
- 数字，`1, 2, 13, 10px`
- 字符串，有引号和无引号，`"foo", 'bar', baz`
- 颜色，`blue, #ff0000, rgba(0,0,0,0.5)`
- 布尔值，`true, false`
- 空值, `null`
- 数组，用空格或逗号做分隔符，`1.5em 1em 2em, Arial, sans-serif`
- maps, 相当于js中的Object，`(key1: value1, key2: value2)`

#### <span id="cate3_3">运算</span>
每种数据类型都支持相等运算`==`和`!=`，此外，每种数据类型也有其格子支持的运算方式。  
- 数字运算    
支持加、减、乘、除、取余等运算、关系运算（<,>,<=,>=）


- 颜色值运算   

```scss
p {
    color: #432542 + #78de1c;
}
a {
    color: #010203 * 2;
}
```
编译为：
```css
p {
    color: #bbff5e;
}
a {
    color: #020406;
}
```
- 字符串运算
```scss
p:before {
    // +号左侧为有引号的字符串，编译的结果为有引号的字符串
    content: "Foo " + Bar;
    
    // +号左侧为无引号的字符串，编译的结果为无引号的字符串
    font-family: sans- + "serif";
}
```
编译为：
```css
p:before {
    content: "Foo Bar";
    font-family: sans-serif; 
}
```

- 布尔值运算
支持`and`、`or`以及`not`运算

- 数组（List）运算
不支持任何运算方式，只能使用list functions控制。

#### <span id="cate3_4">函数</span>
详细用法参见[Sass函数列表](https://sass-lang.com/documentation/modules)  

### <span id="cate4">四、@-Rules与指令</span>
#### <span id="cate4_1">@import</span>
使用@import可以的导入SCSS或Sass文件，被导入的文件将合并编译到同一个CSS文件中。被导入的文件中包含的变量或混合指令都可以在导入的文件中使用。
```scss
// 导入单个文件（带扩展名）
@import 'foo.scss';

// 导入单个文件（不带扩展名，会尝试寻找扩展名为.scss或.sass的文件）
@import 'foo';

// 同时导入多个文件
@import 'rounded-corners', 'text-shadow';

// 一般导入都是不会在嵌套规则内，但是在嵌套规则内导入也是支持的
// 但是这样编译的样式也是会在嵌套内的
#main {
    @import "example"
}
```

#### <span id="cate4_2">@extend</span>
``@extend``用于将一个选择器下的所有样式继承给另一个选择器。  
```scss
.error {
    border: 1px #f00;
    background-color: #fdd;
}
.seriousError {
    @extend .error;
    border-width: 3px;
}
```

### <span id="cate5">五、控制指令</span>
#### <span id="cate5_1">@if</span>
当``@if``的表达式返回值不是``false``或者``null``时，条件成立，输出``{} ``内的代码：
```scss
p {
    @if 1 + 1 == 2 { border: 1px solid red; } 
    @if null  { border: 3px double blue; }
}
```
编译为：  
```css
p {
    border: 1px solid red;
}
```
类似``if...else if...else...``语句，``@if``也支持``@else if``和``@else``。  
```scss
$type: A;
p {
    @if $type == A {
        color: red;
    } @else if $type == B {
        color: green;
    } @else {
        color: blue;
    }
}
```

#### <span id="cate5_2">@for</span>
语法格式1：`@for $var from <start> through <end>`  
语法格式2：`@for $var from <start> to <end>`
`<start>`和`<end>`必须是整数；使用`to`时，遍历范围不包含`<end>`
```scss
@for $i from 1 through 3 {
    .item-#{$i} { width: 2em * $i; }
}
```

#### <span id="cate5_3">@each</span>
语法格式：`@each $var in <list>`  
```scss
@each $animal in puma, sea-slug, egret, salamander {
    .#{$animal}-icon {
        background-image: url('/images/#{$animal}.png');
    }
}
```

#### <span id="cate5_4">@while</span>
指令重复输出格式直到表达式返回结果为`false`。
```scss
$i: 6;
@while $i > 0 {
    .item-#{$i} { width: 2em * $i; }
    $i: $i - 2;
}
```

### <span id="cate6">六、mixin指令</span>
简单总结，详细用法参考文档。
```scss
// 定义
@mixin large-text {
    font: {
        family: Arial;
        size: 20px;
        weight: bold;
    }
    color: #ff0000;
}

// 引用混合样式（使用@include）
.page-title {
    @include: large-text;
    padding: 10px;
}

// 定义（带参数）
@mixin large-text($size, $color) {
    font: {
        family: Arial;
        size: $size;
        weight: bold;
    }
    color: $color;
}

// 引用（带参数）
.page-title {
    @include: large-text(20px, red);
    padding: 10px;
}
```

### <span id="refDoc">七、参考文档及网站链接</span>
[1. Sass中文文档](https://www.sass.hk/docs/)  
[2. Sass函数文档](https://sass-lang.com/documentation/modules)   
