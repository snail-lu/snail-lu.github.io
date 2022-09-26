---
title: 使用http-server启动本地服务开发静态页面
date: 2020-05-02 20:49:16
summary: 如何安装及启动http-server，以实现静态页面真机预览及联调。
tags:
- http-server
- Node
categories:
- [项目开发]
---

### 1. 安装`http-server`
打开cmd(我自己使用的是git bash)，输入命令：

```bash
 npm install http-server -g  
```

### 2. 进入项目根目录
这里是通过命令行进入到项目文件夹中，windows中也可以在资源管理器中直接进入到项目文件夹后，使用`shift+"鼠标右键"`,选择`在此处打开命令行（PowerShell）窗口`

```bash  
cd /d/document/projects/wxCoupon
```

### 3. 启动`http-server`
在命令行中，接着输入以下命令，启动`http-server`服务器，默认端口号为`8080`

```bash
hs -o 
或 
http-server -open  
```

### 4. 查看效果
正常启动后，会出现以下信息：
![](https://s1.ax1x.com/2022/08/14/vUC8u8.png)

此时，在浏览器中访问电脑端访问地址并打开开发者工具，即可看到浏览器端模拟的开发效果。
![](https://s1.ax1x.com/2022/08/14/vUCGDS.png)

使用手机（需要和电脑处于同一局域网中）访问手机端访问地址，即可实现真机查看开发效果：
![](https://s1.ax1x.com/2022/08/14/vUCJHg.png)

> Tips: 若手机无法访问该网络，查看是否是电脑局域网防火墙没有关闭，将防火墙关闭即可。