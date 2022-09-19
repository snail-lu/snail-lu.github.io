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
**原理**：
重写 `SubType` 类型的原型对象为 `SuperType` 类型的实例，这样， `SubType` 的实例就可以顺着原型链访问 `SuperType` 的实例所能访问的所有属性和方法。

**优点**：
父类原型上定义的方法可以被复用

**缺点**：
- 原型中包含的引用值会在所有实例间共享，一变俱变
- 子类型在实例化时无法给父类型的构造函数传参

**代码示例**：
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
// 重写SubType的原型对象
SubType.prototype = new SuperType(); 
SubType.prototype.getSubValue = function () {
    return this.subproperty; 
}; 

let instance1 = new SubType(); 
// SubType的实例可以访问SuperType实例的方法和属性
console.log(instance1.getSuperValue()); // true

let instance2 = new SubType();
// 修改instance1的colors属性，会影响instance2
instance1.colors.push('black')
console.log(instance2.colors); //  ["red","blue","green", "black"]
```
### 2. 盗用构造函数继承
**原理**：
在子类构造函数中调用父类构造函数。

**优点**：
- 解决了原型链继承中存在的原型对象上引用值的继承问题
- 解决了原型链继承中从子类构造函数中向父类构造函数传参的问题

**缺点**：
- 父类的方法在每个子类实例上都要创建一遍，无法复用
- 子类实例无法访问父类原型上的方法

**代码示例**：
```js
function SuperType(show) {
    this.show = show;
    this.colors = ["red", "blue", "green"];
    this.showColors = function() {
        console.log(this.colors)
    }
}
// 父类原型上定义getShow方法
SuperType.prototype.getShow = function() {
    console.log('test')
}
function SubType(show) {
    // 继承 SuperType，为每个实例对象上都创建了独立的colors属性，而非在原型对象上共享
    SuperType.call(this, show);
}
let instance1 = new SubType(true);
instance1.colors.push("black");
console.log(instance1.colors); // ["red","blue","green","black"]
console.log(instance1.show); // true

let instance2 = new SubType(false);
console.log(instance2.colors); // ["red","blue","green"]
console.log(instance2.show); // false

// 缺点1：相同的方法无法复用
console.log(instance1.showColors === instance2.showColors); // false

// 缺点2：无法访问父类原型上的方法
instance1.getShow() // Uncaught TypeError: instance1.getShow is not a function
```

### 3. 组合继承
**原理**：
综合了原型链和盗用构造函数，使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性。

**优点**：
既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。

**缺点**：
效率问题，父类构造函数始终会被调用两次

**代码示例**：
```js
function SuperType(name){ 
    this.name = name; 
    this.colors = ["red", "blue", "green"]; 
} 
SuperType.prototype.sayName = function() { 
    console.log(this.name); 
}; 
function SubType(name, age){ 
    // 盗用构造函数，以继承属性
    SuperType.call(this, name); // 第2次调用SuperType()
    this.age = age; 
} 

// 原型链，以继承方法
SubType.prototype = new SuperType(); // 第1次调用SuperType()
SubType.prototype.sayAge = function() { 
    console.log(this.age); 
}; 
let instance1 = new SubType("Nicholas", 29); 
instance1.colors.push("black"); 
console.log(instance1.colors); // ["red","blue","green","black"]
instance1.sayName(); // "Nicholas"; 
instance1.sayAge(); // 29 

let instance2 = new SubType("Greg", 27); 
console.log(instance2.colors); // ["red","blue","green"]
instance2.sayName(); // "Greg"; 
instance2.sayAge(); // 27

// 方法是公用的，都是SuperType原型上的方法
console.log(instance1.sayName === instance2.sayName) // true
```

### 4. 原型式继承
**原理**：
利用`Object.create()`方法创建一个以指定对象为原型对象的对象实例。

**优点**：
- 无需书写构造函数即可继承某一对象的属性和方法
- 可以传递额外的属性和方法

**缺点**：
- 跟原型链继承一样，原型对象上的引用值会在所有实例间共享

**代码示例**：
```js
let person = { 
    name: "Nicholas", 
    friends: ["Shelby", "Court", "Van"] 
};

// 创建对象，并为新建的对象增加name属性
let anotherPerson = Object.create(person, {
    name: { 
        value: "Greg" 
    }
}); 
anotherPerson.friends.push("Rob"); 

let yetAnotherPerson = Object.create(person); 
yetAnotherPerson.name = "Linda"; 
yetAnotherPerson.friends.push("Barbie"); 

// 缺点：引用类型的值会被共享
console.log(person.friends); // ["Shelby","Court","Van","Rob","Barbie"]
```
### 5. 寄生式继承
**原理**：
创建一个实现继承的函数，以某种方式增强对象，然后返回这个对象。

**优点**：
- 只关注对象，无需关注类型和构造函数

**缺点**：
- 方法在每个实例上都要创建一遍

**代码示例**：
```js
function createAnother(original){ 
    // 这里的copyObject可以是Object.create()方法
    // 也可以是任意一个实现了根据旧对象返回一个新对象的函数
    let clone = copyObject(original); // 通过调用函数创建一个新对象
    clone.sayHi = function() { // 以某种方式增强这个对象
        console.log("hi"); 
    }; 
    return clone; // 返回这个对象
}
```
### 6. 寄生组合式继承
**原理**：
将组合继承中调用父类构造函数来重写子类原型对象的方式，改造为使用寄生式继承来继承父类原型，从而继承父类原型上的方法。
通过盗用构造函数来继承实例属性。

**优点**：
- 解决了组合继承父类构造函数被调用两次的效率问题

**代码示例**：
```js
// 不使用组合继承中调用父类构造函数来重写子类原型对象
// 改用寄生式继承来重写原型对象
function inheritPrototype(subType, superType) { 
    // 这里的copyObject可以是Object.create()方法
    // 也可以是任意一个实现了根据旧对象返回一个新对象的函数
    let prototype = copyObject(superType.prototype); // 创建对象
    prototype.constructor = subType; // 解决由于重写原型导致默认 constructor 丢失的问题 
    subType.prototype = prototype; // 赋值对象
}

function SuperType(name) { 
    this.name = name; 
    this.colors = ["red", "blue", "green"]; 
} 
SuperType.prototype.sayName = function() { 
    console.log(this.name); 
}; 
function SubType(name, age) { 
    // 盗用构造函数，以继承属性
    SuperType.call(this, name);
    this.age = age; 
} 
inheritPrototype(SubType, SuperType); 
SubType.prototype.sayAge = function() { 
    console.log(this.age); 
};
```

### 参考文档
1. JavaScript高级程序设计（第四版）
2. [JavaScript继承](https://juejin.cn/post/7035828718161362981)
3. [JavaScript常用八种继承方案](https://juejin.cn/post/6844903696111763470)