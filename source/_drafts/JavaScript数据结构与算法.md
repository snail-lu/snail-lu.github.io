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
**规则**：先进先出（FIFO），即只允许往队列尾部添加元素，从队列头部移除元素
**实现1**：队列
```js
/**
 * Queue类
 * items - 队列元素集合
 * count - 下一个队列尾部元素下标
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
        return this.count-this.lowestCount === 0;
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
**规则**：先进先出和后进先出，即允许从队列头部和尾部添加和移除元素的特殊队列
**实现1**：双端队列
```js
/**
 * Deque类
 * items - 队列元素集合
 * count - 下一个队列尾部元素下标
 * lowestCount - 队列头部元素下标
 * addFront() - 队列头部增加元素方法
 * addBack() - 队列尾部增加元素方法
 * removeFront() - 移除队列头部元素方法
 * removeBack() - 移除队列尾部元素方法
 * peekFront() - 返回队列头部元素方法
 * peekBack() - 返回队列尾部元素方法
 * isEmpty() - 空队列判断方法
 * clear() - 清空队列方法
 * size() - 返回队列元素数量方法
 */
class Deque {
    constructor() {
        this.items = {};
        this.lowestCount = 0;
        this.count = 0;
    }

    addFront(item) {
        if(this.isEmpty()) {
            this.addBack();
        } else if(this.lowestCount > 0) {
            this.lowestCount--;
            this.items[this.lowestCount] = item;
        } else {
            // 将已有的数据后移一个位置
            let i = this.count;
            while(i > 0) {
                this.items[i] = this.items[i-1];
                i--;
            }
            this.count++;
            this.lowestCount = 0;
            this.items[0] = item;
        }
    }

    addBack(item) {
        this.items[this.count] = item;
        this.count++;
    }

    removeFront() {
        const size = this.size()
        if (size === 0) {
            return undefined;
        } else if (size === 1) {
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

    removeBack() {
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

    peekFront() {
        if (this.count === 0) {
            return undefined;
        }
        return this.items[this.lowestCount];
    }

    peekBack() {
        if (this.count === 0) {
            return undefined;
        }
        return this.items[this.lowestCount];
    }

    isEmpty() {
        return this.count-this.lowestCount === 0;
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
![](https://res.weread.qq.com/wrepub/epub_26211966_96)
**规则**：链表存储有序的元素集合，每个元素由一个存储元素本身的节点和一个指向下一个元素的指针组成
```js
/**
 * LinkedList类
 * count - 元素数量
 * head - 首元素
 * push(element) - 向链表尾部增加元素
 * insert(element, index) - 向链表指定位置增加元素
 * getElementAt(index) - 返回链表指定位置元素
 * indexOf(element) - 返回元素在链表中的索引
 * remove(element) - 移除元素
 * removeAt(index) - 移除指定位置的元素
 * isEmpty() - 链表判空
 * size() - 返回链表元素数量
 */
class LinkedList {
    constructor() {
        this.count = 0;
        this.head = null;
    }

    push(element) {
        const node = new Node(element);
        if(this.head == null) {
            this.head = node;
        } else {
            let current = this.head;
            // 找到最后一个元素
            while(current.next != null) {
                current = current.next;
            }
            // 最后一个元素的next指向node
            current.next = node;
        }
        this.count++;
    }

    insert(element, index) {
        if (index>=0 && index<=this.count) { 
            const node = new Node(element);
            if (index === 0) { // 在第一个位置添加
                node.next = this.head; // node的next指向原来的首元素
                this.head = node; // head指向node
            } else {
                const previous = this.getElementAt(index -1); // 找到前一个元素
                node.next = previous.next; // 将node的next指向index位置的元素
                previous.next = node; // 前一个元素的next指向node
            }
            this.count++; // 更新链表的长度
            return true; // 插入成功则返回true
        }
        return false; // 越界,则返回false
    }

    getElementAt(index) {
        if (index>=0 && index<=this.count) {
            let idx = 0;
            let node = this.head;
            while (idx !== index) { // 找到特定下标的元素
                node = node.next;
                idx++;
            }
            return node.element;
        }
        return null // 越界
    }

    indexOf(element) {
        let current = this.head;
        let index = 0;
        while(current!=null && current.element!==element) { // 元素比对
            current = current.next;
            index++;
        }

        return index>=this.count ? -1 : index // 超出元素数量，表示未找到，返回-1
    }

    remove(element) {
        let current = this.head;
        let node = new Node(element);
        // 移除首元素
        if(current.element === node.element) {
            this.head = current.next;
            this.count--;
            return true
        } else {
            // 找到element元素
            let previous = null;
            while(current!=null && current.element!==node.element) {
                previous = current;
                current = current.next;
            }

            // 将previous的next指向下一个元素，从而达到删除current的目的
            if(current != null) {
                previous.next = current.next;
                this.count--;
                return true
            }
        }
        return false
    }

    removeAt(index) {
        let element = this.getElementAt(index)
        return this.remove(element)
    }

    isEmpty() {
        return this.count === 0;
    }

    size() {
        return this.count;
    }
}

/**
 * Node类 - 链表元素
 * element - 元素值
 * next - 下一个元素
 */
class Node {
    constructor(element) {
        this.element = element;
        this.next = null;
    }
}
```

#### 4. 集合
**规则**：元素无序且唯一
**实现**：实现集合数据结构
```js
/**
 * Set类
 * items - 元素集合
 * add(element) - 向集合中添加元素
 * delete(element) - 从集合中删除元素
 * has(element) - 判断集合中是否存在某个元素
 * clear() - 清空集合
 * size() - 返回集合的元素数量
 * values() - 返回一个包含集合中所有元素的数组
 */
class Set {
    constructor() {
        this.items = {}
    }

    add(element){
        if(!this.has(element)) {
            this.items[element] = element;
            return true;
        }
        return false;
    }

    delete(element) {
        if(this.has(element)) {
            delete this.items[element];
            return true;
        }
        return false; 
    }

    has(element){
        return this.items.hasOwnProperty(element)
    }

    clear(){
        this.items = {}
    }

    size(){
        return Object.keys(this.items).length;
    }

    values(){
        return Object.values(this.items);
    }
}
```

#### 5. 字典
**规则**：`[键,值]`的形式来存储元素
**实现**：实现字典数据结构
```js
/**
 * Dictionary类
 * table - 字典数据集合
 * toStrFn(variable) - 将任意类型的值转换为字符串类型
 * set(key, value) - 向字典中添加新元素
 * remove(key) - 从字典中移除键对应的值
 * hasKey(key) - 判断字典中否存在某个键
 * get() - 获取字典中键对应的值
 * clear() - 清空字典
 * size() - 返回字典中的值的数量
 * keys() - 将字典所包含的所有键以数组的形式返回
 * values() - 将字典所包含的所有值以数组的形式返回
 * keyValues() - 将字典中的所有[键, 值]对返回
 * forEach(callback) - 迭代字典中的所有键值对
 * toString() - 迭代字典中的所有键值对
 */
class Dictionary {
    // 可以自定义toStrFn函数，默认使用defaultToString
    constructor(toStrFn = defaultToString) {
        this.toStrFn = toStrFn
        this.table = {}
    }

    set(key, value) {
        const tableKey = this.toStrFn(key);
        const tableValue = new ValuePair(key, value);
        // tableKey为转换为字符串的key，tableValue中存放了原始的key和value
        this.table[tableKey] = tableValue
    }

    remove(key) {
        if(this.hasKey(key)) {
            delete this.table[this.toStrFn(key)];
            return true;
        }
        return false; 
    }

    hasKey(key) {
        return this.table.hasOwnProperty(this.toStrFn(key))
    }

    get(key) {
        const tableValue = this.table[this.toStrFn(key)]
        return tableValue ? tableValue.value : undefined
    }

    clear() {
        this.table = {}
    }

    size() {
        return Object.keys(this.table).length
    }

    keys() {
        return Object.values(this.table).map(tableValue => tableValue.key)
    }

    values() {
        return Object.values(this.table).map(tableValue => tableValue.value)
    }

    keyValues() {
        return Object.values(this.table)
    }

    toString() {
        if (this.size() === 0) {
            return '';
        }
        const tableValues = this.keyValues();
        let objString = `${tableValues[0].toString()}`;
        for (let i = 1; i < tableValues.length; i++) {
          objString = `${objString}, ${tableValues[i].toString()}`; // 拼接
        }
        return objString;
    }

    forEach(callback) {
        const tableValues = this.keyValues()
        for(let i=0, len=tableValues.length; i<len; i++) {
            const result = callback(tableValues[i].key, tableValues[i].value)
            // callback返回false的时候，跳出循环
            if(result === false) {
                break;
            }
        }
    }

}

/**
 * ValuePair类
 * key - 键
 * value - 值
 * toString() - 转换为字符串的形式 
 */
class ValuePair {
    constructor(key, value) {
        this.key = key;
        this.value = value;
    }
    toString() {
        return `[#${this.key}: ${this.value}]`;
    }
}

/**
 * 将传入参数转换为字符串
 */
function defaultToString(item) {
    if (item === null) {
        return 'NULL';
    } else if (item === undefined) {
        return 'UNDEFINED';
    } else if (typeof item === 'string' || item instanceof String) {
        return `${item}`;
    }
    return item.toString();
}
```
#### 6. 散列表
**规则**：字典的一种，使用散列算法计算出键
**实现**：实现散列表数据结构
```js
/**
 * HashTable类
 * table - 散列表数据集合
 * toStrFn(variable) - 将任意类型的值转换为字符串类型
 * hashCode(key) - 散列函数
 * put(key, value) - 向散列表中添加新元素
 * remove(key) - 从字典中移除键对应的值
 */
class HashTable {
    constructor(toStrFn = defaultToString) {
        this.toStrFn = toStrFn;
        this.table = {};
    }

    // 此种散列函数为lose lose散列函数，较为简单，容易存在键冲突的问题
    hashCode(key) {
        if (typeof key === 'number') { // 如果key是number类型，无需转换
            return key;
        }
        const tableKey = this.toStrFn(key); // 将key转换为字符串
        let hash = 0; // 记录tableKey的hash值
        for (let i = 0; i < tableKey.length; i++) {
            hash += tableKey.charCodeAt(i); // 字符串各字符的ASCII值累加
        }
        return hash % 37; // 除以任意数，得到一个较小的数值
    }

    put(key, value) {
        if (key != null && value != null) {
            const position = this.hashCode(key);
            this.table[position] = new ValuePair(key, value);
            return true;
        }
        return false;
    }

    get(key) {
        const valuePair = this.table[this.hashCode(key)];
        return valuePair == null ? undefined : valuePair.value;
    }

    remove(key) {
        const hash = this.hashCode(key); 
        const valuePair = this.table[hash];
        if (valuePair ! = null) {
            delete this.table[hash];
            return true;
        }
        return false;
    }
}

/**
 * ValuePair类
 * key - 键
 * value - 值
 * toString() - 转换为字符串的形式 
 */
class ValuePair {
    constructor(key, value) {
        this.key = key;
        this.value = value;
    }
    toString() {
        return `[#${this.key}: ${this.value}]`;
    }
}

/**
 * 将传入参数转换为字符串
 */
function defaultToString(item) {
    if (item === null) {
        return 'NULL';
    } else if (item === undefined) {
        return 'UNDEFINED';
    } else if (typeof item === 'string' || item instanceof String) {
        return `${item}`;
    }
    return item.toString();
}
```
#### 7. 树
![](https://res.weread.qq.com/wrepub/epub_26211966_171)
**规则**：一些存在父子关系的节点组成的数据结构
**实现**：二叉搜索树（每个节点最多有两个子节点，且允许在左侧节点存储比父节点小的值，在右侧节点存储比父节点大的值）
```js
const Compare = {
    LESS_THAN: -1,
    BIGGER_THAN: 1
};
/**
 * BinarySearchTree类
 * root - 根节点
 * compareFn() - 节点比较函数
 * insert(key) - 向树中插入一个值
 * inOrderTraverse(cb) - 中序遍历所有节点 
 * preOrderTraverse(cb) - 先序遍历所有节点 
 * postOrderTraverse(cb) - 后序遍历所有节点 
 * search(key) - 在树中查找一个值，若节点存在返回true，反之返回false
 * min() - 返回树中的最小值
 * max() - 返回树中的最大值
 * remove(key) - 从树中移除某个值
 */
class BinarySearchTree {
    constructor(compareFn = defaultCompare) {
        this.compareFn = defaultCompare;
        this.root = null;
    }

    insert(key) {
        if (this.root == null ) {
            this.root = new Node(key)
        } else {
            this.insertNode(this.root, key);
        }
    }

    insertNode(node, key) {
        if (this.compareFn(key, node.key) === Compare.LESS_THAN) { // 要插入的值小于当前节点
            if (node.left == null) { // 当前节点不存在左侧子节点，将该值插入到该位置
                node.left = new Node(key);
            } else {
                this.insertNode(node.left, key); // 向下递归，找到合适的位置
            }
        } else {
            if (node.right == null) { // 当前节点不存在右侧子节点，将该值插入到该位置
                node.right = new Node(key);
            } else {
                this.insertNode(node.right, key); // 向下递归，找到合适的位置
            }
        }
    }

    // 中序遍历，以上行顺序遍历所有节点，也就是从小到大的顺序访问所有节点
    inOrderTraverse(cb) {
        this.inOrderTraverseNode(this.root, cb);
    }

    inOrderTraverseNode(node, cb) {
        if(node != null) {
            this.inOrderTraverseNode(node.left, cb);
            cb(node.key);
            this.inOrderTraverseNode(node.right, cb);
        }
    }

    // 先序遍历，以优先于后代节点的顺序访问每个节点
    preOrderTraverse(cb) {
        this.preOrderTraverseNode(this.root, cb);
    }

    preOrderTraverseNode(node, cb) {
        if(node != null) {
            cb(node.key);
            this.preOrderTraverseNode(node.left, cb);
            this.preOrderTraverseNode(node.right, cb);
        } 
    }
    
    // 后序遍历，先访问节点的后代节点，再访问节点本身
    postOrderTraverse(cb) {
        this.postOrderTraverseNode(this.root, cb);
    }

    postOrderTraverseNode(node, cb) {
        if(node != null) {
            this.preOrderTraverseNode(node.left, cb);
            this.preOrderTraverseNode(node.right, cb);
            cb(node.key);
        } 
    }

    search(key) {
        if (this.root == null ) {
            return false
        } else {
            this.searchNode(this.root, key);
        }
    }

    searchNode(node, key) {
        if (this.compareFn(key, node.key) === 0) {
            return true;
        } else if (this.compareFn(key, node.key) === Compare.LESS_THAN) { // 要检索的值小于当前节点
            if (node.left == null) { // 左侧节点不存在，检索失败
                return false;
            } else {
                this.searchNode(node.left, key); // 向下递归
            }
        } else {
            if (node.right == null) { // 右侧节点不存在，检索失败
                return false;
            } else {
                this.searchNode(node.right, key); // 向下递归
            }
        }
    }

    min() {
        return this.minNode(this.root);
    }

    // 二叉搜索树最左侧的子节点即为最小值
    minNode(node) {
        let current = node;
        while (current != null && current.left != null) {
            current = current.left;
        }
        return current.key;
    }

    max() {
        return this.maxNode(this.root);
    }

    // 二叉搜索树最右侧的子节点即为最大值
    maxNode(node) {
        let current = node;
        while (current != null && current.right != null) {
            current = current.right;
        }
        return current.key;
    }

    remove(key) {
        this.root = this.removeNode(this.root, key);
    }

    removeNode(node, key) {
        if (node == null) {
            return null;
        }
        if (this.compareFn(key, node.key) === Compare.LESS_THAN) { // 要删除的值小于当前节点的值
            node.left = this.removeNode(node.left, key); // 递归，直到找到要删除的值
            return node;
        } else if (this.compareFn(key, node.key) === Compare.BIGGER_THAN) { // 要删除的值大于当前节点的值
            node.right = this.removeNode(node.right, key); // 递归，直到找到要删除的值
            return node;
        } else { // 找到了要删除的节点
            // 第一种情况：该节点无子节点
            if (node.left == null && node.right == null) {
                node = null;
                return node; 
            }
            // 第二种情况：有一个子节点
            if (node.left == null) { // 只有一个右子节点
                node = node.right; // 右子节点代替当前节点
                return node;
            } else if (node.right == null) { // 只有一个左子节点
                node = node.left; // 左子节点代替当前节点
                return node;
            }
            // 第三种情况：有两个子节点，找到右侧子节点中的最小节点的值来替换当前节点的值，并移除该最小节点
            const aux = this.minNode(node.right); // 找到子节点的最大值
            node.key = aux.key; // 值替换
            node.right = this.removeNode(node.right, aux.key); // 移除最小节点
            return node;
        }
    }
}

function defaultCompare(a, b) {
    if (a === b) {
        return 0;
    }
    return a < b ? Compare.LESS_THAN : Compare.BIGGER_THAN;
}

/**
 * Node类 - 树节点
 * key - 节点值
 * left - 左侧子节点
 * right - 右侧子节点
 */
class Node {
    constructor(key) {
        this.key = key;
        this.left = left;
        this.right = right;
    }
}
```
#### 8. 二叉堆
![](https://res.weread.qq.com/wrepub/epub_26211966_199)
**规则**： 特殊的二叉树，除了最后一层叶节点，每一层都有左侧和右侧子节点；所有的节点都大于等于（最大堆）或小于等于（最小堆）每个他的子节点。
**实现**：最小堆
```js
const Compare = {
    LESS_THAN: -1,
    BIGGER_THAN: 1
};
/**
 * MinHeap类
 * heap - 堆数据
 * compareFn() - 节点比较函数
 * insert(value) - 向堆中插入一个值
 * extract() - 移除堆中的最小值 
 * findMinimum() - 返回堆中的最小值 
 */
class MinHeap {
    constructor(compareFn = defaultCompare) {
        this.compareFn = compareFn; 
        this.heap = []; // 使用数组来存储数据
    }

    /**
     * 使用数组来实现的二叉树结构，可以使用以下方法获取index位置的节点的相关节点
     * 左侧子节点：2*index+1
     * 右侧子节点：2*index+2
     * 父节点：index/2向下取整
     */
    getLeftIndex(index) {
        return 2 * index + 1;
    }
    getRightIndex(index) {
        return 2 * index + 2;
    }
    getParentIndex(index) {
        if (index === 0) {
            return undefined;
        }
        return Math.floor((index -1) / 2);
    }

    insert(value) {
        if (value != null) {
            this.heap.push(value);
            this.siftUp(this.heap.length-1); // 上移操作，使父节点小于这个插入的值
            return true;
        }
        return false;
    }

    siftUp(index) {
        let parentIndex = this.getParentIndex(index);
        // 交换节点，直到根节点
        while(index> 0 && this.compareFn(this.heap[parentIndex], this.heap[index]) == Compare.BIGGER_THAN) {
            swap(this.heap, parentIndex, index);
            index = parentIndex;
            parentIndex = this.getPrarentIndex(index);
        }
    }

    extract() {
        if (this.heap.length === 0) {
            return undefined;
        }
        if (this.heap.length === 1) {
            return this.heap.shift();
        }
        const removedValue = this.heap.shift();
        this.siftDown(0); // 
        return removedValue;
    }

    siftDown(index) {
        let element = index;
        const left = this.getLeftIndex(index);
        const right = this.getRightIndex(index);
        const size = this.heap.length;
        if (left < size && this.compareFn(this.heap[element], this.heap[left]) ==  Compare.LESS_THAN) { // 当前元素小于左侧子节点
            element = left; // 交换
        }
        if (right < size && this.compareFn(this.heap[element], this.heap[right]) == Compare.LESS_THAN) { // 当前元素小于右侧子节点
            element = right; // 交换
        }
        if (index !== element) {
            swap(this.heap, index, element);
            this.siftDown(element);
        }
    }

    findMinumum() {
        return this.heap.length === 0 ? undefined : this.heap[0]
    }

}

function swap(array, a, b) {
    const temp = array[a]; 
    array[a] = array[b];
    array[b] = temp;
}

function defaultCompare(a, b) {
    if (a === b) {
        return 0;
    }
    return a < b ? Compare.LESS_THAN : Compare.BIGGER_THAN;
}
```
#### 9. 图


### 二、算法部分 


### 参考文档
1. 学习JavaScript数据结构与算法（第3版） - 洛伊安妮·格罗娜