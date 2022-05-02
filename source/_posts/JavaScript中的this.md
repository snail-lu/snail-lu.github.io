---
title: JavaScript中的this
date: 2022-05-01 22:17:16
tags:
- JavaScript
categories:
- [前端]
- [JavaScript进阶]
- [面试]
---

### 一、this到底是什么
`this`是在运行时进行绑定的，并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件，`this`的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。
当一个函数被调用时，会创建一个`活动记录`（执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在函数执行的过程中用到。

### 二、绑定规则
#### 1. 默认绑定
普通函数直接调用时（非严格模式下），应用 `this` 的`默认绑定`，`this`指向全局对象（浏览器中为`window`对象，`Node.js`中为`global`对象）。
> 严格模式下，`this`会被绑定到`undefined`。
```js
function foo(){
    console.log(this.a);
}

var a = 2;
foo();    //2
```

#### 2. 隐式绑定
当函数引用有上下文对象时，隐式绑定规则会把函数调用中的 `this` 绑定到这个上下文对象。且当对象属性存在引用链时，最后一层引用决定调用位置。
```js
function foo() { 
    console.log( this.a ); 
}
const obj = { 
    a: 2, 
    foo
};

obj.foo(); // 2


const obj1 = {
    a: 3,
    obj
}

obj1.obj.foo(); // 2
```

#### 3. 显示绑定
利用JS提供的`call()`、`apply()`、`bind()`方法显示指定`this`的绑定对象。
```js
function foo(){
    console.log(this.a);
}

const obj = {
    a: 2
};

foo.call(obj);     // 2
foo.apply(obj);    // 2

const foo1 = foo.bind(obj);
foo1();            // 2
```

#### 4. `new`绑定
使用`new`来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：
1. 创建一个全新的对象。
2. 这个新对象会被执行`[[原型]]`连接。
3. 这个新对象会绑定到函数调用的`this`。
4. 如果函数没有返回其它对象，那么`new`表达式中的函数调用会自动返回这个新对象。
```js
function foo(a){
    this.a = 2;
}

var bar = new foo(2);

console.log(bar.a);       //2
```

### 三、绑定规则的优先级
`new绑定` > `显示绑定` > `隐式绑定` > `默认绑定`
根据优先级来判断`this`的指向，顺序如下：
1. 由 `new` 调用？绑定到新创建的对象。 
2. 由 `call` 或者 `apply`（或者 `bind`）调用？绑定到指定的对象（指定`null`或`undefined`的时候会被忽略，然后遵循`默认绑定`规则绑定`this`）。 
3. 由上下文对象调用？绑定到那个上下文对象。 
4. 默认：在严格模式下绑定到 `undefined`，否则绑定到全局对象。
### 四、箭头函数中的this
箭头函数不使用上述`this`绑定的四种标准规则，而是根据外层（函数或全局）作用域来决定`this`。
```js
function foo(){
    return () => {
        console.log(this.a);
    }
}

const obj1 = {
    a: 2
};

const obj2 ={
    a: 3
};

const bar = foo.call(obj1);

bar.call(obj2);     // 2 ,不是3
```
`foo()`内部创建的箭头函数会捕获调用`foo()`的`this`，由于`foo()`的`this`绑定到`obj1`，`bar`的`this`也会绑定到`obj1`，箭头函数的绑定无法被修改。


### 五、常见使用浅析
```js
var name = "window";
var obj = {
    name: "obj",
    // 传统函数形式
    getName1: function() {
        console.log(this.name)
    },

    // 箭头函数-作为直接执行的方法
    getName2: () => {
        console.log(this.name)
    },

    // 传统函数闭包
    getName3: function(){
        setTimeout(function(){
            console.log(this.name);
        }, 500)
    },
 
    // 箭头函数闭包
    getName4: function(){
        setTimeout(() => {
            console.log(this.name)
        },500)
    }
};
// 调用时this应用隐式绑定规则，绑定到obj上
obj.getName1() // "obj"

// obj.getName2为箭头函数，调用时作用域为全局作用域，this被绑定到window
obj.getName2() // "window"

// setTimeout回调函数，应用默认绑定，绑定到window
obj.getName3() // "window"

// setTimeout回调函数为箭头函数，外层作用域为getName4的作用域，
// 此作用域中的this指向obj，则箭头函数中的this指向也为obj
obj.getName4() // "obj"


// 函数的间接引用，直接调用函数，应用默认绑定规则，this指向window
const getName1 = obj.getName1;
getName1(); // "window"
```

### 六、总结
> 每个函数在被调用时都会自动取得两个特殊变量：`this` 和 `arguments`。内部函数在搜索这两个变量时，只会搜索到其活动对象为止，因此永远不可能直接访问外部函数中的这两个变量。  

闭包中的`this`是无法`“继承”`其外部作用域中的`this`的，所以闭包中的`this`可以直接用上述4中规则进行判断。而箭头函数会继承外层函数调用的 `this` 绑定。
### 七、参考文章
- [KYLE SIMPSON.你不知道的JavaScript-上卷]()
- [尼古拉斯·泽卡斯.JavaScript高级程序设计]()