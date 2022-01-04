---
title: CSS常用实现总结
date: 2021-12-15 21:41:11
tags:
- CSS
categories:
- [随笔]
- [前端]
---

### 一、文本超长省略
溢出的前提条件是宽度超出了，所以要确保元素有宽度确定，这样才能配合下列代码实现溢出效果。
```css
/* 单行溢出 */
.oneline-overflow {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

/* 多行溢出 */
.multiline-overflow{
    white-space: normal;
    text-overflow: ellipsis;
    display: -webkit-box;
    word-break: break-all;
    -webkit-box-orient: vertical;
    /* 设置行数 */
    -webkit-line-clamp: 2;
}
```

### 二、浏览器12px以下字号设置
大部分常用浏览器上显示字体最小为`12px`，css设置小于`12px`，显示的时候还是`12px`大小，可以利用css中的`transform`或者`zoom`来缩小：
```css
.small-font{
    font-size: 20px;                 /* 以此为基准进行缩放 */
    transform: scale(0.5);
    -webkit-transform: scale(0.5);
    transform-origin: left;          /* 设置缩放起始位置 */
    
    /* 利用transform: scale()缩小字体，虽然字体会变小，但是该元素所占的空间不会跟着缩放, 可以配合maring来调整位置 */
    margin: -10px; 

    /* 如果不想要上述效果，可以使用zoom进行缩放 */
    zoom: 0.5;
}

/* 浏览器兼容处理 */
/* IE、FF没有字体最小限制，可以直接设置字体大小 */
.small-font-fix {
    font-size: 20*0.5px;        
}
```

### 三、隐藏滚动条
```css
.box1::-webkit-scrollbar {
    display:none
}
```

### 四、三角形符号
```css
/* 向上的三角形 */
.triangle-up {
    width: 0;
    height: 0;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-bottom: 100px solid red;
}

/* 向右的三角形 */
.triangle-right {
    width: 0;
    height: 0;
    border-top: 50px solid transparent;
    border-bottom: 50px solid transparent;
    border-left: 100px solid red;
}

/* 向右上的三角形 */
.triangle-top-right {
    width: 0;
    height: 0;
    border-top: 50px solid red;
    border-left: 50px solid transparent;
}

/* 向左下的三角形 */
.triangle-top-right {
    width: 0;
    height: 0;
    border-bottom: 50px solid red;
    border-right: 50px solid transparent;
}

/* 其他方向类推即可 */
```

### 五、解决图片底部存在间隙问题
出现间隙的原因：图片文字等inline元素默许是跟父级元素的baseline对齐，而baseline又和底边有一定间距。
解决方法：
```css
/* 设置图片元素的垂直对齐方式 */
.img {
    vertical-align: bottom;
}

/* 设为块级元素 */
.img {
    display: block;
}

/* 父元素中没有别的元素时，可以设置父元素行高为0 */
.img-box {
    line-height: 0;
}

```

### 六、多行文字两端对齐
多行文案需要两端对齐从而更加美观好看。


### 七、不同大小文字底部对齐