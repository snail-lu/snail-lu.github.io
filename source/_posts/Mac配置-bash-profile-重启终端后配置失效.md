---
title: 'Mac配置.bash_profile,重启终端后配置失效'
date: 2019-12-30 13:31:09
summary: Mac中配置bash_profile无效解决。
tags:
- Mac
- zsh
- bash
categories:
- [随笔]
- [开发配置]
---

### 1.场景
在Mac中使用mongodb，一般都要在.bash_profile中配置一下环境变量，后面使用会比较方便一点。  
遇到的问题状况是：配置.bash_profile后，source了一下，然后启动了mongodb，这时又新建了一个终端窗口去连接数据库，去执行mongo命令，提示:
![](https://github.com/Snail-Lu/imageGalleries/blob/main/gh-pages/2021-10/mac-bash/1610029-20191210130742610-1488085198.png?raw=true)

### 2.原因
使用的终端是`zsh`，zsh加载的是` ~/.zshrc`文件，而`.zshrc`文件中并没有定义任务环境变量

### 3.解决
1. 终端中执行命令，打开`.zshrc`文件：

``` bash
open -e ~/.zshrc
```

2. 在`~/.zshrc`文件最后，增加一行：

```
source ~/.bash_profile
```
3. 执行命令，使其立即生效

``` bash
source ~/.zshrc 
```

4、新建窗口，测试：

![](https://github.com/Snail-Lu/imageGalleries/blob/main/gh-pages/2021-10/mac-bash/1610029-20191210132653687-1027904723.png?raw=true)

PS:
> profile（/etc/profile），用于设置系统级的环境变量和启动程序，在这个文件下配置会对所有用户生效。当用户登录（login）时，文件会被执行，并从/etc/profile.d目录的配置文件中查找shell设置。
一般不建议在/etc/profile文件中添加环境变量，因为在这个文件中添加的设置会对所有用户起作用。
bash_profile只有单一用户有效，文件存储位于~/.bash_profile，该文件是一个用户级的设置，可以理解为某一个用户的profile目录下。这个文件同样也可以用于配置环境变量和启动程序，但只针对单个用户有效。
和profile文件类似，bash_profile也会在用户登录（login）时生效，也可以用于设置环境变理。但与profile不同，bash_profile只会对当前用户生效。
 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 