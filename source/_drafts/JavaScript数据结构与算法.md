---
title: JavaScript数据结构与算法
tags: 
- 数据结构
- 算法
categories:
- [读书笔记]
- [前端进阶]
---

### 一、数据结构部分
#### 1. 栈
**规则**：后进先出（LIFO）
**实现**：
```js
/**
 * Stack类
 * items - 栈元素集合
 * push() - 栈顶增加元素方法
 * pop() - 栈顶移除元素方法
 * peek() - 返回栈顶元素方法
 * isEmpty() - 空栈判断方法
 * clear() - 清空栈集合方法
 * size() - 返回栈元素数量方法
 */
class Stack {
    constructor() {
        this.items = []
    }

    push(item) {
        this.items = [...this.items, item]
    }

    pop() {
        const size = this.size()
        if(size === 0) {
            return null
        } else {
            return this.items[size-1]
        }
    }

    peek() {
        
    }
}
```
#### 2. 队列
#### 3. 链表
#### 4. 集合
#### 5. 字典和散列表
#### 6. 树
#### 7. 二叉堆
#### 8. 图


### 二、算法部分 
