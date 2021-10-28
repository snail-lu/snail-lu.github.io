---
title: npm常用命令总结
date: 2020-07-20 21:25:17
summary: npm常用命令总结。
tags:
- npm
- Node
categories:
- [随笔]
---

### 查看npm版本  
``` bash
npm -v
```

### 查看帮助
``` bash
# 显示npm的所有命令
npm help

# 显示npm所有命令使用语法
npm -l

# 显示指定命令的使用语法
npm <command> -h
```

### 更新npm

```bash
npm install npm@latest -g
```

### 创建包描述文件

``` bash
npm init 

# 创建默认的package.json
npm init --yes/-y
```

### 安装包

``` bash
# 安装当前package.json文件中所列的依赖包
npm install 

# 安装包（不会修改package.json）
npm install <package_name>

# 安装生产环境的包（会添加依赖信息到package.json中的dependencies）
npm install <package_name> --save/-S/--save-prod/-P

# 安装开发环境和测试环境依赖包（会添加依赖信息到package.json中的devDependencies）
npm install <package_name> --save-dev/-D

# 安装全局包（安装后可以在任意目录下使用这个包）
npm install <package_name> --global/-g

# 安装某一版本的包
npm install <package_name>@<version>

```

### 卸载包

``` bash
# 从node_modules目录下删除指定的包（不会修改package.json）
npm uninstall <package_name>

# 卸载指定的包并在package.json中删除依赖
npm uninstall --save/--save-dev <package_name>

# 卸载全局安装的包
npm uninstall -g <package_name>

```

### 更新包

``` bash
# 更新生产环境依赖包
npm update <package_name> --save

# 更新开发环境依赖包
npm update <package_name> --save-dev

# 更新全局安装的包
npm update <package_name> -g
```  

### 镜像地址

```bash
# 查看当前镜像地址
npm get registry

# 修改镜像地址为淘宝镜像（原始镜像地址为：https:#registry.npmjs.org/）
npm config set registry https:#registry.npm.taobao.org/

```

### 发布npm包

``` bash
# 创建用户（或者直接去官网注册）
npm adduser

# 登录
npm login

# 显示当前登录的用户名
npm whoami

# 查看版本
npm version 

# 更新版本号
npm version <newversion>

# 发布
npm publish
```

### npm脚本

在`package.json`中`scripts`字段中可以自定义npm脚本命令

``` json
{
    ...
    "scripts": {
        "build": "node index.js"
    }
}
```
使用``npm run``命令可以执行自定义命令  

```bash
npm run build

# 查看当前项目的所有npm脚本命令
npm run
```


### 参考文档：  

[1. npm Docs](https:#docs.npmjs.com/cli/v6/commands)  
[2. npm中文文档](https:#www.npmjs.cn/)
