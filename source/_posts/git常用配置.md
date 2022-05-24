---
title: git常用配置
date: 2021-12-15 16:58:06
summary: 日常开发中git常用配置
tags:
- Git
- SSH
categories:
- [项目开发]
---
### 一、设置用户名和邮箱
全局设置：
```bash
$  git config --global user.name  "xxxx"

$  git config --global user.email  "xxxx"
```

项目单独设置：
```bash
$  git config user.name  "xxxx"

$  git config user.email  "xxxx"
```

查看全局配置：
```bash
$  git config --list
```

查看当前项目配置：
```bash
方法一：
$  git config --local --list

方法二（项目根目录下）：
$ cd .git
$ cat config

方法三：
$ git config user.name 
$ git config user.email
```

### 二、配置多个SSH秘钥
在公司电脑上，有时需要同时访问公司私有gitlab和个人github仓库，用到的ssh秘钥不同，需要以下简单配置。  
1. 利用公司邮箱生成的ssh密钥对`id_rsa`及`id_rsa.pub`
2. 利用个人邮箱生成ssh密钥对`id_rsa.github`及`id_rsa.github.pub`
3. 将上述密钥对的公钥`id_rsa.pub`及`id_rsa.github.pub`分别配置到`gitlab`及`github`平台上。
4. 进入`~/.ssh/`目录，执行`touch config`创建`config`文件，执行`vim config`，添加如下内容：
``` bash
Host *github.com
    User ### // 用户名
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa.github
    ServerAliveInterval 300
    ServerAliveCountMax 10

```
按下`ESC`，输入`:wq`，（保存并退出）即可。