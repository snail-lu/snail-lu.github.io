---
title: MongoDB基础知识总结
date: 2022-03-27 15:40:07
summary: MongoDB数据库基础知识
tags:
- Node.js
- MongoDB
categories:
- [数据库]
- [读书笔记]
---

### 一、数据库分类
- 关系型数据库（RDBMS）
  - MySQL、Oracle、DB2、SQL Server
  - ...
- 非关系型数据库（NoSQL)
  - 键值对数据库: Redis
  - 文档数据库: MongoDB

### 二、安装MongoDB
  - 数据库软件构成
    - 数据库的服务器
      - 数据库的服务器用来存放数据
      - `mongod` 用来启动服务器
    - 数据库的客户端
      - 客户端用来操作服务器，对数据进行增删改查的操作
      - `mongo` 用来启动客户端
- 下载[msi安装包](https://www.mongodb.com/try/download/community?tck=docs_server)
- 打开安装包进行安装
- 配置mongoDB安装路径到环境变量中 `C:\Program Files\MongoDB\Server\4.0\bin`
- 在C盘根目录创建一个`data`文件夹，在`data`中创建`db`文件夹，用于存放数据库文件
- 打开命令行工具，输入`mongod`，启动`mongoDB`服务器
  - 如果想要更改数据库默认存放目录及端口，使用命令`mongod --dbpath 路径  --port 端口`
- 再打开一个命令行窗口，输入`mongo`，出现`>`，表示成功连接mongoDB服务器
- 设为系统服务：开机自启，后台自动运行（4.0版本以后安装MongoDB的时候就可以开启此功能）（参见[mongodb官网资料](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)）



### 三、相关概念
- `database` 数据库
  数据库是一个仓库，在仓库中可以存放集合

  **命名规范**：
  * UTF-8字符
  * 不能是空字符串
  * 不能含有空格、`.`、`$`、`/`、`\`和`\0`（空字符）
  * 全部小写
  * 最多64Byte。
  
  **保留的数据库名**：
  * **admin**：root数据库
  * **local**：用于存储限于本地单台服务器的任意集合
  * **config**：用于保存分片相关的信息

- `collection` 集合
  类似于数组，对应SQL中的`table`，在集合中可以存放文档
  **命名规范**：
  * 不能是空字符串
  * 不能包含`\0`（空字符）
  * 不能以`system.`开头
  * 不能包含`$`
- `document` 文档
  是MongoDB数据库中最小单位，我们存储和操作的内容都是文档。文档是一组键值对，采用BSON格式存储（[BSON](https://blog.csdn.net/m0_38110132/article/details/77716792 "BSON的介绍及BSON与JSON的区别")，一种类json的二进制形式的存储格式。）  
  **注意**：
  1. 文档中的键值对是有序的，例如`{"username":"test","password":"123"}`和`{"password":"123","username":"test"}`是不同的文档。
  2. 文档的键是字符串，值可以是字符串，也可以是其它几种[数据类型](https://docs.mongodb.com/manual/reference/bson-types/)。
  3. 区分类型和大小写。
  4. 键不能重复。

  **命名规范**：
  * 不能含有`\0`（空字符）
  * `.`和`$`有特别的意义，只有在特定环境下使用
  * 以`_`开头的键是保留的

### 四、基本指令
启动`mongo`客户端
```bash
show dbs/databases        # 显示当前的所有数据库
use <database>            # 进入到指定的数据库中/创建不存在的数据库
db                        # 查看当前所处的数据库
show collections          # 显示当前数据库中的所有集合

db.dropDatabase()         # 删除数据库
```
### 五、CRUD指令
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
db.<collection>.find()                                                 # 查询所有文档
db.<collection>.find(query)                                            # 查询符合查询条件的文档集合
db.<collection>.findOne(query)                                         # 查询符合查询条件的第一个文档
db.<collection>.find(query).count()                                    # 查询文档数量
db.<collection>.find(query).limit(pageSize)                            # 限制查询数量
db.<collection>.find(query).skip([pageNum-1]*pageSize).limit(pageSize) # 查询分页

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

### 六、操作符
- 比较操作符

|操作符|含义|
|:----:|:----:|
|$gt|>|
|$gte|>=|
|$lt|<|
|$lte|<=|
|$eq|=|
|$ne|!=|
|$type|数据类型|

示例：
```bash
db.users.find({ age: { $gt: 20 }}) # 查询age>20的文档数据
db.users.find({ age: { $gt: 20, $lt: 40 }}) # 查询20<age<40的文档
db.blogs.find({"title": {$type: 'string'}}) # 查找title属性的值为string类型的文档
```

### 七、文档间的关系
- 一对一
- 一对多
- 多对多

### 八、文档排序
```bash
# sort()可以用来指定文档的排序规则 
# 参数为{ <field>: 1|-1 }
# <field>为要排序的字段，1表示升序 -1表示降序
db.users.find({}).sort({ age: 1 }) # users集合中的文档按照age升序返回
```

### 九、限制查询的返回字段
默认情况下，MongoDB中的查询返回匹配文档中的所有字段。可以在查询中来配置指定货限制返回的字段。
```bash
db.inventory.find( { status: "A" }, { item: 1, status: 1 } ) # 查找status为A的文档，返回的查询结果仅包含item,status,_id三个字段
db.inventory.find( { status: "A" }, { item: 1, status: 1, _id: 0 } ) # 查找status为A的文档，返回的查询结果仅包含item,status两个字段
```

### 十、使用mongoose连接mongoDB
```js
// 引入
const mongoose = require('mongoose');

// 连接数据库
mongoose.connect("mongodb://localhost:27017/adminclient");

// 监听连接状态
const connection = mongoose.connection;
connection.once('open', function() {
    console.log('数据库连接成功');
})
connection.once('close', function() {
    console.log('数据库连接断开');
})

// 断开数据库连接(一般无需使用)
mongoose.disconnect()

// 定义Schema（集合中的文档结构）
const Schema = mongoose.Schema;

const blogSchema = new Schema({
    title:  String,
    author: String,
    body:   String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
      votes: Number,
      favs:  Number
    }
});

// 创建Model（文档集合），以便后续来操作集合
const BlogModel = mongoose.model('blog', blogSchema)

// 使用BlogModel来进行CRUD
BlogModel.create({
    title: '第一篇博客',
    author: 'Snail-Lu',
    body: '这是我的第一篇博客',
    comments: [{ body: '写的不错', date: '2022/4/1 22:23:14' }],
    date: '2022-4-1 22:22:22',
    hidden: false,
    meta: {
      votes: 10,
      favs:  10
    }
}, function(err) {
    if(!err) {
        console.log('插入成功')
    }
}) 

BlogModel.findOne({ title: '第一篇博客' }, function(err, doc) {
    if(!err) {
        // 利用document方法进行文档修改
        doc.$set({ hidden: true })
        doc.save()
    }
})
```

### 参考资料
1. [菜鸟MongoDB教程](https://www.runoob.com/mongodb/mongodb-tutorial.html)
2. [MongoDB官方文档](https://www.mongodb.com/docs/manual/tutorial/getting-started/)