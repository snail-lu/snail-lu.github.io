---
title: 使用http-server启动本地服务开发静态页面
date: 2020-05-02 20:49:16
summary: http-server的简单实践。
tags:
- http-server
- Node
categories:
- [项目开发]
---
### 1. 全局安装`http-server`
工具：`Git Bash`
```bash
npm install http-server -g  
```

### 2. 进入项目根目录
```bash  
cd /d/document/projects/wxCoupon
```

### 3. 启动`http-server`
在命令行中，接着输入以下命令，启动`http-server`服务器，默认端口号为`8080`
```bash
http-server -open
```
### 4. 查看效果
正常启动后，会出现以下信息：
![](https://s1.ax1x.com/2022/08/14/vUC8u8.png)

此时，在浏览器中访问电脑端访问地址并打开开发者工具，即可看到浏览器端模拟的开发效果。
![](https://s1.ax1x.com/2022/08/14/vUCGDS.png)

使用手机（需要和电脑处于同一局域网中）访问手机端访问地址，即可实现真机查看开发效果：
![](https://s1.ax1x.com/2022/08/14/vUCJHg.png)