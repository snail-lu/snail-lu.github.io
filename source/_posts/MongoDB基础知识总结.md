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
use <database>            # 进入到指定的数据库中/创建不存在的数据库
db                        # 查看当前所处的数据库
show collections          # 显示当前数据库中的所有集合
```
### CRUD指令
> query指查询条件，documnent指文档。

- 插入文档
```bash
db.<collection>.insert()                                # 插入一个或多个文档
db.<collection>.insertOne(document)                     # 插入一个文档      
db.<collection>.insertMany([document1, document2, ...]) # 插入多个文档

# 示例：
db.users.insert({ name: 'admin', age: 16 })
db.users.insertOne({ name: 'admin', age: 16 })
db.users.insertMany([{ name: 'admin', age: 16 }, { name: 'admin2', age: 30 }])
```

- 查询文档
```bash
db.<collection>.find()               # 查询所有文档
db.<collection>.find(query)          # 查询符合查询条件的文档集合
db.<collection>.findOne(query)       # 查询符合查询条件的第一个文档
db.<collection>.find(query).count()  # 查询文档数量

# 示例：
db.users.find()
db.users.find({ age: 16 })
db.users.findOne({ age: 16 })
```

- 修改
```bash
db.<collection>.update(query, document)                # 替换文档
db.<collection>.update(query, { $set：newDocument })   # 修改文档
db.<collection>.updateMany(filter, update, options)    # 修改多个文档
db.<collection>.updateOne(filter, update, options)     # 修改一个文档

# 示例：
db.users.update({ name: 'mike' }, { name: 'Jack' })    # 查找到的文档会被{ name:'Jack' }替换
db.users.update({ name: 'mike', { $set: { age: 17 }}}) # 查到的第一个文档age值会被修改为17
```

- 删除
```bash
db.users.remove({})                         # 删除集合中所有文档
db.<collection>.remove(query, options)      # 删除符合条件的文档
db.<collection>.deleteOne(filter, options)  # 删除一个文档
db.<collection>.deleteMany(filter, options) # 删除多个文档
db.<collection>.drop()                      # 删除集合
db.dropDatabase()                           # 删除数据库
# 示例：
db.users.remove({ name: 'Mike' })
db.users.deleteOne()
```

### 操作符
|操作符|含义|
|----|----|
|$gt|>|
|$gte|>=|
|$lt|<|
|$lte|<=|
|$eq|=|
|$ne|!=|