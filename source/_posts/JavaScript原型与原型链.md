---
title: JavaScript原型与原型链
date: 2022-04-30 14:46:11
tags:
- JavaScript
categories:
- [前端]
- [JavaScript进阶]
- [面试]
---

### 原型链
> 每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针（`constructor`），而实例都包含一个指向原型对象的内部指针（`__proto__`）
，假如让原型对象等于另一个类型的实例，此时的原型对象将包含一个指向另一个原型对象的指针，相应的，另一个原型中也包含着指向另一个构造函数的指针。
假如另一个原型对象又是另一个类型的实例，上述关系层层递进，就构成了实例与原型的链条。

![img](https://cdn.jsdelivr.net/gh/Snail-Lu/imageGalleries/gh-pages/2022-04/js-prototype.png)

### 判断原型链上关系的方式
- `instanceof` ---- 判断实例对象与构造函数的关系
- `__proto__` ---- 访问实例对象的原型对象（已废弃，不建议使用）
- `constructor` ----  访问原型对象/实例对象对应的构造函数
- `isPrototypeOf()` ---- 判断对象之间是否有原型链关系
- `Object.getPrototypeOf()` ---- 获取某个实例对象对应的原型对象