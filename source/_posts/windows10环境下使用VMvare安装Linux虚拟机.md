---
title: windows10环境下使用VMvare安装Linux虚拟机
date: 2020-05-05 22:18:09
summary: windows10环境下使用VMvare安装Linux虚拟机体验。
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
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212506641-397719604.png)

#### 4. 自定义虚拟机配置
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212559842-1450751586.png)

#### 5. 设置虚拟机硬件兼容性
使用默认配置即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212725252-930402878.png)

#### 6. 指定系统镜像位置
将镜像地址指向自己下载好的Linux镜像文件地址即可。
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212732317-215440586.png)

#### 7. 配置简易安装信息
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212732317-215440586.png)
#### 8. 配置名称及存储位置
我这里只更改了名称，位置使用了默认路径。
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212828860-1311874380.png)
#### 9. 处理器配置
按自己需求配置即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212836865-1178715123.png)
#### 10. 内存配置
按自己需求配置即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212843299-192235076.png)

#### 11. 网络连接类型配置
使用桥接网络
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212849458-1162105469.png)

#### 12. I/O控制器类型配置
默认值即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212906347-597953643.png)

#### 13. 磁盘类型配置
默认值即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212912251-1756270358.png)

#### 14. 选择磁盘
选择`创建新虚拟磁盘`
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212919164-1840071717.png)

#### 15. 配置磁盘容量
默认值即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505212925809-1498233658.png)

#### 16. 指定磁盘文件的存储位置
默认值即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213003452-6980476.png)

#### 17. 配置完成
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213010534-2034553820.png)

#### 18. 报错
安装报错：`Section %packages does not end with %end` （没报错忽略步骤18、19）
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213018507-1966327036.png)

#### 19. 报错处理
- 在工具栏上找到关机按钮，关闭此客户机操作系统，并编辑虚拟机配置
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213046470-405745597.png)

- 取消勾选 `CD/DVD (IDE)` 设备状态中的`启动时连接`，确定后重新开启虚拟机
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213101105-138428487.png)

#### 20. 进入安装界面
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213112187-664525513.png)

#### 21. 开始安装
鼠标移入界面后，按下回车键，开始进行系统安装。
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213117491-1328763925.png)

#### 22. 选择系统语言
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213133872-562760715.png)

#### 23. 时区等配置
时区改为`亚洲/上海`，系统安装目的地，点击进入使用默认选项即可
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213140649-1837353948.png)

#### 24. 用户设置
设置root密码和创建用户
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213150367-1079272039.png)

#### 26. 安装完成
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213200301-1125335387.png)

#### 27. 重启
进行初始设置，接受许可证
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213220095-426658966.png)

#### 28. 系统登录页面
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213227530-753033842.png)

#### 29. 安装完成
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213236031-715559728.png)

### 网络配置
#### 1. 开启网络
安装完成的Linux虚拟机，网络是没有开启的，需要手动开启网络
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213254874-1541423838.png)

#### 2. 网路测试
连接网络后，进行网络连接测试
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213300197-149747629.png)
如果出现可以连接其他虚拟Linux节点，但是无法访问宿主机的情况，要考虑下是否是宿主机的防火墙未关闭。

#### 3. 反向测试
使用宿主机进行反向测试
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213307774-533253695.png)

### 使用ssh终端进行登录
#### 1. 创建会话
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213314073-257092472.png)

#### 2. 连接成功
![](https://fastly.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2021-10/vmvare//1610029-20200505213328869-1764961625.png)
