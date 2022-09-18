---
title: Nest.js基础知识总结
date: 2021-02-03 10:56:00
summary: Nest.js基础知识总结
tags:
- Nest.js
- Node.js
categories:
- [读书笔记]
- [前端进阶]
---

NestJS 的设计模式，主要就是 `Controller`、`Service`、`Module` 共同努力，形成了一个模块。

- `Controller`：传统意义上的控制器，提供 api 接口，负责处理路由、中转、验证等一些简洁的业务；
- `Service`：又称为 `Provider`， 是一系列服务、repo、工厂方法、helper 的总称，主要负责处理具体的业务，如数据库的增删改查、事务、并发等逻辑代码；
- `Module`：负责将 `Controller` 和 `Service` 连接起来，类似于 `namespace` 的概念；

### 1. Service

使用nest-cli提供的指令可以快速创建文件：

```bash
nest g [文件类型] [文件名] [文件目录（src目录下）]
```

示例：

```bash
nest g service user server
```

此命令在src目录下创建了`server/user`文件夹，其中包含两个文件`user.service.spec.ts`（不用管）和`user.service.ts`。

后面的`user`相关的业务基本都在`user.service.ts`中实现。

```typescript
// src/server/user/user.service.ts
import { Injectable } from '@nestjs/common';

@Injectable()
export class UserService {}
```

### 2. Controller

使用指令创建控制器文件：

```bash
nest g controller user server
```

此命令会在`server/user`文件夹下创建`user.controller.spec.ts`(不用管)和`user.controller.ts`两个文件。

```typescript
// src/server/user/user.controller.ts
import { Controller } from '@nestjs/common';

@Controller('user')
export class UserController {}
```

### 3. 管道

> 数据传输对象（DTO：Data Transfer Object），是一种设计模式之间传输数据的软件应用系统。数据传输目标往往是数据访问对象从数据库中检索数据。数据传输对象与数据交互对象或数据访问对象之间的差异是一个以不具有任何行为除了存储和检索的数据（访问和存取器）。

管道有两个类型：

- 转换：管道将输入数据转换为所需的数据输出；
- 验证：对输入数据进行验证，如果验证成功继续传递，验证失败则抛出异常；

`Nest`自带三个开箱即用的管道：`ValidationPipe`、`ParseIntPipe`、`ParseUUIDPipe`。

### 4. 中间件

中间件是在路由处理程序之前调用的函数。

中间件函数可以执行以下任务：

- 执行任何代码。
- 对请求和响应对象进行更改。
- 结束请求-响应周期。
- 调用堆栈中的下一个中间件函数。
- 如果当前的中间件函数没有结束请求-响应周期，它必须调用`next()`将控制传递给下一个中间件函数。否则，请求将被挂起。

### 5. 异常过滤器

内置的`异常层`负责处理整个应用程序中的所有抛出的异常。

### 6. 管道

管道有两个类型：

- 转换：管道将输入数据转换为所需的数据输出
- 验证：对输入数据进行验证，如果验证成功继续传递，验证失败则抛出异常。

管道`参数（arguments）`会由控制器的路由处理程序进行处理。Nest会在调用这个方法之前插入一个管道，管道会先拦截方法的调用参数，进行转换或是验证处理，然后用转换好或是验证好的参数调用原方法。

`Nest`内置6个开箱即用的管道：

- ValidationPipe
- ParseIntPipe
- ParseBoolPipe
- ParseArrayPipe
- ParseUUIDPipe
- DefaultValuePipe

### 7. 守卫

### 8. 拦截器

- 在函数执行之前/之后绑定额外的逻辑
- 转换从函数返回的结果
- 转换从函数抛出的异常
- 扩展基本函数行为
- 根据所选条件完全重写函数

### 文档地址

- [布拉德特皮.NestJS实战教程.掘金.](https://juejin.cn/collection/6845244185432293389)
- [Nestjs中文文档](https://docs.nestjs.cn/)
- [TypeORM中文文档](https://typeorm.biunav.com/zh/)















