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
（2）使用数组泛型：`let list: Array<number> = [1, 2, 3];`<br>
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