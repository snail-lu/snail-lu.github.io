---
title: MongoDB简单实践
tags:
---

### 一、基本概念  

SQL概念 | MongoDB概念 | 含义(用/区分SQL和MongoDB)
---|---|---
database | database | 数据库
table | collection | 数据库表/集合
row | document | 数据记录行/文档
column | field | 数据字段/域
index | index | 索引
table joins |  | 表链接/不支持
primary | primary key | 主键（mongoDB中自动设置_id为主键）

### 二、概念解析
#### 1.数据库
MongoDB的单个实例可以容纳多个独立的数据库，常用命令有：  
```
//显示数据库列表
show dbs

//连接到数据库testdb
use testdb

//显示当前连接到的数据库
db


```

数据库的命名规范：
* UTF-8字符
* 不能是空字符串
* 不能含有空格、.、$、/、\和\0（空字符）
* 全部小写
* 最多64Byte。

保留的数据库名：
* **admin**：root数据库
* **local**：用于存储限于本地单台服务器的任意集合
* **config**：用于保存分片相关的信息


#### 2.集合
集合就是一组文档，可以在集合中插入文档。
集合的命名规范：
* 不能是空字符串
* 不能包含\0（空字符）
* 不能以“system.”开头
* 不能包含$

#### 3.文档
文档是一组键值对，采用BSON格式存储（[BSON](https://blog.csdn.net/m0_38110132/article/details/77716792 "BSON的介绍及BSON与JSON的区别")，一种类json的二进制形式的存储格式。）  
注意：
1. 文档中的键值对是有序的，例如{"username":"test","password":"123"}和{"password":"123","username":"test"}是不同的文档。
2. 文档的键是字符串，值可以是字符串，也可以是其它几种[数据类型](https://docs.mongodb.com/manual/reference/bson-types/)。
3. 区分类型和大小写。
4. 键不能重复。

文档的键名命名规范：
* 不能含有\0（空字符）
* .和$有特别的意义，只有在特定环境下使用
* 以"_"开头的键是保留的

### 三、数据类型
数据类型 | 描述
---- | ----
String | 字符串,UTF-8编码的字符串才是合法的
Integer | 整型数值
Boolean | 布尔值
Double | 双精度浮点值
Min/Max keys | 将一个值与BSON元素的最低值和最高值相对比
Array | 数组
Timestamp | 时间戳，64位的值，前32位是一个time_t值，后32位是在某秒钟操作的一个递增的序数
Object | 内嵌文档
Null | 空值
Symbol | 符号
Date | 日期时间用UNIX时间格式来存储当前日期或时间
Object ID | 对象ID
Binary Data | 二进制数据
Code | 代码类型
Regular expression | 正则表达式类型

### 四、基本语法
#### 创建数据库
```
use testdb
```

#### 删除数据库
``` bash
// 先查看数据库列表
show dbs 

// 再连接要删除的数据库
use testdb

// 删除该数据库
db.dropDatabase()
```

#### 创建集合
``` bash
db.createCollection(name,options)
```
参数：  
* name：要创建的集合名称
* options：可选参数，指定内存大小及索引  

> Tip: MongoDB中不需要创建集合，当插入文档时，会自动创建集合。

#### 查看集合列表 
```
show collections
```

#### 删除集合

```
db.COLLECTION_NAME.drop()
```
> 注：COLLECTION_NAME为要删除的集合名称

#### 插入文档
 
```
db.COLLECTION_NAME.insertOne(document)
db.COLLECTION_NAME.inserMany(document)
```

#### 更新文档 
```
db.COLLECTION_NAME.updateOne()
db.COLLECTION_NAME.updateMany()
db.COLLECTION_NAME.replaceOne()
```

#### 删除文档
  
```
db.COLLECTION_NAME.deleteMany()
db.COLLECTION_NAME.deleteOne()
```

#### 查找文档
  
```
db.COLLECTION_NAME.find()
```

#### 条件操作符
* $gt   ---  大于
* $lt   ---  小于
* $gte  ---  大于等于
* $lte  ---  小于等于
* $ne   ---  不等于
* $eq   ---  等于
* $type ---  数据类型
示例：  
```
// 在blogs集合中查找likes属性值大于100的文档
db.blogs.find({"likes": {$gt: 100}});

// 查找title属性的值为string类型的文档
db.blogs.find({"title": {$type: 'string'}})
```

#### Limit和Skip  
```
//最多返回NUMBER个查找结果
db.COLLECTION_NAME.find().limit(NUMBER)

//从符合查找条件的所有结果中，跳过NUMBER2个文档，返回NUMBER1个文档
db.COLLECTION_NAME.find().limit(NUMBER1).skip(NUMBER2)
```

#### 排序
 
```
//按照KEY的值对查找结果进行排序，1为升序，-1为降序
db.COLLECTION_NAME.find().sort({KEY: 1})
```

#### 索引 
```
// keys为索引参数对象，键为要创建的索引字段，键值设为1为升序，-1为降序
// options为可选参数，可取值有background/name/unique等，详见mongoDB文档
db.COLLECTION_NAME.createIndex(keys, options)

// 示例
// 1为升序，-1为降序，options
db.values.createIndex({open: 1, close: 1}, {background: true})
```

#### 聚合/管道
```
db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)

// 示例
db.articles.aggregate([
    { $match : { score : { $gt : 70, $lte : 90 } } },
    { $group: { _id: null, count: { $sum: 1 } } }
]);
```

常用的几个操作：  
* $project: 修改输入文档的结构。
* $match: 过滤数据。
* $limit: 限制返回的文档数量。
* $unwind: 将文档中某一个数组类型字段拆分成多条，每条包含数组中的一个值。
* $group: 将集合中的文档分组。
* $sort: 文档排序。
* $geoNear: 输出接近某一地理位置的有序文档。

常用的几个聚合表达式： 
* $sum: 计算总和
* $avg: 计算平均值
* $min: 最小值
* $max: 最大值
* $push: 在结果文档中插入一个值到数组中
* $addToSet: 在结果文档中插入一个值到数组中，不创建副本
* $first: 第一个文档
* $last: 最后一个文档


#### 复制（副本集） 
mongoDB的复制至少需要两个节点，其中一个是主节点，负责处理客户端需求，其余的都是从节点，负责复制主节点上的数据。     
MongoDB各个节点常见的搭配方式为：一主一从、一主多从。  
主节点记录在其上的所有操作oplog，从节点定期轮询主节点获取这些操作，然后对自己的数据副本执行这些操作，从而保证从节点的数据与主节点一致。    

副本集特征：  
* N个节点的集群
* 任何节点可作为主节点
* 所有写入操作都在主节点上
* 自动故障转移
* 自动恢复

常用命令：  
```
//启动一个新的副本集
rs.initiate()

//查看副本集的配置
rs.conf()

//查看副本集状态
rs.status()

//副本集添加成员
rs.add(HOST_NAME:PORT)
```

#### 分片
通过在多台机器上分割数据，使得数据库能存储和处理更多的数据。


#### 备份与恢复
语法：  
```
//备份
mongodump -h hostname:port -d dbname -o dbdirectory

//恢复
mongorestore -h hostname:port -d dbname dbdirectory
```
* -h: 服务器地址
* -d: 数据库名称
* -o: 备份的数据库存放地址


#### 监控
使用mongostat和mongotop两个命令来监控MongoDB的运行情况。  
```
//进入MongoDB安装目录下的bin目录下，运行
mongostat

mongotop <sleeptime>
mongotop --locks
```

### 六、高级教程
#### 文档之间的关系
* 嵌入式关系
* 引用式关系
```
//user文档
{
   "_id":ObjectId("52ffc33cd85242f436000001"),
   "name": "Tom Hanks",
   "contact": "987654321",
   "dob": "01-01-1991"
}

//address文档
{
   "_id":ObjectId("52ffc4a5d85242602e000000"),
   "building": "22 A, Indiana Apt",
   "pincode": 123456,
   "city": "Los Angeles",
   "state": "California"
} 

//嵌入式关系
{
   "_id":ObjectId("52ffc33cd85242f436000001"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin",
   "address": [
      {
         "building": "22 A, Indiana Apt",
         "pincode": 123456,
         "city": "Los Angeles",
         "state": "California"
      },
      {
         "building": "170 A, Acropolis Apt",
         "pincode": 456789,
         "city": "Chicago",
         "state": "Illinois"
      }]
}

//引用式关系
{
   "_id":ObjectId("52ffc33cd85242f436000001"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin",
   "address_ids": [
      ObjectId("52ffc4a5d85242602e000000"),
      ObjectId("52ffc4a5d85242602e000001")
   ]
}
```

#### DBRefs引用
当从不同集合中引用文档时，应该使用DBRefs引用。
语法：  
```
{ 
    $ref: COLLECTION_NAME,
    $id:  DOCUMENT_ID,
    $db: DATABASE_NAME(可选)
}
```
示例：  
```
//引用的地址文档在testdb数据库下的addredd_home集合，id为534....000。
{
   "_id":ObjectId("53402597d852426020000002"),
   "address": {
   "$ref": "address_home",
   "$id": ObjectId("534009e4d852427820000002"),
   "$db": "testdb"
   },
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin"
}
```

#### 覆盖索引查询
覆盖查询是以下的查询：  
* 所有的查询字段是索引的一部分
* 所有的查询返回字段在同一个索引中  

由于所有出现在查询中的字段是索引的一部分，MongoDB无需再整个数据文档中检索匹配查询条件和返回使用相同索引的查询结果。因为索引存在于RAM中，从索引中获取数据比通过扫描文档读取数据要快得多。  

#### 查询分析
explain()和hint()

#### 原子操作 
不会被线程调度机打断的操作。例如MongoDB保存文档，要么成功保存到了MongoDB,要么没有保存到MongoDB,不会出现没有保存完整的情况。  
常用命令：  
```
//指定一个键并更新键值
$set
//示例：
{ $set: { field: value } }

//删除一个键
$unset
//示例：
{ $unset: { field: 1 } }

//增减操作
$inc
//示例
{ $inc: { field: value } }

//数组push
$push
//示例：
{ $push: { field: value } }

//数组push多个值
$pushAll
//示例：
{ $pushAll: { filed: values_array } }

//数组删除某一个等于value的值
$pull
//示例：
{ $pull: { field: value } }

//增加一个值到数组，数组中不存在这个值才会增加
$addToSet
//示例：
{ $addToSet: { field: value } }

//删除数组的第一个或最后一个元素
$pop
//示例：
{ $pop: { field: 1 } }

//重命名
$rename
//示例：
{ $rename: { old_field_name: new_field_name } }

//位操作
$bit
//示例：
{ $bit: { field: { and: 5 } } }

//偏移操作符

```
