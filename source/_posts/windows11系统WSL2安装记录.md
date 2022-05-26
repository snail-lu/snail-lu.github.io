---
title: windows11系统WSL2安装记录
date: 2021-12-16 15:47:30
summary: windows11下linux子系统安装实践
tags:
- Linux
categories:
- [随笔]
---
### 一、需求场景
今天在安装`Docker Desktop`时，出现提示`WSL2 installation is incomplete.`：
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-12/20211216155010.png)
于是，开始动手安装`WSL2`。

### 二、安装步骤 
1. 确保`控制面板`--`程序`--`Windows功能`中启用`适用于Linux的Windows子系统`及`虚拟机平台`;
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-12/20211216161653.png)

2. 管理员身份运行`PowerShell`，执行`wsl --install`，并不会像官方文档介绍的那样`安装运行适用于 Linux 的 Windows 子系统 (WSL) 所需的全部内容。`，而是出现`wsl`命令用法的帮助文档。
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-12/20211216180645.png)
查阅各种资料，使用`wsl --install`依然无效，只能改用[WSL文档](https://docs.microsoft.com/zh-cn/windows/wsl/)中的[旧版本的手动安装步骤](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual)进行安装。

3. 按照[旧版本的手动安装步骤](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual)进行安装，最终顺利安装完成。
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-12/20211217093054.png)

### 参考文档
[WSL文档](https://docs.microsoft.com/zh-cn/windows/wsl/)