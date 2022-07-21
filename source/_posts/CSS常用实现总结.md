---
title: CSS常用实现总结
date: 2021-12-15 21:41:11
summary: CSS常用需求实现
tags:
- CSS
categories:
- [CSS]
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
`H5`或`web`端：
```html
<!-- 退换货政策 -->
<div class="refund-policy">
	<div class="title">退换货政策</div>
	<div class="content">
		1.收到货品七天内可申请退换货（以签收日为准起始7天内），退换商品需保证包装及吊牌完好，未经穿着、洗涤、人为损坏、加工等不影响二次销售的情况我们的产品不满意，我们将提供退换货的服务，对于贴身衣服（内裤、袜子等），非质量问题，不予退换。若交易已完成，发现质量问题，请在交易完成后1客服沟通协商处理；对于参与多件多折、多金额段满减等产生差价的商品产生局部退货时，需要重新计算商品的价格，按实际收货的商品件数和金额来计算修改实际退款金额。换货仅支持调换同款尺码。<br />
		2.非质量问题退换货买家需承担运费，有质量问题的商品请先与我们联系，关于质量问题和发错商品的，我们承担运费。<br />
		3.退货时发票需跟随商品同时退回。<br />
		4.仓库确认收货后，退款将在5个工作日内退还，换货的商品也将在5个工作日内发出。
	</div>
</div>
```
```scss
.refund-policy {
	padding: 0 30px;
	box-sizing: border-box;
	width: 100%;
    
    .title {
		font-size: 28rpx;
		font-weight: bold;
		margin-bottom: 30rpx;
	}
	.content {
		font-size: 24px;
		white-space: pre-line;
		text-align: justify;
	}
}
```
微信小程序：
```html
<!-- 此种标签写法，内部使用了换行符，安卓手机上文本可以两端对齐，ios上无效 -->
<view class="refund-policy">
	<view class="title">退换货政策</view>
	<view class="content">1.收到货品七天内可申请退换货（以签收日为准起始7天内），退换商品需保证包装及吊牌完好，未经穿着、洗涤、人为损坏、加工等不影响二次销售的情况下，如您对我们的产品不满意，我们将提供退换货的服务，对于贴身衣服（内裤、袜子等），非质量问题，不予退换。若交易已完成，发现质量问题，请在交易完成后15天内联系客服沟通协商处理；对于参与多件多折、多金额段满减等产生差价的商品产生局部退货时，需要重新计算商品的价格，按实际收货的商品件数和金额来计算优惠金额，修改实际退款金额。换货仅支持调换同款尺码。\n2.非质量问题退换货买家需承担运费，有质量问题的商品请先与我们联系，关于质量问题和发错商品的，我们承担运费。\n3.退货时发票需跟随商品同时退回；\n4.仓库确认收货后，退款将在5个工作日内退还，换货的商品也将在5个工作日内发出。</view>
</view>

<!-- 为了兼容ios，将文本拆分 -->
<view class="refund-policy">
	<view class="title">退换货政策</view>
	<view class="content">1.收到货品七天内可申请退换货（以签收日为准起始7天内），退换商品需保证包装及吊牌完好，未经穿着、洗涤、人为损坏、加工等不影响二次销售的情况下，如您对我们的产品不满意，我们将提供退换货的服务，对于贴身衣服（内裤、袜子等），非质量问题，不予退换。若交易已完成，发现质量问题，请在交易完成后15天内联系客服沟通协商处理；对于参与多件多折、多金额段满减等产生差价的商品产生局部退货时，需要重新计算商品的价格，按实际收货的商品件数和金额来计算优惠金额，修改实际退款金额。换货仅支持调换同款尺码。
	</view>
	<view class="content">2.非质量问题退换货买家需承担运费，有质量问题的商品请先与我们联系，关于质量问题和发错商品的，我们承担运费。</view>
    <view class="content">3.退货时发票需跟随商品同时退回。</view>
    <view class="content">4.仓库确认收货后，退款将在5个工作日内退还，换货的商品也将在5个工作日内发出。</view>
</view>
```
```scss
.refund-policy {
	padding: 0 30px;
	box-sizing: border-box;
	width: 100%;

    .title {
		font-size: 28rpx;
		font-weight: bold;
		margin-bottom: 30rpx;
	}
	.content {
		font-size: 24px;
		white-space: pre-line;
		text-align: justify;
	}
}

// 为了兼容ios
.refund-policy {
	padding: 0 30px;
	box-sizing: border-box;
	width: 100%;
	.content {
		font-size: 24px;
		text-align: justify;
	}
}
```

### 七、不同大小文字底部对齐
```scss
// 利用vertical-align，看效果选择
.box {
    vertical-align: bottom;
    // 或
    vertical-align: baseline;
}

// 利用flex布局的align-items
.box {
    display: flex;
    align-items: baseline;
    // 或
    align-items: flex-end;
}


```