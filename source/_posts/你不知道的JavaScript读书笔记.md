---
title: 你不知道的JavaScript读书笔记
date: 2021-12-18 00:02:00
tags:
- JavaScript
categories:
- [前端]
- [读书笔记]
---
## 第一部分 作用域和闭包
### 第1章 作用域
#### 1.1 编译原理
在传统编译语言的流程中，程序中的一段源代码在执行之前会经历三个步骤，统称为`编译`。
- 分词/词法分析（Tokenizing/Lexing）
这个过程会将由字符组成的字符串分解成有意义的代码块，这些代码块被称为`词法单元`。
- 解析/语法分析
这个过程是将词法单元流（数组）转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树，这个树被称为`抽象语法树`（Abstract Syntax Tree，AST）。
- 代码生成
将AST转换成可执行代码的过程。

尽管通常将JavaScript归类为`动态`或`解释执行`语言，但事实上它是一门编译语言。对于JavaScript来说，大部分情况下编译发生在代码执行前的几微妙（甚至更短）的时间内。

#### 1.2 理解作用域
总结：变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量，然后在运行时引擎会在作用域中查找该变量，如果能够找到就会对它赋值。

LHS和RHS查询？赋值操作的左侧或右侧。

#### 1.3 作用域嵌套
遍历嵌套作用域链的规则：
> 引擎从当前的执行作用域开始查找变量，如果找不到，就会向上一级继续查找，当抵达最外层的全局作用域时，无论找到还是没找到，查找过程都会停止。

#### 1.4 异常
如果RHS查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出`ReferenceError`异常。试图对一个非函数类型的值进行函数操作，或者引用null或undefined类型的值中的属性，引擎会抛出`TypeError`异常。
ReferenceError同作用域判别失败相关，而TypeError则代表作用域判别成功了，但是对结果的操作是非法或不合理的。

### 第2章 词法作用域
#### 2.1 词法阶段
词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写代码时将变量和块作用域写在哪里决定的。  
作用域查找会在找到第一个匹配的标识符时停止。在多层的嵌套作用域中可以定义同名的标识符，这叫作`遮蔽效应`（内部的标识符遮蔽了外部的标识符）。

#### 2.2 欺骗词法
词法作用域是由写代码期间函数所声明的位置决定的，如何来修改（欺骗）词法作用域呢？
两种机制：
- eval
eval函数可以接收一个字符串为参数，并将其中的内容视为好像在书写时就存在于程序中这个位置的代码。
示例：
```js
function foo(str,a){
    eval( str );
    console.log(a,b);
}
var b = 2;
foo("var b=3;", 1);            //1,3
```

- with
with通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象本身。
```js
var obj = {
  a: 1,
  b: 2,
  c: 3  
};

obj.a = 2;
obj.b = 3;
obj.c = 4;

//使用with关键字
with(obj){
    a = 3;
    b = 4;
    c = 5;
}

// with的词法示例
function foo(obj) {
    with (obj) { 
        a = 2; 
    } 
}
var o1 = { a: 3 };
var o2 = { b: 3 };

foo( o1 ); 
console.log( o1.a ); // 2 

foo( o2 ); 
console.log( o2.a ); // undefined 
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了！
```
with 声明实际上是根据你传递给它的对象凭空创建了一个全新的词法作用域。  
当传递 o1 给 with 时，with 所声明的作用域是 o1，而这个作用域中含有一个同 o1.a 属性相符的标识符。但将 o2 作为作用域时，其中并没有 a 标识符， 因此进行了正常的 LHS 标识符查找。 o2 的作用域、foo(..) 的作用域和全局作用域中都没有找到标识符 a，因此当 a＝2 执行 时，自动创建了一个全局变量。


### 第3章 函数作用域和块作用域
#### 3.1 函数中的作用域
函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复用。

#### 3.2 隐藏内部实现
最小授权或最小暴露原则：是指在软件设计中，应该最小限度地暴露必要内容，而将其他内容都“隐藏”起来。

#### 3.3 函数作用域
通过声明一个具名函数的方法可以创建一个函数作用域，但是函数名的存在也是对所在作用域的一种“污染”，解决办法：
- 匿名和具名
```js
setTimeout(function(){
    console.log("I waited 1 second!");
},1000);
```

其中的function()...就是匿名函数表达式。
匿名函数的缺点：
匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。
当函数需要引用自身时只能使用已经过期的arguments.callee引用。
匿名函数省略了对于代码可读性/可理解性很重要的函数名。

- 立即执行函数表达式(IIFE)
```js
var a = 2;
(function foo(){
    var a = 3;
    console.log(a);   //3
})();

console.log(a);       //2

匿名函数的两种形式：两种形式在功能上是一致的。可以随意选择。
//形式1:
(function(){
    ....
})();

//形式2：
(function(){
    ....
}());

//进阶用法：传递参数进去
(function(global){
    ...
})(window);

//进阶用法：倒置代码的运行顺序
var a = 2;
(function IIFE(def){
    def(window);
})(function def(global){
    var a = 3;
    console.log(a);                 //3
    console.log(global.a);          //2
})
```

### 3.4 块作用域
表面上看JavaScript并没有块作用域的相关功能。
- with
with是块作用域的一种形式。用with从对象中创建出来的作用域仅在with声明中而非外部作用域中有效。

- try/catch
ES3规范中规定try/catch的catch分句会创建一个块作用域，其中声明的变量仅在catch内部有效。

- let
let关键字可以将变量绑定到所在的任意作用域。（通常是{....}内部）
使用let进行的声明不会被提升。
```js
{
    console.log(a);       //2
    var a = 2;            //会被提升到作用域顶部
}

{
    console.log(b);          //ReferenceError
    let b = 3;               //不会被提升
}
```

- const
const同样可以用来创建块作用域变量，但其值是固定的（常量），之后任何试图修改值得操作都会引起错误。（但是可以修改对象的属性）


### 第4章 提升
只有声明本身会被提升，而赋值或其他运行逻辑会留在原地。
```js
foo();                         //不会报错

function foo(){                //函数声明会被提升，所以第一行代码不会报错
    console.log(a);            //undefined
    var a = 2;                 //声明会被提升至函数作用域顶部，但是赋值不会被提升
}
```

- 函数优先
函数声明和变量声明都会被提升，但是函数会首先被提升，然后才是变量。
```js
foo();          //1

var foo;

function foo(){
    console.log(1);    
}

foo = function(){
    console.log(2);
}

foo();        //2
```


### 第5章  作用域闭包
#### 5.4 循环和闭包
```js
for(var i=1;i<=5;i++){
    setTimeout(function(){
        console.log(i);
    },i*1000);
}

//输出结果6个6
```
我们以为循环中的每个迭代在运行时都会给自己“捕获”一个i的副本，但是根据作用域的工作原理，实际情况是尽管循环中的五个函数是在各个迭代中分别定义的，但是他们都被封闭在一个共享的全局作用域中，因此实际上只有一个i。
那么使用IIFE来创建独立的块作用域可以么？
```js
for(var i=1;i<=5;i++){
    (function(){
        setTimeout(function(){
            console.log(i);
        },i*1000);
    })();
}

//输出结果依然是6个6
```

使用IIFE虽然将每次创建的作用域封闭起来了，但是每个作用域中依然共享着上层作用域中的同一个i，所以结果依然没有改变。试着为每个封闭的作用域创建块级变量。
```js
for(var i=1;i<=5;i++){
    (function(){
        var j = i;
        setTimeout(function(){
            console.log(j);
        },j*1000);
    })();
}

//输出结果为1，2，3，4,5,6
```
改进写法：
```js
for(var i=1;i<=5;i++){
    (function(j){
        setTimeout(function(){
            console.log(j);
        },j*1000);
    })(i);
}
```

#### 5.5 模块
```js
function CoolModule(){                 //封闭函数
    var something = "cool";
    var another = [1,2,3];
    
    function doSomething(){
        console.log(something);
    }
    
    function doAnother(){
        console.log(another.join("!"));
    }
    
    return  {                       //返回了一个以上的内部函数
        doSomthing: doSomething,    
        doAnother: doAnother
    }
}

var foo = CoolModule();             //至少被调用一次
foo.doSomething();       //coolo
foo.doAnother();         //1！2！3
```

模块模式需要具备两个必要条件：
1. 必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）
2. 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态


## 第二部分 this和对象原型
### 第1章 关于this
#### 1.2 误解
有两种常见的对于this的误解，但是它们都是错误的。
1. 指向自身
```js
function foo(num) {
    console.log( "foo: " + num );
    // 记录 foo 被调用的次数
    this.count++;
}
foo.count = 0;             //为函数对象foo添加了一个属性count
var i;
for (i=0; i<10; i++) {
    if (i > 5) {
        foo( i );
        //foo.call(foo,i);      //可以通过call方法将this执行函数对象foo本身
    }
} 
// foo: 6
// foo: 7
// foo: 8
// foo: 9
// foo 被调用了多少次？
console.log( foo.count ); // 0 -- WTF?
```

foo.count = 0为foo对象添加了一个count属性，同时this.count++也执行了四次，但是最终的输出结果console.log(foo.count)却为0，说明将this理解为指向自身是错误的。
2. 指向函数的作用域
某些情况下是正确的，某些情况下是错误的。
但是可以明确的是，this在任何情况下都不指向函数的词法作用域。

#### 1.3 this到底是什么
this是在运行时进行绑定的，并不是在编写时绑定的，它的上下文取决于函数调用时的各种条件，this的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。
当一个函数被调用时，会创建一个活动记录（执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在函数执行的过程中用到。

### 第2章 this全面解析
#### 2.1 调用位置
调用栈就是为了能达到当前执行位置所调用的所有函数，调用位置就在当前正在执行的函数的前一个调用中。

#### 2.2 绑定规则
- 默认绑定
```js
function foo(){
    console.log(this.a);
}

var a = 2;
foo();    //2
```

- 隐式绑定
```js
function foo(){
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

obj.foo();     //2
```

当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。

- 隐式丢失
一个常见的this绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把this绑定到全局对象或者undefined上，取决于是否是严格模式。
```js
function foo(){
    console.log(this.a);
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo;

var a = "global";

bar();         //"global"
```

虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身，因此此时的bar()其实是一个不带任何修饰的函数调用，因此应用了默认绑定。
```js
function foo(){
    console.log(this.a);
}

function doFoo(fn){
    fn();
}

var obj = {
    a: 2,
    foo: foo
};

var a = "global";

doFoo(obj.foo);         //"global"

setTimeout(obj.foo,100);    //回调函数也会丢失this的绑定，结果也为"global"
```
参数传递其实就是一种隐式赋值，所以结果和之前的一样。

- 显示绑定
JavaScript提供的绝大多数函数以及你自己创建的所有函数都可以使用call()和apply()方法，这两个方法的第一个参数是一个对象，它们会把这个对象绑定到this，接着在调用函数的时候指定这个this，因此你可以指定this的绑定对象，因此我们称之为显示绑定。
```js
function foo(){
    console.log(this.a);
}

var obj = {
    a:2
};

foo.call(obj);        //2
```

如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作this的绑定对象，这个原始值会被转换成它的对象形式（new String()、new Boolean()、new Number()），这通常被称为“装箱”。
1. 硬绑定
可以解决前面提到的丢失绑定的问题。
```js
function foo(){
    console.log(this.a);
}

var obj = {
    a: 2
}

var bar = function(){
    foo.call(obj);
}

bar();       //2
setTimeout(bar,100);  //2
```

ES5中提供了内置的方法Function.prototype.bin，它会返回一个硬编码的新函数，它会把参数设置为this的上下文并调用原始函数。
```js
function foo(something){
    console.log(this.a, something);
    return this.a + something;
}

var obj = {
    a: 2
};

var bar = foo.bind(obj);
var b = bar(3);       // 2 3
console.log(b);       // 5
```

2. API调用的“上下文”

第三方库的许多函数，以及JavaScript语言和宿主环境中的许多新的内置函数，都提供了一个可选的参数，通常被称为“上下文”（context），其作用和bind()一样，确保你的回调函数使用指定的this。
```js
function foo(el){
    console.log(el,this.id);
}

var obj = {
    id: "awesome"
}

[1,2,3].forEach(foo,obj);         //1 awesome 2 awesome 3 awesome
```

- new绑定

在JavaScript中，构造函数只是一些使用new操作符时被调用的函数，他们不会属于这个类，也不会实例化一个类，实际上，他们甚至都不能说是一种特殊类型的函数类型，他们只是被new操作符调用的普通函数而已。
实际上并不存在所谓的“构造函数”，只有对函数的“构造调用”。
使用new来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：
1. 创建一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的this。
4. 如果函数没有返回其它对象，那么new表达式中的函数调用会自动返回这个新对象。
```js
function foo(a){
    this.a = 2;
}

var bar = new foo(2);

console.log(bar.a);       //2
```

### 2.3 优先级
new绑定>显示绑定>隐式绑定>默认绑定
根据优先级来判断this，顺序如下：
1. 函数是否在new中调用（new绑定）？如果是的话this绑定的就是新创建的对象。
2. 函数是否通过call、apply（显示绑定）或者硬绑定（bind）调用？如果是的话，this绑定的就是指定的对象。
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this绑定的就是那个上下文对象。示例： var bar = obj1.foo();
4. 如果都不是的话，使用默认绑定，如果在严格模式下，就绑定到undefined，否则绑定到全局对象。

#### 2.4 绑定例外
- 被忽略的this
如果把null或者undefined作为this的绑定对象传入call、apply或者bind，这些值在调用时会被忽略，实际应用的是默认绑定规则。
```js
function foo(){
    console.log(this.a);
}

var a = 2;
foo.call(null);        //2
```

- 间接引用
```js
function foo(){
    console.log(this.a);
}

var a = 2;
var o = { a: 3, foo: foo};
var p = { a: 4};

o.foo();    //3
(p.foo = o.foo)();   //2
```
赋值表达式p.foo = o.foo是对函数foo的引用，应用的是默认绑定规则。

- 软绑定
软绑定实现的效果：可以给默认绑定指定一个全局对象或undefined以外的值，那就可以实现和硬绑定相同的效果，同时保留隐式绑定或者显示绑定修改this的能力。
```js
//软绑定函数定义
if (!Function.prototype.softBind) {
    Function.prototype.softBind = function(obj) {
        var fn = this;
        // 捕获所有 curried 参数
        var curried = [].slice.call( arguments, 1 );
        var bound = function() {
            return fn.apply(
                (!this || this === (window || global)) ? obj : this,
                curried.concat.apply( curried, arguments )
            );
        };
        bound.prototype = Object.create( fn.prototype );
        return bound;
    };
}

//测试是否实现了软绑定

function foo() {
    console.log("name: " + this.name);
}
var obj = { name: "obj" },
obj2 = { name: "obj2" },
obj3 = { name: "obj3" };

var fooOBJ = foo.softBind( obj );
fooOBJ(); // name: obj

obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！ ！ ！

fooOBJ.call( obj3 ); // name: obj3 <---- 看！

setTimeout( obj2.foo, 10 );
// name: obj <---- 应用了软绑定
```

#### 2.5 this词法
箭头函数不使用this绑定的四种标准规则，而是根据外层（函数或全局）作用域来决定this。
```js
function foo(){
    return (a)=>{
        console.log(this.a);
    }
}

var obj1 = {
    a: 2
};

var obj2 ={
    a: 3
};

var bar = foo.call(obj1);

bar.call(obj2);     //2 ,不是3
```
foo()内部创建的箭头函数会捕获调用时foo()的this，由于foo()的this绑定到obj1，bar的this也会绑定到obj1，箭头函数的绑定无法被修改。


### 第3章 对象
#### 3.1 语法
对象可以通过两种形式定义：`声明形式`和`构造形式`。
```js
//声明形式
var myObj = {
    key: value
    //...
}

//构造形式
var myObj = new Object();
myObj.key = value;
```

#### 3.2 类型
内置对象：
- String
- Number
- Boolean
- Object
- Function
- Array
- Date
- RegExp
- Error
这些内置对象从表现形式上来看很像其他语言中的类型（type）或者类（class），但是在JavaScript中，他们实际上只是一些内置函数。这些内置函数可以当做构造函数来使用，从而可以构造出一个对应类型的新对象。
```js
var strPrimitive = "I am a string";
typeof strPrimitive;      //'string'
strPrimitive instanceof String;    //false

var strObject = new String("I am a string");
typeof strObject;     //'object'
strObject instanceof String;     //true
```

#### 3.3 内容
在对象中，属性名永远都是字符串。如果你使用string（字面量）以外的其他值作为属性名，那它首先会被转换为一个字符。即使是一个数字也不例外。

- 可计算属性名
ES6增加了可计算属性名，可以在文字形式中使用[]包裹一个表达式来当作属性名：
```js
var prefix = "foo";
var myObj = {
    [prefix + 'bar']: "hello",
    [prefix + 'baz']: "world"
}
```

- 属性与方法
从技术角度来说，函数永远不会“属于”一个对象，所有把对象呢内部引用的函数称为“方法”似乎有点不妥。
最保险的说法可能是，“函数”和“方法”在JavaScript中是可以互换的。

- 数组
数组也是对象，所以虽然每个下标都是整数，但是仍然可以给数组添加属性：
```js
var myArr = ["foo", 42, "bar"];
myArr.baz = "baz";
console.log(myArr.length);         //3      数组长度没有发生变化
console.log(myArr.baz);            //"baz"
```

- 复制对象
深复制：
```js
var newObj = JSON.parse(JSON.stringify(someObj));
```

浅复制：
```js
var newObj = Object.assign({}, myObject);
```

- 属性描述符
查看某个对象某个属性对应的属性描述符：
```js
Object.getOwnPropertyDescriptor(对象，属性名);
```

创建对象时根据需要设置属性描述符：
```js
Object.defineProperty(对象，属性名，{
    value: 属性值，
    writable: true,                 //该属性是否可修改
    configurable: true,               //该属性是否可配置属性描述符
    enumberable: true          //该属性是否可枚举
})
```

- 不变性
对象常量
结合`writable:false`和`configurable:false`就可以创建一个真正的常量属性（不可修改、重定义或者删除）：
```js
var myObject = {};
Object.defineProperty( myObject, "FAVORITE_NUMBER", {
    value: 42,
    writable: false,
    configurable: false
} )
```

禁止扩展
如果想禁止一个对象添加新属性并且保留已有属性，可以使用`Object.preventExtensions(..)`：
```js
var myObject = {
    a: 2
};

Object.preventExtensions(myObject);

myObject.b = 3;
myObject.b;     //undefined
```

密封
`Object.seal(...)`会创建一个“密封”的对象，这个方法会该对象上调用`Object.preventExtensions(..)`并把所有现有属性标记为`configurable:false`。
所以密封的属性不仅不能添加新属性，也不能重新配置或者删除任何现有属性。（可以修改值）
冻结
`Object.freeze(..)`会创建一个冻结对象，这个方法会在该对象上调用`Object.seal(..)`并把所有“数据访问”属性标记为`writable:false`。

- [[Get]]
```js
var myObject = {
    a: 2
};
myObject.a; // 2
```

在语言规范中，myObject.a在myObject上实际上是实现了[[Get]]操作。对象默认的内置[[Get]]操作首先在对象那种查找是否有同名属性，如果有，就返回这个属性的值，如果没有，就会遍历可能存在的[[Prototype]]链（原型链）。

- [[Put]]

[[Put]]触发时，实际的行为取决于许多因素，包括对象中是否已经存在这个属性（这是最重要的因素）。
如果已经存在这个属性，[[Put]]算法大致会检查下列内容：
属性是否是访问描述符？是，并且存在setter就调用setter。
属性的数据描述符中writable是否是false？是，在非严格模式下静默失败，在严格模式下抛出TypeError异常。
都不是，将该值设置为属性的值。

- Getter和Setter

属性分为数据属性和访问器属性。
数据属性拥有value、writable、configurable和enumberable特性，而访问器属性拥有configurable、enumberable、[[Get]]和[[Set]]特性。

- 存在性

myObject.a的属性访问返回值可能是undefined，但是这个值有可能是属性中存储的undefined，也可能是因为属性不存在返回的undefined。可以通过hasOwnProperty()来检查某一对象是否有某一属性。
枚举
可枚举相当于"可以出现在对象属性的遍历中"，可以使用for...in循环来遍历对象的可枚举属性。
可使用myObject.propertyIsEnumerable("a")来检查指定的属性名是否直接存在于某一对象中。
Object.keys(..)会返回一个数组，包含所有的可枚举属性。

#### 3.4 遍历
使用for..in遍历对象是无法直接获取属性值的，因为它实际上遍历的是对象中的所有可枚举属性，你需要手动获取属性值。
使用ES6中的for..of语法来遍历数据结构（数组、对象，等等）中的值，for..of会寻找内置或者自定义的@@interator对象并调用它的next()方法来遍历数据值。


### 第4章 混合对象“类”
#### 4.1 类理论
面向对象编程强调的是数据和操作数据的行为本质是相互关联的，因此好的设计就是把数据以及和它相关的行为打包（或者说封装）起来。这在正式的计算机科学中有时被称为数据结构。

#### 4.2 类的机制
- 建造

为了获得真正可以交互的对象，我们必须按照类来实例化一个东西，这个东西通常被称为实例，有需要的话，我们可以直接在实例上调用方法并访问其所有公有数据属性。
这个对象就是类中描述的所以特性的一份副本。

- 构造函数

类实例是由一个特殊的类方法构造的，这个方法名通常和类名相同，被称为构造函数，这个方法的任务就是初始化实例所需要的所有信息。

#### 4.3 类的继承
在面向类的语言中，你可以先定义一个类，然后定义一个继承前者的类，后者通常被称为“子类”，前者通常被称为“父类”。
定义好一个子类之后，相当于父类来说他就是一个独立并且完全不同的类。子类会包含父类行为的原始副本，但是也可以重写所有继承的行为甚至定义新行为。
- 多态

相对多态：任何方法都可以引用继承层次中高层的方法（无论高层的方法名和当前方法名是否相同）。之所以说“相对”是因为我们并不会定义想要访问的绝对继承层次，而是使用相对引用“查找上一层”。
多态的另一个方面是，在继承链的不同层次中一个方法名可以被多次定义，当调用方法时会自动选择合适的定义。

- 多重继承
有些面向类的语言允许你继承多个“类”。多重继承意味着所有父类的定义都会被复制到子类中。  
相比之下，JavaScript本身并不提供“多重继承”功能。但是可以使用一些方法来实现多重继承。

#### 4.4 混入
- 显示混入
```js
// 非常简单的 mixin(..) 例子 :
function mixin( sourceObj, targetObj ) {
    for (var key in sourceObj) {
        // 只会在不存在的情况下复制
        if (!(key in targetObj)) {
            targetObj[key] = sourceObj[key];
        }
    }
    return targetObj;
}
var Vehicle = {
    engines: 1,
    ignition: function() {
        console.log( "Turning on my engine." );
    },
    drive: function() {
        this.ignition();
        console.log( "Steering and moving forward!" );
    }
};
var Car = mixin( Vehicle, {
    wheels: 4,
    drive: function() {
        Vehicle.drive.call( this );
        console.log("Rolling on all " + this.wheels + " wheels!");
    }
} )
```

mixin(..)的工作原理：它会遍历sourceObj的属性，如果在targetObj没有这个属性就会进行复制。由于我们是在目标对象初始化之后进行复制，因此一定要小心不要覆盖目标对象的原有属性。
如果是先进行复制然对目标对象进行特殊化处理的话，就可以跳过存在性检查。不过这种方法并不好用并且效率更低。
```js
// 另一种混入函数， 可能有重写风险
function mixin( sourceObj, targetObj ) {
    for (var key in sourceObj) {
        targetObj[key] = sourceObj[key];
    }
    return targetObj;
}
var Vehicle = {
    // ...
};
// 首先创建一个空对象并把 Vehicle 的内容复制进去
var Car = mixin( Vehicle, { } );
// 然后把新内容复制到 Car 中
mixin( {
    wheels: 4,
    drive: function() {
        // ...
    }
}, Car );
```

寄生继承：显示混入模式的一种变体，它既是显示的又是隐式的。
```js
//“传统的 JavaScript 类” Vehicle
function Vehicle() {
    this.engines = 1;
} 
Vehicle.prototype.ignition = function() {
    console.log( "Turning on my engine." );
};
Vehicle.prototype.drive = function() {
    this.ignition();
    console.log( "Steering and moving forward!" );
};

//“寄生类” Car
function Car() {
    // 首先， car 是一个 Vehicle
    var car = new Vehicle();
    
    // 接着我们对 car 进行定制
    car.wheels = 4;
    
    // 保存到 Vehicle::drive() 的特殊引用
    var vehDrive = car.drive;
    
    // 重写 Vehicle::drive()
    car.drive = function() {
    vehDrive.call( this );
        console.log(
        "Rolling on all " + this.wheels + " wheels!"
    );
    return car;
}

var myCar = new Car();
myCar.drive();
// 发动引擎。
// 手握方向盘！
// 全速前进
```

首先我们复制一份Vehicle父类（对象）的定义，然后混入子类（对象）的定义，然后用这个复合对象构建实例。

- 隐式混入
```js
var Something = {
    cool: function() {
        this.greeting = "Hello World";
        this.count = this.count ? this.count + 1 : 1;
    }
};

Something.cool();
Something.greeting; // "Hello World"
Something.count; // 1
var Another = {
    cool: function() {
        // 隐式把 Something 混入 Another
        Something.cool.call( this );
    }
};
Another.cool();
Another.greeting; // "Hello World"
Another.count; // 1（count 不是共享状态）
```

通过在构造函数调用或者方法调用中使用Something.cool.call(this)，我们实际上“借用”了函数Something.cool()并在Another的上下文中调用了它（通过this绑定），最终的结果就是Something.cool()中的夫妇只操作都会在Another对象上而不是Something对象上。

### 第5章 原型
#### 5.1 [[Prototype]]
- Object.prototype
所有普通的[[Prototype]]链最终都会指向内置的Object.prototype，它包含JavaScript中许多通用的功能。

- 属性设置和屏蔽
给一个对象设置属性并不仅仅是添加一个新属性或者修改已有的属性值。
```js
myObject.foo = "bar";
```
如果myObject对象中包含名为foo的普通数据访问属性，这条语句只会修改已有的属性值。
如果foo既出现在myObject也出现在myObject的[[Prototype]]链上，那么myObject中的foo属性机就会屏蔽原型链上层的foo属性。
如果foo不是直接存在于myObject中，[[Prototype]]链就会被遍历，如果原型链上找不到foo，就会在myObject上添加一个foo属性。如果原型链上存在foo属性，可能会出现以下三种情况：

1. 原型链上存在名为foo的普通数据访问属性并且没有被标记为只读，那么就会在myObject中添加一个名为foo的新属性，它是屏蔽属性。
2. 原型链上存在foo，但是foo被标记为只读，那么无法修改已有属性或者在myObject上创建屏蔽属性，如果在严格模式下，代码会抛出一个错误。否则，该赋值语句会被忽略，总之，不会发生屏蔽。
3. 原型脸上存在的foo是一个setter，那就一定会调用这 个setter，foo不会被添加到myObject，也不会重新定义foo这个setter。

#### 5.2 “类”
- “构造函数”
```js
function Foo(){
    //...
}
Foo.prototype.constructor === Foo;      //true

var a  = new Foo();
a.constructor === Foo;     //true
```

实际上，Foo函数经常被认为是一个构造函数，其实构造函数和普通函数没有任何区别，函数本身并不是构造函数，但是使用new关键字之后，就会把这个函数调用编程一个“构造函数"调用。

#### 5.3 （原型）继承
```js
function Foo(name) {
    this.name = name;
} 
Foo.prototype.myName = function() {
    return this.name;
};
function Bar(name,label) {
    Foo.call( this, name );
    this.label = label;
} 

// 我们创建了一个新的 Bar.prototype 对象并关联到 Foo.prototype
Bar.prototype = Object.create( Foo.prototype );
// 注意！ 现在没有 Bar.prototype.constructor 了
// 如果你需要这个属性的话可能需要手动修复一下它
Bar.prototype.myLabel = function() {
    return this.label;
};
var a = new Bar( "a", "obj a" );
a.myName(); // "a"
a.myLabel(); // "obj a"
```

检查"类“关系
在传统的面向类环境中，检查一个实例（JS中的对象）的继承祖先（JS中的委托关联）通常被称为内省（或者反射）。
```js
// 非常简单： b 是否出现在 c 的 [[Prototype]] 链中？
b.isPrototypeOf( c );

//ES5中，标准的方法
Object.getPrototypeOf( a ) === Foo.prototype; // true

//非标准方法
a.__proto__ === Foo.prototype; // true
```


#### 5.4 对象关联
[[Prototype]]机制急就是存在于对象中的一个内部链接，它会引用其它对象。
通常来说，这个链接的作用是：如果在对象上没有找到需要的属性或者方法引用，引擎就会继续在[[Prototype]]关联的对象上进行查找。同理，如果在后者中页没有找到需要的引用就会继续查找它的[[Prototype]]。以此类推，这一系列对象的链接被称为“原型链”。

- 创建关联
```js
var foo = {
something: function() {
    console.log( "Tell me something good..." );
}
};
var bar = Object.create( foo );
bar.something(); // Tell me something good...
```

- 关联关系是备用
```js
var anotherObject = {
    cool: function() {
        console.log( "cool!" );
    }
};
var myObject = Object.create( anotherObject );

myObject.cool();   //"cool!"           //直接委托

myObject.doCool = function() {
    this.cool(); // 内部委托！
};
myObject.doCool(); // "cool!"
```
内部委托可以让API接口更加清晰。

### 第6章 行为委托
#### 6.1 面向委托的设计
委托行为意味着某些对象在找不到属性或者方法引用时会把这个请求委托给另一个对象。
互相委托（禁止）
禁止在两个或两个以上互相（双向）委托的对象之间创建循环委托。之所以要禁用互相委托，是因为引擎的开发者们发现在设置时检查（并禁止）一次无线循环引用要更加高效，否则每次从对象中查找属性时都要进行检查。

#### 6.2 类与对象
使用纯JavaScript实现类风格的代码：
```js
// 父类
function Widget(width,height) {
    this.width = width || 50;
    this.height = height || 50;
    this.$elem = null;
} 

Widget.prototype.render = function($where){
    if (this.$elem) {
        this.$elem.css( {
            width: this.width + "px",
            height: this.height + "px"
        } ).appendTo( $where );
    }
};

// 子类
function Button(width,height,label) {
    // 调用“super” 构造函数
    Widget.call( this, width, height );
    this.label = label || "Default";
    this.$elem = $( "<button>" ).text( this.label );
    } 

// 让 Button“继承” Widget
Button.prototype = Object.create( Widget.prototype );
// 重写 render(..)
Button.prototype.render = function($where) {
    //“super” 调用
    Widget.prototype.render.call( this, $where );
    this.$elem.click( this.onClick.bind( this ) );
};

Button.prototype.onClick = function(evt) {
    console.log( "Button '" + this.label + "' clicked!" );
};

$( document ).ready( function(){
    var $body = $( document.body );
    var btn1 = new Button( 125, 30, "Hello" );
    var btn2 = new Button( 150, 40, "World" );
    btn1.render( $body );
    btn2.render( $body );
} );
```
代码中出现了丑陋的显示伪多态，即通过Widget.call和Widget.prototype.render.call从子类方法中引用父类中的基础方法。

ES6的class语法糖
```js
class Widget {
    constructor(width,height) {
        this.width = width || 50;
        this.height = height || 50;
        this.$elem = null;
    } 
    render($where){
        if (this.$elem) {
            this.$elem.css( {
                width: this.width + "px",
                height: this.height + "px"
            } ).appendTo( $where );
        }
    }
}

class Button extends Widget {
    constructor(width,height,label) {
        super( width, height );
        this.label = label || "Default";
        this.$elem = $( "<button>" ).text( this.label );
    } 
    render($where) {
        super( $where );
        this.$elem.click( this.onClick.bind( this ) );
    } 
    onClick(evt) {
        console.log( "Button '" + this.label + "' clicked!" );
    }
}

$( document ).ready( function(){
    var $body = $( document.body );
    var btn1 = new Button( 125, 30, "Hello" );
    var btn2 = new Button( 150, 40, "World" );
    btn1.render( $body );
    btn2.render( $body );
} );
```

使用对象关联委托实现：
```js
var Widget = {
    init: function(width,height){
        this.width = width || 50;
        this.height = height || 50;
        this.$elem = null;
    },
    insert: function($where){
        if (this.$elem) {
            this.$elem.css( {
            width: this.width + "px",
            height: this.height + "px"
            } ).appendTo( $where );
        }
    }
};
var Button = Object.create( Widget );
Button.setup = function(width,height,label){
    // 委托调用
    this.init( width, height );
    this.label = label || "Default";
    this.$elem = $( "<button>" ).text( this.label );
};
Button.build = function($where) {
     // 委托调用
    this.insert( $where );
    this.$elem.click( this.onClick.bind( this ) );
};
Button.onClick = function(evt) {
    console.log( "Button '" + this.label + "' clicked!" );
};
$( document ).ready( function(){
    var $body = $( document.body );
    var btn1 = Object.create( Button );
    btn1.setup( 125, 30, "Hello" );
    var btn2 = Object.create( Button );
    btn2.setup( 150, 40, "World" );
    btn1.build( $body );
    btn2.build( $body );
} );  
```

#### 6.5 内省
自省就是检查实例的类型。类实例的自省主要目的是通过创建方式来判断对象的结构和功能。
```js
var Foo = { /* .. */ };
var Bar = Object.create( Foo );
Bar...
var b1 = Object.create( Bar );

//内省
// 让 Foo 和 Bar 互相关联
Foo.isPrototypeOf( Bar ); // true
Object.getPrototypeOf( Bar ) === Foo; // true
// 让 b1 关联到 Foo 和 Bar
Foo.isPrototypeOf( b1 ); // true
Bar.isPrototypeOf( b1 ); // true
Object.getPrototypeOf( b1 ) === Bar; // true
```































