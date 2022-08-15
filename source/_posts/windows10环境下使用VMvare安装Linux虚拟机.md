---
title: windows10环境下使用VMvare安装Linux虚拟机
date: 2020-05-05 22:18:09
summary: windows10环境下使用VMvare安装Linux虚拟机实践
tags:
- CentOS
- SSH
- VMvare
- Linux
categories:
- [随笔]
---

### 软件版本
- 宿主机操作系统： `Windows 10 家庭版`
- 虚拟机软件： `VMvare Workstation Pro 15.5.2 for Windows`
- Linux系统镜像： `CentOS 8.1 64位`
- SSH终端软件： `SecureCRT 8.5`

### 安装步骤
#### 1. 软件下载 
- VMvare Workstation 15.5 Pro for Windows[下载地址](https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html)
- CentOS 8.1 ISO [下载地址](http://isoredirect.centos.org/centos/8/isos/x86_64/CentOS-8.1.1911-x86_64-dvd1.iso)
- SecureCRT 8.5 [下载地址](https://www.vandyke.com/cgi-bin/releases.php?product=securecrt)

#### 2. 软件安装 
安装过程只要按照软件提示一步步进行就好了，这里不再赘述。

#### 3. 创建新的虚拟机
![vUiGkQ.png](https://s1.ax1x.com/2022/08/14/vUiGkQ.png)

#### 4. 自定义虚拟机配置
![vUi3Tg.png](https://s1.ax1x.com/2022/08/14/vUi3Tg.png)

#### 5. 设置虚拟机硬件兼容性
使用默认配置即可
![vUilm8.png](https://s1.ax1x.com/2022/08/14/vUilm8.png)

#### 6. 指定系统镜像位置
![vUiMOf.png](https://s1.ax1x.com/2022/08/14/vUiMOf.png)

#### 7. 配置简易安装信息
![vUi10S.png](https://s1.ax1x.com/2022/08/14/vUi10S.png)
#### 8. 配置名称及存储位置
我这里只更改了名称，位置使用了默认路径。
![vUiJYj.png](https://s1.ax1x.com/2022/08/14/vUiJYj.png)
#### 9. 处理器配置
按自己需求配置即可
![vUiYfs.png](https://s1.ax1x.com/2022/08/14/vUiYfs.png)
#### 10. 内存配置
按自己需求配置即可
![vUiNpn.png](https://s1.ax1x.com/2022/08/14/vUiNpn.png)

#### 11. 网络连接类型配置
使用桥接网络
![vUiUlq.png](https://s1.ax1x.com/2022/08/14/vUiUlq.png)

#### 12. I/O控制器类型配置
默认值即可
![vUia60.png](https://s1.ax1x.com/2022/08/14/vUia60.png)

#### 13. 磁盘类型配置
默认值即可
![vUidXV.png](https://s1.ax1x.com/2022/08/14/vUidXV.png)

#### 14. 选择磁盘
选择`创建新虚拟磁盘`
![vUi0mT.png](https://s1.ax1x.com/2022/08/14/vUi0mT.png)

#### 15. 配置磁盘容量
默认值即可
![vUiB0U.png](https://s1.ax1x.com/2022/08/14/vUiB0U.png)

#### 16. 指定磁盘文件的存储位置
默认值即可
![vUiD7F.png](https://s1.ax1x.com/2022/08/14/vUiD7F.png)

#### 17. 配置完成
![vUiytJ.png](https://s1.ax1x.com/2022/08/14/vUiytJ.png)

#### 18. 报错
安装报错：`Section %packages does not end with %end` （没报错忽略步骤18、19）
![vdC8HO.png](https://s1.ax1x.com/2022/08/15/vdC8HO.png)

#### 19. 报错处理
- 在工具栏上找到关机按钮，关闭此客户机操作系统，并编辑虚拟机配置
![](https://s1.ax1x.com/2022/08/15/vdCYUe.png)

- 取消勾选 `CD/DVD (IDE)` 设备状态中的`启动时连接`，确定后重新开启虚拟机
![](https://s1.ax1x.com/2022/08/15/vdC3DK.png)

#### 20. 进入安装界面
![](https://s1.ax1x.com/2022/08/15/vdC1u6.png)

#### 21. 开始安装
鼠标移入界面后，按下回车键，开始进行系统安装。
![](https://s1.ax1x.com/2022/08/15/vdCJED.png)

#### 22. 选择系统语言
![](https://s1.ax1x.com/2022/08/15/vdCt4H.png)

#### 23. 时区等配置
时区改为`亚洲/上海`，系统安装目的地，点击进入使用默认选项即可
![](https://s1.ax1x.com/2022/08/15/vdCa8A.png)

#### 24. 用户设置
设置root密码和创建用户
![](https://s1.ax1x.com/2022/08/15/vdCUCd.png)

#### 26. 安装完成
![](https://s1.ax1x.com/2022/08/15/vdCwvt.png)

#### 27. 重启
进行初始设置，接受许可证
![](https://s1.ax1x.com/2022/08/15/vdCdgI.png)

#### 28. 系统登录页面
![](https://s1.ax1x.com/2022/08/15/vdCrb8.png)

#### 29. 安装完成
![](https://s1.ax1x.com/2022/08/15/vdCBKP.png)

### 网络配置
#### 1. 开启网络
安装完成的Linux虚拟机，网络是没有开启的，需要手动开启网络
![](https://s1.ax1x.com/2022/08/15/vdCDDf.png)

#### 2. 网路测试
连接网络后，进行网络连接测试
![](https://s1.ax1x.com/2022/08/15/vdCc5Q.png)
如果出现可以连接其他虚拟Linux节点，但是无法访问宿主机的情况，要考虑下是否是宿主机的防火墙未关闭。

#### 3. 反向测试
使用宿主机进行反向测试
![](https://s1.ax1x.com/2022/08/15/vdC6Ug.png)

### 使用ssh终端进行登录
#### 1. 创建会话
![](https://s1.ax1x.com/2022/08/15/vdCyVS.png)

#### 2. 连接成功
![](https://s1.ax1x.com/2022/08/15/vdCyVS.png)
