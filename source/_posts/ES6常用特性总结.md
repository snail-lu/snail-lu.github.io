---
title: ES6常用特性总结
date: 2019-10-30 15:40:00
summary: ES6常用特性总结及对应的代码示例
tags:
- ES6
- JavaScript
categories:
- [读书笔记]
---
### 1.let/const
`let` 用来声明一个局部变量，用法类似于 `var`，但是它所声明的变量，只在 `let` 声明所在的块级作用域内有效。
```js
{
    let a = 1;
    var b = 2;
}
console.log(b); // 2
console.log(a); // ReferenceError: a is not defined
```

`const` 用来声明一个只读的常量，作用域与 `let` 相同（声明所在的块级作用域），一旦定义之后不可以修改，不过如果声明的变量如果是引用类型的，可以修改它的属性。
```js
const PI = 3.14;
PI = 3.1415926; // TypeError: invalid assignment to const `PI'

const PERSON = { name: 'Tom' };
PERSON.name = 'Jack';
console.log(PERSON.name); // 'Jack'
```

### 2.函数
- 参数的默认值
```js
function log(x, y = 'world'){
    console.log(x, y);
}
log('hello');   // "hello","world"
```
- `rest` 参数  
`rest` 参数的形式为`...变量名`, 用于获取函数的多余参数，`rest` 参数是一个数组，可以用于替代 `arguments` 对象。
```js
// 普通排序写法，使用arguments
function sortNumbers(){
    return Array.prototype.slice.call(arguments).sort();
}

// 使用rest参数
const sortNumbers = (...numbers) => numbers.sort();
```
- 箭头函数
```js
let sum = (num1, num2) => { return num1 + num2 };
// 或
let sum = (num1, num2) => num1 + num2;
```
- `this` 在箭头函数中的使用  
箭头函数体内的 `this` 对象，是定义时所在的对象，不是使用时所在的对象。
```js
var user = "Mike";
let person = {
    user: "Tom",
    sayHello: function() {
        setTimeout(function() {
            console.log(this.user);   // this指向运行时所在的作用域
        },1000);
        setTimeout(() => {
            console.log(this.user)    // this指向定义时的对象
        },2000)
    }
};
person.sayHello(); 
// "Mike"
// "Tom"  
```

### 3.展开操作符
- 用于函数调用
```js
function sum(x, y, z){
    return x + y + z;
}
let args = [1, 2, 3];
// ES6之前的用法
sum.apply(null, args);  
// 使用展开操作符：
sum(...args);
```

- 用于数组字面量
```js
let arr1 = [1,2,3];
let arr2 = [4,5,6];
// es6之前的用法
let arr3 = arr1.concat(arr2); // [1,2,3,4,5,6]
// 展开操作符用法
let arr3 = [...arr1,...arr2];
```

- 对象的展开运算符
```js
let student = { name:'Tom', age:14 };
student = { ...student, sex:'male' };
```

### 4.模板字符串
```js
let name = 'Mike';
let str = `My name is ${name}`;
console.log(str);          //‘My name is Mike’
```

### 5.解构语法
- 解构数组
```js
let arr = ['blue','green','red'];
let [a,b,c] = arr;        //按照数组序号，一一对应 a='blue',b='green',c='red'
```

- 解构对象
```js
let person = {name:'Jack',age: 20};
let {name,age} = person;  //与属性名一一对应 name='Jack',age=20
```

### 6.类
```js
class Animal {
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    shout(){
        return `My name is ${this.name}, age is ${this.age}`;
    }
    //静态方法
    static foo(){
        console.log('Here is a static method');
    }
}
const cow = new Animal('betty', 2);
cow.shout();        //My name is betty, age is 2
Animal.foo();     //Here is a static method

//派生类
class Dog extends Animal{
    constructor(name, age=3,color='black'){
        //继承父类属性
        super(name,age);
        this.color = color;
    }
    //重写shout方法
    shout(){
        //使用super调用父类方法
        return super.shout() + `,color is ${this.color}`;
    }
}
const dog = new Dog('Bagong');
dog.shout(); //My name is Bagong, age is 3, color is black
```

### 7.模块
- 一个模块的导入/导出  
导出文件module1.js

```js
function func1(){
    console.log("this is func1");
}

export func1;  //使用export导出这个模块
```
导入文件：

```js
import { func1 } from './module1.js';     //使用import导入这个模块（假设两个文件在同级目录下）
```

- 一个模块的多个导出  
导出文件module1.js
```js
//形式1，逐个导出
export const PI = 3.14;                   //导出变量
export function func1(){                  //导出方法
    console.log("this is func1");
}
export let person = {name: "Nike"};       //导出变量

//形式2，底部集体导出
const PI = 3.14;                   
function func1(){                 
    console.log("this is func1");
}
let person = {name: "Nike"};

export { PI, func1, person };            //导出的变量或方法都写在这里
```

导入文件：
```js
//导入方式1，使用对象解构加载
import { PI,func1,person } from './module1.js';

//导入方式2，作为一个整体导入
import * as util from './module1.js';
console.log(util.PI);                //3.14
```

- 模块的默认导出
导出文件module1.js
```js
export default class Person{  //默认导出
    ....
}

export function func1(){ //默认导出的同时，也可以定义其他的非默认导出
    ....
}
```

导入文件：
```js
import importedPerson, { func1 } from './module1.js';      //默认导出的模块不需要使用{}包裹,同时可以在导入处自定义名称 
```

- 导入导出重命名
导出重命名：
```js
//导出模块文件module1.js
function sayHi(){
    console.log('Hi');
}

export { sayHi as sayHello}; //使用as重命名导出

//导入文件
import { sayHello } from './module1.js'; //只能通过sayHello导入
```
导入重命名：
```js
//导出模块文件module1.js
export function sayHi(){
    console.log('Hi');

//导入文件
import { sayHi as sayHello } from './module1.js';
sayHello(); //只能调用sayHello,无法使用sayHi
```

### 8.Set/Map