---
title: TypeScript基础知识总结
date: 2022-03-21 20:46:46
summary: TypeScript基础知识总结
tags:
- TypeScript
- TS
categories:
- [读书笔记]
- [前端进阶]
---

### 基础类型
#### 布尔值
```ts
let isDone: boolean = false;

// 数字(全是浮点数)
let deLiteral: number = 6;           // 十进制 
let hexLiteral: number = oxfood;     // 十六进制 
let binaryLiteral: number = 0b1010;  // 二进制 
let octalLiteral: number = 0o744;    // 八进制
```

#### 字符串
```ts
let name: string = "bob"; 
name = "smith"; 

// 模板字符串，支持定义多行文本，支持使用${}内嵌表达式
let age: number = 37; 
let sentence: string = `Hello,my name is ${name}. 
I'll be ${age+1}years old next month.`;
```

#### 数组
元素的类型相同，两种定义方式：
```ts
// 1.元素类型后面接上[] 
let list: number[] = [1,2,3]; 

// 2.数组泛型，Array<元素类型> 
let list: Array<number> = [1,2,3];
```

#### 元组Tuple
已知元素数量和类型的数组，各元素的类型不必相同。
```ts
let x: [string, number]; 
x = ['hello', 10];  // OK 
x = [10, 'hello'];  // Error
```

#### 枚举
```ts
enum Gender { Male, Female }; //默认情况下，从0开始为元素编号，

let person: { name: string, gender: Gender } = {
  name: 'Nike',
  gender: Gender.Male
}
// 也可以手动指定成员的数值 
// enum Gender { Male = 1, Female }; 

// 可以根据枚举的值获得它对应的名字
let gender: string = Gender[1]; 
console.log(gender); // Female
```

#### Any
对于暂时还不确定类型的变量可以使用any类型，类型检查器在编译时不会检查变量的数据类型。
```ts
let notSure: any = 4; 
notSure = "Maybe a string instead";   // OK 
notSure = false;                      // OK

let list: any[] = [1, true, "free"];
list[1] = 100; // OK
```

#### Void
与`Any`类型相反，表示没有任何类型。
```ts
// 没有返回值 
function warnUser(): void{    
  console.log("This is my warning message"); 
} 
// void类型变量只能赋值undefined和null 
let unusable: void = undefined; 
let unusable: void = null;
```
#### Null 和Undefined

默认情况下`null`和`undefined`是所有类型的子类型。当`--strictNullChecks`标记关闭的情况下，可以将这两个类型赋值给别的类型。
```ts
let u: undefined = undefined; 
let n: null = null;

let test: number = u; // OK
```

#### Never

表示永不存在的值的类型。

`never`类型是任何类型的子类型，也可以赋值给任何类型；除了`never`类型自身，别的类型都不可以赋值给never类型，包括`any`类型。
```ts
// 返回never的函数必须存在无法达到的终点 
function error(message: string): never {
  throw new Error(message);
} 

// 返回never的函数必须存在无法达到的终点 
function infiniteLoop(): never {
  while (true) {}
}

// 推断的返回值类型为never 
function fail() {
  return error("Something failed");
} 
```

#### Object

```ts
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(13); // Error
create('test'); // Error
```

**类型断言**

两种形式。当使用`JSX`时，只能使用as语法形式
```ts
// 形式1： 尖括号语法 
let someValue: any = "this is a string"; 
let strLength: number = (<string>someValue).length; 

// 形式2：as语法 
let someValue: any = "this is a string"; 
let strLength: number = (someValue as string).length;
```

### 接口
接口的作用：定义对象的结构。

#### 可选属性

接口里的属性不全都是必须的，可选属性名字定义的后面加一个`?`符号

```ts
interface SquareConfig {    
  color?: string;   
  width?: number;  
} 

function creatSquare(config: SquareConfig): {color: string, area: number}{    
  let newSquare = { color: "white", area: 100 };    
  if (config.color) {        
    newSquare.color = config.color;    
  }    
  
  if (config.width) {        
    newSquare.area = config.width * config.width;    
  }    
  
  return newSquare; 
} 

let mySquare = creatSquare({color: "black"});
```

#### 额外的属性校验
在上述示例代码中，只给`createSquare()`传递只包含部分属性的对象是可以的，如果传递了接口定义中不存在的属性，TS检查器会报错。这是因为将对象字面量赋值给变量或作为参数传递的时候会被进行`额外的属性检查`。
```ts
interface SquareConfig {    
  color?: string;   
  width?: number;  
} 

function creatSquare(config: SquareConfig): {color: string, area: number}{    
  let newSquare = { color: "white", area: 100 };    
  if (config.color) {        
    newSquare.color = config.color;    
  }    
  
  if (config.width) {        
    newSquare.area = config.width * config.width;    
  }    
  
  return newSquare; 
} 
// error: 'opacity' not expected in type 'SquareConfig'
let mySquare = creatSquare({ color: "black", opacity: 0.5 });

// 解决方法1：使用类型断言
let mySquare = creatSquare({ color: "black", opacity: 0.5 } as SquareConfig);

// 解决方法2：使用一个额外变量作为中转
let squareOptions = { color: "black", opacity: 0.5 };
let mySquare = creatSquare(squareOptions);

```

#### 只读属性
在属性名前使用`readonly`来指定只读属性。另有`ReadonlyArray<T>`类型来指定数组为只读数组。
```ts
interface Point {
  readonly x: number;
  readonly y: number; 
} 

let p1: Point = {x: 10, y: 20}; 
p1.x = 5;      // Error 

// ReadonlyArray<T> 数组创建后，不能再修改 
let a: number[] = [1,2,3,4]; 
let ro: ReadonlyArray<number> = a; 
ro[0] = 5;   // Error
a = ro;  // Error，ro和a为同一个引用，不可修改
```

> readonlly VS const
>
> 最简单判断该用readonly还是const的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 const，若做为属性则使用readonly。

#### 函数类型

接口不仅可以描述带有属性的普通对象，也可以描述函数类型。使用接口表示函数类型时，需要给接口定义一个`调用签名`，它就像是一个只有参数列表和返回值类型的函数定义，参数列表里的每个参数都需要名字和类型。

```ts
interface SearchFunc {    
  (source: string, subString: string): boolean;
} 

let mySearch: SearchFunc; 

// 函数的参数名不需要与接口里定义的名字相同，对应位置的参数类型匹配即可。
mySearch = function(src: string, sub: string) {    
  let result = src.search(sub);    
  return result > -1; 
}
```

#### 可索引的类型

可索引类型具有一个`索引签名`，它描述了对象索引的类型，还有相应的索引返回值类型。

```ts
// 定义StringArray类型的接口，该类型的索引是number类型，值是string类型
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];

// 索引签名设置为只读
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let myAnotherArray: ReadonlyStringArray = ['Jack', 'Bob'];
myAnotherArray[2] = 'Snail'; // Error
```

TypeScript支持两种索引签名：`string`和`number`。可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。因为当使用`number`来索引时，会将其转换成`string`，因此两者返回值需要保持一致。

```ts
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
    [x: number]: Animal;
    [x: string]: Dog;
}
```

#### 类类型

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

#### 继承接口

接口也可以相互继承。

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

// 继承单个接口
interface Square extends Shape {
    sideLength: number;
}

// 继承多个接口
interface Square extends Shape, PenStroke {
  sideLength: number;
}
```

#### 混合类型

具有多个类型的接口

示例：可以同时作为函数和对象使用的接口。

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

#### 接口继承类

当接口继承了一个`类`类型时，它会继承类的成员但不包括其实现。接口同样会继承到类的`private`和`protected`成员。这意味着这个接口类型只能被这个类或其子类所实现（implement）。

```ts
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性，无法实现SelectableControl接口
class Image implements SelectableControl {
    select() { }
}
```

### 函数
#### 函数声明
```ts
// 常规形式
function myAdd1(x: number, y: number): number {
  return x + y;
}
// 函数表达式形式
let myAdd: (x: number, y: number) => number;
// 函数赋值
myAdd = function(x: number, y: number): number {
  return x + y;
}
```
#### 函数完整类型
包含两部分：参数类型和返回值类型。
```ts
// 声明的同时定义函数
let myAdd: (x: number, y: number) => number =
    function(x: number, y: number): number { return x + y; };
```

#### 可选参数
参数名后面添加`?`即表示该参数为可选参数。可选参数必须跟在必须参数后面。
```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}
```

#### 默认参数
可以给参数提供默认值以供用户没有传递这个参数或者传递了`undefined`时使用。
- 在所有必须参数后面的有默认值的参数都是可选参数。
- 带默认值的参数不必一定放在必选参数后面。
- 带默认值的参数出现在必须参数前时，当且仅当用户传入`undefined`时，才会使用默认参数。
```ts
function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
}

let result = buildName(undefined, "Adams");     // okay and returns "Will Adams"
```

#### 剩余参数
可以把函数的所有参数收集到一个变量里，该变量即为剩余参数。剩余参数为可选参数，不限个数。
```ts
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

#### 函数重载
TS中为同一个函数提供多个函数类型定义来进行函数重载。
```ts
function getUserInfo(name: string): string;
function getUserInfo(name: string, age?: number): object;

function getUserInfo(name: string, age?: number): any {
  if (age) {
    return {
      name,
      age
    }
  } else {
    return `Hello, ${name}`
  }
}

console.log(getUserInfo('Jack', 15))
```

### 泛型
在定义函数或类时，如果遇到类型不明确就可以使用泛型。
```ts
// 此处定义泛型是为了保证函数入参与返回值是同一数据类型
function fn<T>(a: T): T {
  return a;
}

let result1 = fn(10); // 调用时不指定泛型，TS会自动推断
let result2 = fn<string>('hello'); // 调用时指定泛型
```