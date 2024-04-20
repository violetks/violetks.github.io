---
layout:     post
title:      JavaScript基础知识（一）
date:       2019-11-24
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、与Java的区别
1、JavaScript的程序代码由客户端浏览器解释执行；Java程序则必须先经过编译，转换成class文件，在JVM环境下执行。<br>
2、JavaScript模仿Java的语法，设计了一套类似于纯面向对象（常称为基于对象）的语法结构。<br>
3、JavaScript的程序代码直接嵌入HTML文件中，而Java编写的是一个独立的Java程序。<br>
4、JavaScript是动态的弱类型语言，使用前无需声明变量的数据类型；Java是静态类型语言，或称为强制类型语言，变量的类型必须事先定义。<br>

### 二、JavaScript的作用
1、进行表单验证。<br>
2、实现页面特效。<br>
3、动态调整页面内容。<br>

### 三、JavaScript的组成
1、核心语法（ECMAScript）。<br>
2、浏览器对象模型（BOM）：提供与浏览器交互的方法和接口。<br>
3、文档对象模型（DOM）：提供访问和操作网页内容的方法和接口。<br>

### 四、说说JS的基本规范
1、不要在同一行声明多个变量。<br>
2、请使用`===/!==`来比较`true/false`或者数值。<br>
3、使用对象字面量替代`new Array`这种形式。<br>
4、不要使用全局函数。<br>
5、`switch`语句必须带有`default`分支。<br>
6、函数不应该有时候有返回值，有时候没有返回值。<br>
7、`for`循环必须使用大括号。<br>
8、`if`语句必须使用大括号。<br>
9、`for-in`循环中的变量，应该使用`var`关键字明确限定作用域，从而避免作用域污染。<br>
10、命名规则中构造器函数首字母大写，如`function Person(){}`。<br>
11、写注释。<br>

### 五、什么是全局变量？如何声明？使用全局变量有哪些问题
全局变量是整个代码长度可用的变量，也就是说这些变量没有任何作用域。**var关键字用于声明局部变量或对象。**如果省略var关键字，则声明一个全局变量。<br>
使用全局变量的**优点**是：可以减少变量的个数，减少由于实际参数和形式参数的数据传递带来的时间消耗。<br>
使用全局变量所面临的**问题**是本地和全局变量名称的冲突。此外，全局变量破坏了函数的封装性能，很难调试和测试依赖于全局变量的代码。<br>

### 六、JavaScript输出方式
1、`alert()`：以字符串格式输出<br>
```javascript
alert(true)     // 输出字符串true
alert([1,2,3])  // 输出字符串1,2,3
alert({ name: 'violetks', age: 21 }) // 输出字符串[object Object]
```

2、`confirm()`：在alert的基础上增加了两个选择按钮：确定和取消。<br>
3、`prompt("提示句", "值")`：在confirm的基础上增加了让用户输入的效果。<br>
4、控制台输出：（快捷操作：变量名.log，然后按Tab键补全）<br>
```javascript
console.log();
console.warn();
console.error();
```
5、向页面输出内容：`document.write();`

### 七、JavaScript数据类型
- **六个值类型：**string、number、boolean、null、undefined、Symbol（ES6引入，表示独一无二的值）
- **引用数据类型：**Object、Array、Function、Date、RegExp（正则）。

#### 检测类型常用`typeof`和`instanceof`
（1）`typeof`：返回值是一个字符串，用来说明变量的数据类型。<br>
- 返回结果：string、number、boolean、function、object、undefined
- 判断变量是否存在：`typeof a != "undefined"`
- 对于Array、Null等特殊对象使用typeof一律返回object

（2）`instanceof`：用于判断一个变量是否属于某个对象的实例。<br>
```javascript
({}) instanceof Object              //true
([]) instanceof Array               //true
(/aa/g) instanceof RegExp           //true
(function(){}) instanceof Function  //true
```

#### typeof bar === "object"这个验证的风险有哪些？
对于Array、Null等特殊对象都是是返回object，会判断错误。<br>
解决方法：`(bar !== null) && (typeof bar === “object”) && (! $.isArray(bar))`

```javascript
console.log(true + 1);         //2
console.log('name' + true);  	 //nametrue
console.log(undefined + 1); 	 //NaN
console.log(typeof undefined); //undefined
console.log(typeof(NaN));      //number
console.log(typeof(null));     //object
```

### 八、==和===的区别
（1）==：如果两个值类型不同也有可能相等，null和undefined使用==判断是相等的。<br>
（2）===：严格相等，类型不同不相等，null和undefined不相等。<br>
（3）==会自动转换类型，===不会。<br>

### 九、null和undefined的区别
1、undefined：Undefined类型，字面意思是未定义的值，**表示一个变量最原始的状态，而非人为操作的结果。**这种原始状态会在以下 4 种场景中出现：<br>
（1）声明了一个变量但未初始化。<br>
（2）访问对象上不存在的属性。<br>
（3）函数定义了形参，但没有传递实参。<br>
（4）使用 void 对表达式求值。<br>
2、null：Null类型，代表 “空值”，**表示一个对象被人为的设置为空对象，而非一个变量最原始的状态。**代表一个空对象指针，使用typeof运算得到 “object” ，是因为JavaScript数据类型在底层都是以二进制的形式表示的，**二进制的前三位为 0 会被 typeof 判断为对象类型**，而 null 的二进制位恰好都是 0 ，因此 null 被误判断为 Object 类型。<br>

### 十、NaN是Number类型，验证它的函数：isNaN()

### 十一、JavaScript实现继承的六种方式
原型链继承、构造函数继承、组合继承、原型式继承、寄生式继承、寄生组合式继承。<br>
参考文章：https://www.cnblogs.com/Leophen/p/11401734.html

### 十二、双问号的使用

```javascript
let str = name ?? '';
```
如果 `name` 存在， `str = name`，不存在则 `str = ''`。

### 十三、JavaScript构造函数和普通函数的区别（★）
1、构造函数也是一个普通函数，创建方式和普通函数一样，但构造函数习惯上首字母大写。<br>
2、构造函数和普通函数的区别在于：调用方式不一样。作用也不一样，构造函数用来新建实例对象。<br>
3、调用方式：普通函数的调用方式直接用`person()`，构造函数的调用方式需要使用`new`关键字来调用`new Person()`。<br>
4、构造函数的函数名与类名相同，`Person()`这个构造函数，`Person`既是函数名，也是这个对象的类名。<br>
5、构造函数内部用`this`来构造属性和方法。<br>
```javascript
function Person(name, job, age) {
  this.name = name;
  this.job = job;
  this.age = age;
  this.sayHi = function() {
    console.log("Hi")
  }
}
```

### 十四、JavaScript递归函数（★）
1、如果一个函数在内部可以调用其本身，那么这个函数就是**递归函数**。<br>
2、递归应用例子：求1~n的阶乘、求斐波那契数列、实现多级菜单结构。<br>