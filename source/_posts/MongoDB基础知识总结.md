---
title: MongoDB基础知识总结
date: 2022-03-27 15:40:07
tags:
- Node.js
- MongoDB
categories:
- [数据库]
- [读书笔记]
---

### 数据库分类
- 关系型数据库（RDBMS）
  - MySQL、Oracle、DB2、SQL Server
  - ...
- 非关系型数据库（NoSQL)
  - 键值对数据库: Redis
  - 文档数据库: MongoDB

### 安装MongoDB
  - 数据库软件构成
    - 数据库的服务器
      - 数据库的服务器用来存放数据
      - `mongod` 用来启动服务器
    - 数据库的客户端
      - 客户端用来操作服务器，对数据进行增删改查的操作
      - `mongo` 用来启动客户端
- 下载msi安装包
- 打开安装包进行安装
- 配置mongoDB安装路径到环境变量中 `C:\Program Files\MongoDB\Server\4.0\bin`
- 在C盘根目录创建一个`data`文件夹，在`data`中创建`db`文件夹，用于存放数据库文件
- 打开命令行工具，输入`mongod`，启动`mongoDB`服务器
  - 如果想要更改数据库默认存放目录及端口，使用命令`mongod --dbpath 路径  --port 端口`
- 再打开一个命令行窗口，输入`mongo`，出现`>`，表示成功连接mongoDB服务器
- 设为系统服务：开机自启，后台自动运行(4.0版本以后安装MongoDB的时候就可以开启此功能)（参见[mongodb官网资料](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)）



### 相关概念
- 数据库（database）
  数据库是一个仓库，在仓库中可以存放集合
- 集合（collection）
  集合类似于数组，在集合中可以存放文档
- 文档（document)
  是数据库中最小单位，我们存储和操作的内容都是文档

### 基本指令
```bash
show dbs/databases        # 显示当前的所有数据库
use 数据库名               # 进入到指定的数据库中/创建不存在的数据库
db                        # 查看当前所处的数据库
show collections          # 显示当前数据库中的所有集合
```
### CRUD指令
- 插入文档
```bash
db.<collection>.insert()  
db.<collection>.insertOne()  # 插入一个文档      
db.<collection>.insertMany() # 插入多个文档
```

- 查询文档
```bash
db.<collection>.find()                  # 查询所有文档
db.<collection>.find(查询条件)          # 查询符合查询条件的文档
db.<collection>.find(查询条件).count()  # 查询文档数量
```

- 修改
```bash
db.<collection>.update(查询条件,新文档)           # 替换文档
db.<collection>.update(查询条件，{$set：新文档})  # 修改文档
db.<collection>.updateMany()  
db.<collection>.updateOne()
```

- 删除
```bash
db.<collection>.remove()
db.<collection>.deleteOne()
db.<collection>.deleteMany()
```