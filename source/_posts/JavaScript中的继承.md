---
title: JavaScript中的继承
date: 2022-07-16 15:45:53
summary: JavaScript中的继承知识总结
tags:
- JavaScript
- 面试
categories:
- [前端进阶]

---
### 1. 原型链继承
**原理**：重写 `SubType` 类型的原型对象为 `SuperType` 类型的实例，这样， `SubType` 的实例就可以顺着原型链访问 `SuperType` 的实例所能访问的所有属性和方法。
**缺点**：
- 原型中包含的引用值会在所有实例间共享，一变俱变
- 子类型在实例化时无法在不影响所有对象实例的情况下给父类型的构造函数传参
```js
function SuperType() { 
    this.property = true;
    this.colors = ["red", "blue", "green"];
} 
SuperType.prototype.getSuperValue = function() { 
    return this.property; 
}; 
function SubType() { 
    this.subproperty = false; 
} 
// 重写原型对象
// SuperType实例中拥有引用类型的
SubType.prototype = new SuperType(); 
SubType.prototype.getSubValue = function () {
    return this.subproperty; 
}; 
let instance1 = new SubType(); 
console.log(instance1.getSuperValue()); // true
console.log(instance1.colors)  // ["red","blue","green"]

let instance2 = new SubType();
console.log(instance2.colors); //  ["red","blue","green"]
```
### 2. 构造函数继承
### 3. 组合继承
### 4. 原型式继承
### 5. 寄生式继承
### 6. 寄生组合式继承

### 参考文档
