---
layout:     post
title:      JavaScript作用域
date:       2019-11-28
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、什么是作用域
作用域是可访问变量的集合，JS 中的作用域是一个存储变量、函数和对象的位置，每个变量、函数和对象都被存储在一个特定的作用域中，它是指变量和函数在代码中的可访问范围，作用域决定了代码中哪些部分可以访问特定的变量和函数。可以将变量和函数封装在不同的作用域中，使其在合适的范围内可访问。<br>
![作用域.png](/instructPic/作用域.png)

就像上图中的示例，Foo1 无法获取 Foo2 中的 var4 变量。<br>

### 二、作用域类型
JavaScript 作用域类型包括全局作用域（Global Scope）、局部作用域（Local Scope）和块级作用域（Block Scope）。<br>

#### 1、全局作用域
全局作用域是在整个代码中都可访问的作用域。在浏览器环境中，全局作用域通常是指 window 对象；在 node 环境下，则是 globalThis 或 global。<br>
在全局作用域中声明的变量或函数是全局变量或全局函数，在代码任何地方都可以访问和使用。<br>
比如图示中的 Foo4 可以访问到全局作用域的变量及函数。<br>

![全局作用域.png](/instructPic/全局作用域.png)

```javascript
const var1 = "var1";
const var2 = "var2";
function Foo1() {
  const var3 = "var3";
}
function Foo2() {
  const var4 = "var4";
  function Foo3() {}
}

function Foo4() {
  console.log(var1, var2, Foo1, Foo2); // var1 var2 [Function: Foo1] [Function: Foo2]
}
Foo4();
```

#### 2、局部作用域
JS 中的局部作用域一般代指函数作用域（Function Scope），它是在函数内部声明的作用域，函数内部的变量和函数只能在函数内部访问，外部无法直接访问。<br>
Foo3 的效果，可以访问到全局以及当前所在函数的作用域的变量及函数。<br>
![局部作用域.png](/instructPic/局部作用域.png)

```javascript
const var1 = "var1";
const var2 = "var2";
function Foo1() {
  const var3 = "var3";
}
function Foo2() {
  const var4 = "var4";
  function Foo3() {
    console.log(var4, Foo3); // var4 [Function: Foo3]
  }
  Foo3();
}
Foo2();
```

#### 3、块级作用域
块级作用域是在代码块（通常是由大括号{}包裹起来的部分）内声明的作用域。比如if(){...}、for(...){...}、try{...}等。<br>
##### ES6之前
在ES6之前，变量都是用 var 声明，没有块级作用域。可通过使用立即执行函数表达式（IIFE）：通过将代码包装在匿名函数中并立即执行该函数，可以创建一个独立的作用域，使得内部声明的变量在函数外部不可访问。<br>
```javascript
(function () {
  var var5 = "var5";
  console.log(var5); // var5
})();
console.log(var5); // var5 is not defined
```

##### ES6之后
在ES6之后，官方推出了块级作用域的概念，使用let和const关键字声明的变量具有块级作用域，它们只能在声明的代码块内部访问。<br>
```javascript
if (true) {
  let var5 = "var5";
  console.log(var5); // var5
}
console.log(var5); // var5 is not defined
```

### 三、作用域链
作用域链（Scope Chain）是JS用于解析标识符（变量和函数）的机制，它是由多个嵌套的作用域组成的，它决定了变量和函数的查找顺序。<br>
上面我们说到局部作用域时谈到的Foo3可以访问全局以及当前函数作用域中的标识符这个特点就归功于作用域链这个概念。当访问一个变量时，JS引擎会先从当前作用域开始查找，如果找不到这个名称的标识符则继续向上一级作用域查找，直到找到变量或达到全局作用域为止，如果在全局作用域中仍然找不到，则认为该标识符未定义。<br>

### 四、变量提升
变量提升是JS在代码执行前将变量和函数声明提升到作用域顶部的行为，它由JavaScript引擎在代码执行前的编译阶段处理。变量提升影响了整个作用域范围内的代码，它允许我们在声明之前使用变量，但是需要注意一点：只有变量声明被提升，赋值不会提升。<br>
```javascript
console.log(var6); // undefined
var var6 = 10;

// 相当于
var var6;
console.log(var6); // undefined
var6 = 10;
```

#### 优先级问题
当同一个作用域中同时出现同名的函数和变量时，函数提升的优先级更高，也就是说函数会在变量之上声明。<br>
```javascript
var a = 10;
function a() {}
console.log(a); // 10

// 相当于
var a; // 函数a
var a; // 变量a
a = function () {};// 使用function声明函数可以看成是声明+赋值
a = 10;
console.log(a); // 10
```

```javascript
function a() {}
var a;
console.log(a); // [Function: a]

// 当把a的赋值去除时，函数的赋值顺序就可以得到验证，相当于
var a; // 函数a
var a; // 变量a
a = function () {};
console.log(a); // [Function: a]
```

### 五、闭包
#### 1、定义
当函数开始执行时，函数中的变量以及函数会压入栈中，那么此时如果当前的作用域中有另一个函数正在使用该作用域的变量，该变量占用的内存也不会被垃圾回收机制回收，这个现象就是闭包。<br>
换句话说，闭包是指函数能够"记住"并访问其创建时的词法环境，在函数定义的词法作用域之外执行同样适用<br>
![闭包.png](/instructPic/闭包.png)

```javascript
const foo = (function iife() {
  const num = 10;
  function foo() {
    return num;
  }
  return foo;
})();
console.log(foo()); // 10
```

上述代码中使用立即执行函数iife作为外部函数的作用域，它返回内部函数foo，而foo函数使用了iife函数中的num变量，形成了闭包，最后在iife函数的外部使用foo时依然可以访问num变量。<br>

#### 2、特点
- 即使外部函数已经执行完毕，内部函数依然可以访问外部函数作用域中的变量（当栈将函数弹出时，变量依然处于内存中）
- 闭包可以持有对外部变量的引用，使得外部变量的值在内部函数中保持活动状态（不被垃圾回收机制回收）
- 闭包中的内部函数可以修改并更新外部变量的值
- 闭包的函数可以获取到创建时的整个作用域链的标识符
- 闭包可能会导致内存泄漏，被闭包引用的变量无法被垃圾回收机制处理

#### 3、使用场景
##### （1）封装私有变量
```javascript
const Animal = (function () {
  const name = "dog";
  function Animal() {}
  Animal.prototype.getName = function () {
    return name;
  };
  return Animal;
})();
console.log(new Animal().getName()); // dog
```

##### （2）延长变量周期
延长变量的生命周期也是闭包的特性之一，该效果通过内部函数对外部作用域的可访问性实现。<br>
```javascript
function delayMessage(msg) {
  return function () {
    return msg;
  };
}
const msg = delayMessage("msg");
console.log(msg()); // msg
```

##### （3）模块化、命名空间
```javascript
var moduleA = (function () {
  var privateVariable = "Hello";
  // 私有函数
  function privateMethod() {
    console.log(privateVariable);
  }
  return {
    // 公共函数
    publicMethod: function () {
      privateMethod();
    },
  };
})();

moduleA.publicMethod(); // Hello
console.log(moduleA.privateVariable); // undefined
```

##### （4）缓存
闭包还可以用于创建缓存函数，以提高函数的执行效率。缓存函数可以将输入参数与其对应的结果保存在内部，避免重复计算。<br>
```javascript
function createCache() {
  var cache = {};
  return function (key, val) {
    if (!cache[key]) {
      cache[key] = val;
      console.log("保存");
    }
    return cache[key];
  };
}

var cacheModule = createCache();
cacheModule("num1", "123"); // 保存
cacheModule("num2", "456"); // 保存
cacheModule("num1", "123");
cacheModule("num2", "456");
```

### 六、ES6的作用域
#### 1、const、let
##### 块级作用域
使用let和const关键字声明的变量具有块级作用域，不仅是条件语句、循环语句，使用{...}定义的范围都是块级作用域。在块级作用域内部声明的变量只在该作用域内有效，并且在作用域外部无法访问。<br>
```javascript
{
  let msg = "hello";
  console.log(msg); // hello
}
console.log(msg); // msg is not defined
```

##### 变量提升
使用let和const声明的变量不会被提升到作用域的顶部，它们只能在声明后才能被访问。这点与var不太一样。

##### 暂时性死区
暂时性死区指的是在变量声明前访问变量会抛出错误。只有在变量声明语句执行完成之后，变量才会进入有效状态，才能被访问和使用。这点效果与上面的变量提升效果一样。

##### 不可重复声明
在同一个作用域中不能被重复声明，否则会报错；而使用var定义变量时后声明的会覆盖先声明的。

```javascript
console.log(var1); // 在赋值前使用了变量“var1”
const var1 = 11;
let var2 = 22; // 无法重新声明块范围变量“var2”
let var2 = 22;
```

#### 2、箭头函数
箭头函数在JavaScript中仍然与普通函数一样有函数作用域的概念。

### 七、动态作用域与词法作用域
作用域的种类有两种：分别是动态作用域和词法作用域（静态作用域）。上述我们介绍的是词法作用域。

#### 1、词法作用域
词法作用域是基于代码的静态结构来确定变量的访问规则。也就是说它由变量和函数在代码中的声明位置而不是调用的位置来确定。<br>
```javascript
function foo1() {
  var var1 = 10; // foo2可访问的作用域
  // 声明foo2的作用域
  return function foo2() {
    console.log(var1);// 10
  };
}
var foo2 = foo1();
function foo3() {
  var var1 = 20; // foo2不可访问的作用域
  // 执行foo2的作用域
  foo2();
}
foo3();
```

在foo1中声明了函数foo2，在foo3中执行foo2，可以看到，foo2取的var1是声明foo2的作用域中的变量（10）。这个现象说明JS采用的是词法作用域。

#### 2、动态作用域
反之，如果还是上述代码，foo2取的var1是执行foo2的作用域中的变量（20），就说明语言采用的是动态作用域。