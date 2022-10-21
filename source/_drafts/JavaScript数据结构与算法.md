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
**实现1**：使用数组实现栈数据结构
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
        this.items.push(item);
    }

    pop() {
        return this.items.pop();
    }

    peek() {
        if (this.items.length === 0) {
            return undefined;
        }
        return this.items[this.items.length - 1];
    }

    isEmpty() {
        return this.items.length === 0;
    }

    clear() {
        this.items = [];
    }

    size() {
        return this.items.length;
    }
}
```
**实现2**：使用对象实现栈数据结构
```js
/**
 * Stack类
 * items - 栈元素集合
 * count - 栈元素数量
 * push() - 栈顶增加元素方法
 * pop() - 栈顶移除元素方法
 * peek() - 返回栈顶元素方法
 * isEmpty() - 空栈判断方法
 * clear() - 清空栈集合方法
 * size() - 返回栈元素数量方法
 */
class Stack {
    constructor() {
        this.items = {};
        this.count = 0;
    }

    push(item) {
        this.items[this.count] = item;
        this.count++;
    }

    pop() {
        if (this.count === 0) {
            return null;
        }
        this.count--;
        const last = this.items[this.count];
        delete this.items[this.count];
        return last;
    }

    peek() {
        if (this.count === 0) {
            return undefined;
        }
        return this.items[this.count - 1];
    }

    isEmpty() {
        return this.count === 0;
    }

    clear() {
        this.items = {};
        this.count = 0;
    }

    size() {
        return this.count;
    }
}
```

#### 2. 队列
**规则**：先进先出（FIFO）
**实现1**：实现队列数据结构
```js
/**
 * Queue类
 * items - 队列元素集合
 * count - 队列尾部元素下标
 * lowestCount - 队列头部元素下标
 * enqueue() - 队列尾部增加元素方法
 * dequeue() - 移除队列头部元素方法
 * peek() - 返回队列头部元素方法
 * isEmpty() - 空队列判断方法
 * clear() - 清空队列方法
 * size() - 返回队列元素数量方法
 */
class Queue {
    constructor() {
        this.items = {};
        this.lowestCount = 0;
        this.count = 0;
    }

    enqueue(item) {
        this.items[this.count] = item;
        this.count++;
    }

    dequeue() {
        const queueSize = this.size()
        if (queueSize === 0) {
            return undefined;
        } else if (queueSize === 1) {
            const headElement = this.items[this.count];
            delete this.items[this.count];
            this.lowestCount = 0;
            this.count = 0;
            return headElement;
        } else if (this.lowestCount < this.count) {
            const headElement = this.items[this.lowestCount];
            delete this.items[this.lowestCount];
            this.lowestCount++;
            return headElement;
        } 
    }

    peek() {
        if (this.count === 0) {
            return undefined;
        }
        return this.items[this.lowestCount];
    }

    isEmpty() {
        return this.count === 0;
    }

    clear() {
        this.items = {};
        this.count = 0;
        this.lowestCount = 0;
    }

    size() {
        return this.count - this.lowestCount;
    }
}
```
#### 3. 链表
#### 4. 集合
#### 5. 字典和散列表
#### 6. 树
#### 7. 二叉堆
#### 8. 图


### 二、算法部分 
