---
layout:     post
title:      TypeScript学习总结
date:       2021-04-01
author:     violetks
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - TypeScript
---

### 一、简介

`TypeScript` 是 `JavaScript` 的一个超集，支持 `ECMAScript 6` 标准。其设计目标是开发大型应用，它可以编译成纯 `JavaScript`，可以在任何浏览器、任何计算机和任何操作系统上运行，并且是开源的。

### 二、基础类型

#### 1、布尔值：`let isDone: boolean = false`
#### 2、数字：`let count: number = 6`
#### 3、字符串：`let name: string = "Angle"`
#### 4、数组：两种方式定义数组。
（1）`let list: number[] = [1, 2, 3];`<br>
（2）使用数组泛型：<br>
```javascript
let list: Array<number> = [1, 2, 3];
let ro: ReadonlyArray<number> = [1, 2]; // 只读，不能修改
ro[0] = 12; // error!
list = ro; // error!
```
上面代码的最后一行，可以看到就算把整个 `ReadonlyArray` 赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：<br>
```javascript
list = ro as number[];
```
#### 5、元组Tuple：一个已知元素数量和类型的数组，各元素的类型不必相同。
```javascript
let list: [string, number]; // 声明一个元组类型
list = ['hello', 10]; // 正确的初始化
list = [10, 'hello']; // 错误的初始化
# 当访问一个越界的元素，会使用联合类型替代：
list[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
console.log(list[5].toString()); // OK, 'string' 和 'number' 都有 toString
list[6] = true; // Error, 布尔不是(string | number)类型
```
#### 6、枚举
```javascript
// 默认从 0 开始为元素编号
enum Color {Red, Green, Blue}
let c: Color = Color.Green; // 值为 1
// 手动赋值
enum ActionMsg {
  A = '新建',
  U = '修改',
  C = '复制',
  D = '删除',
  V = '查看',
}
```
#### 7、Object：`declare function create(o: object | null): void`
#### 8、Any，任何类型，允许在编译时可选择地包含或移除类型检查。
```javascript
let notSure: any = 4;
notSure = "qwe";
notSure = false;
let list: any[] = [];
```
#### 9、一些不常用类型Void、Null、Undefined、Never
（1）`Void`：没有任何类型。当一个函数没有返回值时，其返回值类型是 `void`。
```javascript
function warnMsg (): void {
  console.log("message");
}
```
（2）声明一个 `void` 类型变量，只能赋值 `undefined` 和 `null`。<br>
（3）`never` 类型表示永不存在的值的类型，`never` 类型没有子类型。<br>
（4）`null`、`undefined`、`never` 是所有类型的子类型。<br>
#### 10、类型断言，有两种形式
```javascript
let someValue: any = "this is a string";
# 尖括号语法：
let strLength: number = (<string>someValue).length;
# as 语法：
let strLength: number = (someValue as string).length;
```

### 三、接口
#### 1、使用接口描述带有属性的普通对象
```javascript
// 声明一个接口
interface Person {
  name: string;
  sex: string;
  age: number;
  color?: string; // 可选属性
  readonly height: numner; // 只读属性
}
// 使用接口
const obj: Person = {
  name: '张三',
  sex: '男',
  age: 30
}
```
使用接口时如果传入额外属性会报错，有两种解决方法：<br>
（1）使用类型断言。<br>
（2）接口中添加一个字符串索引签名。<br>
```javascript
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}
```
#### 2、使用接口描述函数类型
```javascript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
// 使用函数类型接口
let mySearch: SearchFunc;
mySearch = function (src: string, sub: string) {
  let res = src.search(sub);
  return res > -1;
}
```
#### 3、使用接口描述可索引的类型
```javascript
interface StringArray {
  [index: number]: string;
}
let myArray: StringArray = ['Bob', 'Fred'];
let myStr: string = myArray[0];
```
上面例子里，我们定义了 `StringArray` 接口，它具有索引签名。这个索引签名表示了当用 `number` 去索引 `StringArray` 时会得到 `string` 类型的返回值。<br>
#### 4、使用接口描述类类型
与 `C#` 或 `Java` 里接口的基本作用一样， `TypeScript` 也能够用它来明确的强制一个类去符合某种契约。<br>
```javascript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date); // 在接口中描述一个方法
}

class Clock implements ClockInterface {
  currentTime: Date;
  // 在类里实现方法
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { }
}
```
#### 5、继承接口
和类一样，接口也可以相互继承。<br>
```javascript
interface Shape {
  color: string;
}
interface PenStroke {
  penWidth: number;
}
// 继承多个接口
interface Square extends Shape, PenStroke {
  sideLength: number;
}
let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```
#### 6、混合类型：例如一个对象可以同时作为函数和对象使用，并带有额外的属性。
#### 7、接口继承类
当接口继承了一个类类型时，它会继承类的成员但不包括其实现。接口同样会继承到类的private和protected成员。这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现。
### 四、函数
#### 1、函数类型
```javascript
// 匿名函数
let myAdd: (x: number, y: number) => number =
    function(x: number, y: number) : number { return x + y; };
```
#### 2、默认参数、可选参数、剩余参数
```javascript
// 可选参数必须放最后
function myName (name: string, firstName = "Smith", lastName?: string) {}
// 剩余参数
function myName (name: string, ...otherName: string[]) {}
```
### 五、泛型
```javascript
// 定义泛型函数
function identity<T>(arg: T): T {
    return arg;
}
// 使用
let output = identity<string>("myString");
let output = identity("myString"); // 类型推论，根据入参自动确定
```
### 六、枚举
#### 1、数字枚举
```javascript
enum Color {
  Red = 1,
  Green,
  Blue
}
console.log(Color.Green); // 2
enum Color {
  Red,
  Green,
  Blue = getSomeValue()
}
```
#### 2、字符串枚举<br>
#### 3、异构枚举，不常用
```javascript
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}
```
#### 4、**反向映射：**数字枚举成员具有反向映射，字符串枚举成员不会生成反向映射。
```javascript
enum Enum {
  A
}
let a = Enum.A;
let nameOfA = Enum[a]; // "A"
```