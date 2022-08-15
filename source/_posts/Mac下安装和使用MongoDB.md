---
title: Mac下安装和使用MongoDB（下载压缩包的方式）
date: 2019-12-10 17:08:40
summary: Mac上通过压缩包安装MongoDB和简单使用。
tags:
- Mac
- MongoDB
categories:
- [数据库]
---
### 1.下载安装
官网下载地址：[MongoDB Download Center | Community](https://www.mongodb.com/download-center/community). community版本可以免费使用
![vUi9Qx.png](https://s1.ax1x.com/2022/08/14/vUi9Qx.png)


下载完成后，将其解压后拷贝至`/usr/local`目录下。

>Tips：如何进入`/usr/local`目录？
打开`Finder`，快捷键`command+shift+G`，弹出弹窗，输入`/usr/local`，回车即可

### 2.配置环境变量
- 打开终端窗口，输入命令：

``` bash
open -e ~/.bash_profile
```

- 弹出文本编辑窗口后，在`.bash_profile`中配置`MongoDB`的环境变量

```
export PATH=/usr/local/mongodb/bin:$PATH
```
![vUipS1.png](https://s1.ax1x.com/2022/08/14/vUipS1.png)
- 保存后关闭窗口，使用命令使配置生效

```bash
source ~/.bash_profile
```

- 使用命令测试配置是否成功

``` bash
mongod -version
```
![vUiCy6.png](https://s1.ax1x.com/2022/08/14/vUiCy6.png)
成功输出版本号，说明以上步骤成功完成。如果出现`command not found`，证明配置失败，解决办法见[Mac配置.bash_profile失效](http://localhost:4000/2019-12-30-mac-pei-zhi-bash-profile-chong-qi-zhong-duan-hou-pei-zhi-shi-xiao.html)。

### 3.运行MongoDB
- 在根目录下创建数据库文件夹和日志文件夹

``` bash
sudo mkdir -p /database/data
sudo mkdir -p /database/log
```

- 给新建的文件夹开放权限

``` bash
sudo chown YOUR_USERNAME /database/data
sudo chown YOUR_USERNAME /database/log
```

- 配置`mongod.conf`文件
在 `/usr/local/etc/` 目录下新建 `mongod.conf` 文件，将上面创建的文件夹配置成`MongoDB`的数据库路径和日志路径

``` text
systemLog:
    destination: file
    path: /database/log/mongod.log
storage:
    dbPath: ⁨/database/data
net:
    bindIp: 127.0.0.1
    port: 27017 
```

- 运行MongoDB

``` bash
mongod --config /usr/local/etc/mongod.conf
 
// 如果没有创建全局路径 PATH，需要进入以下目录
cd ~
cd /usr/local/xxx/bin   //xxx是mongodb安装包的文件夹名
sudo ./mongod
```

### 4.校验MongoDB服务
- 验证mongoDB服务是否已启动
新打开一个终端窗口，输入以下命令：

``` bash
ps aux | grep -v grep | grep mongod
```

会显示服务启动事件和启动方式等信息。

- 此时也可以打开浏览器，访问localhost:27017，会出现以下内容：

```
It looks like you are trying to access MongoDB over HTTP on the native driver port.
```

### 5.连接数据库
新打开一个终端，运行以下命令
``` bash
mongo

// 如果没有创建全局路径 PATH，需要进入以下目录
cd ~
cd /usr/local/xxx/bin   //xxx是mongodb安装包的文件夹名
sudo ./mongo
```

出现以下内容，证明成功连接到了数据库
![vUPzWR.png](https://s1.ax1x.com/2022/08/14/vUPzWR.png)


如果出现`command not found`，证明配置失败，解决办法见[Mac配置.bash_profile失效](http://localhost:4000/2019-12-30-mac-pei-zhi-bash-profile-chong-qi-zhong-duan-hou-pei-zhi-shi-xiao.html)。

### 6.断开与数据库的连接及关闭MongoDB服务
在连接数据库和启动MongoDB服务的终端窗口中按下`Ctrl+C`即可断开数据库连接及关闭MongoDB服务。

 