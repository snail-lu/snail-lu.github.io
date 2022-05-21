---
title: React+Webpack项目中使用Iconfont字体图标
date: 2020-05-02 15:59:47
summary: React项目中如何配置及使用iconfont图标
tags:
- React
- Webpack
- Iconfont
categories:
- [项目开发]
---

### 1、下载字体图标
[Iconfont官网](https://www.iconfont.cn/ 'Iconfont')，收藏自己要用的图标到自己的项目下，下载后将文件解压：

![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/iconfont/1610029-20200502154112751-1052215406.png)
> Tips: 其中`demo`开头的文件为图标样式示例文件，可以在浏览器中打开，便于使用图标时查看图标名称或编码。可以不放到项目中。

### 2、项目中导入图标文件
将所需的字体文件和样式文件复制到项目的静态资源文件夹中：
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/iconfont/1610029-20200502154129525-228099742.png)

### 3、引入图标样式
在`App.js`中或者需要使用图标字体样式的组件中引入`iconfont.css`文件：
``` jsx
import '../../style/iconfont/iconfont.css';  // 注意路径不要写错
```

### 4、使用图标
在组件中要使用图标字体的位置，可以通过使用`iconfont类`或`unicode编码`来实现图标效果：  
``` jsx
    // 使用类名来实现图标效果
    // iconfont为iconfont.css文件中定义的类名，必写
    // icon-meishi 为iconfont.css文件中要使用的图标类名
    <span className="iconfont icon-meishi"></span>     // jsx文件
    
    // 使用unicode来实现图标效果
    // iconfont为iconfont.css文件中定义的类名，必写
    <span className="iconfont">&#xe61c;</span>         // jsx文件    
```
### 5、编译报错
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/iconfont/1610029-20200502154207285-1011437676.png)
无法解析处理`ttf`字体文件，需要安装对应的加载器。

### 6、安装file-loader
``` bash
npm install file-loader --save-dev
```

`webpack.config.js`中配置：
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/iconfont/1610029-20200502154224797-637029206.png)


### 7、最终实现效果
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/iconfont/1610029-20200502154249813-892010816.png)