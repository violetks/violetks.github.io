---
layout:     post
title:      JavaScript代码片段分析
date:       2019-11-18
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、setTimeout输出10个10
```javascript
for (var i = 0; i < 10; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```
分析：涉及到JavaScript的执行机制，JavaScript是一个单线程的解释器，`setTimeout`是异步执行函数，本质是间隔一定时间将任务添加到任务队列（`Event Queue`）中。`for`循环作为主线程先执行完毕，按序执行10次输出 `i`，就会输出10个10。<br>
#### 如何实现按序输出？
**方法一：**闭包
```javascript
for (var i = 0; i < 10; i++) {
  (function (i) {
    setTimeout(function () {
      console.log(i);
    }, 1000);
  })(i);
}
```
**方法二：**ES6的`let`声明变量
```javascript
for (let i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i);
  }, 1000);
}
```
**方法三：**使用`setTimeout`的第三个参数
```javascript
for (var i = 0; i < 10; i++) {
  setTimeout(function () {
    console.log(i);
  }, 1000, i);
}
```

### 二、意外全局变量
```javascript
function foo () {
  let a = b = 0;
  a++;
  return a;
}

foo();
typeof a;  // => ???
typeof b;  // => ???
```
分析：`let a = b = 0;`该语句声明一个局部变量 `a`，以及全局变量 `b`。<br>
在 `foo()` 范围或全局范围中都没有声明变量 `b`。因此JavaScript将 `b = 0` 解释为 `window.b = 0`。<br>
在浏览器中，以上代码片段等效于：<br>
```javascript
function foo () {
  let a;
  window.b = 0;
  a = window.b;
  a++;
  return a;
}

foo();
typeof a;  // => 'undefined'
typeof b;  // => 'number'
```
`typeof a` 等于 `'undefined'`，变量 `a` 存在于 `foo()` 范围内，而在外部范围内不使用。<br>
`b` 是一个值为 0 的全局变量。

### 三、数组的length属性
```javascript
const clothes = ['jacket','t-shirt'];
clothes.length = 0;

clothes[0];  // => ???
```
分析：**减少length属性的值的结果是删除自己的数组元素。**因此，`clothes[0]`等于`undefined`，因为 `clothes` 数组已被清空。<br>

### 四、鹰眼测试
```javascript
// numbers 数组的内容是什么？
const length  = 4;
const numbers = [];
for (var i = 0; i < length; i++);{
  numbers.push(i + 1);
}

numbers; // => ???
```
分析：让我们仔细看一下分号 `;` 出现在左大括号 `{`之前。这个分号创建了一个空语句。上面的代码等效于以下代码：<br>
```javascript
const length = 4;
const numbers = [];
var i;
for (i = 0; i < length; i++) {
  // does nothing
}
{
  // a simple block
  numbers.push(i + 1);
}

numbers; // => [5]
```
`for()` 将 `i` 变量递增到4，然后JavaScript一次进入块 `{numbers.push(i + 1);}`，将 `4 + 1` 推入数字数组。
因此，`numbers` 数组的内容为 `[5]`。<br>

### 五、自动分号插入
```
// arrayFromValue() 返回什么值？
function arrayFromValue (item) {
  return
    [item];
}

arrayFromValue(10); // => ???
```
分析：很容易错过 `return` 关键字和 `[item]` 表达式之间的换行符。此换行符使JavaScript自动在 `return` 和 `[item]` 表达式之间插入分号。
`return;` 函数内部使其返回 `undefined`。因此 `arrayFromValue(10)` 的值为 `undefined`。<br>

### 六、浮点数计算
```javascript
0.1 + 0.2 === 0.3 // => ???
```
分析：浮点数计算会产生误差，并不等于 `0.3`，结果是 `false`。<br>

### 七、变量提升
```javascript
myVar;   // => ???
myConst; // => ???

var myVar = 'value';
const myConst = 3.14;
```
分析：在声明之前访问 `myVar` 的结果为 `undefined`。在初始化之前，提升的 `var` 变量具有 `undefined` 的值。<br>
然而，在声明行之前访问 `myConst` 会引发 `ReferenceError`。`const` 变量处于临时死区，直到声明行 `const myConst = 3.14`。<br>

### 八、下列表达式结果
```javascript
""==0      // true，0和空字符串都是false，值相等
{}==={}    // false，引用数据类型，是两个独立的对象
0===0      // true
!""        // true
[]==![]    // true，相当于[]==false，类型转换0==0，所以是true
1+'1'      // 11
```

### 九、逻辑运算符
```javascript
var a=1,b=true,c=2,d=3;
a = b && d && c;
console.log(a);    // 2
```
`a && b`：如果a是false，那么b不管是true还是false，都返回false，因此不用判断b了，这个时候刚好判断到a，因此返回a。<br>
　　　    如果a是true，那么就要再判断b，和刚刚一样，不管b是true是false，都返回b。<br>
`a || b`：如果a是true，那么b不管是true还是false，都返回true。因此不用判断b了，这个时候刚好判断到a，因此返回a。<br>
　　    　如果a是false，那么就要判断b，如果b是true，那么返回true，如果b是false，返回false，其实不就是返回b了吗。<br>

### 十、运算符优先级
```javascript
// 假设val已经声明，可定义为任何值
console.log('Value is ' + (val != '0') ? 'define' : 'undefine');  // => ???
```
分析：加号优先级高于三目运算，低于括号。所以括号中无论真假，加上前边的字符串都为true，三目运算为true是输出 `define`。<br>

### 十一、遍历数组arr，剔除数组中为0的元素，最终会被剔除掉几个0
```javascript
var arr = [0,0,2,0,0,4,0,1,0,0,0,2];
for (var i = 0; i < arr.length; i++) {
  if (arr[i] == 0)
  arr.splice(i, 1);
}
```
分析：`splice()`方法会改变原始数组。<br>
第一次：`i=0`,`arr[0]`为0，删除，数组`[0,2,0,0,4,0,1,0,0,0,2]`；<br>
第二次：`i=1`,`arr[1]`为2，不删除；<br>
第三次：`i=2`,`arr[2]`为0，删除，数组`[0,2,0,4,0,1,0,0,0,2]`；<br>
第四次：`i=3`,`arr[3]`为4，不删除；<br>
第五次：`i=4`,`arr[4]`为0，删除，数组`[0,2,0,4,1,0,0,0,2]`；<br>
第六次：`i=5`,`arr[5]`为0，删除，数组`[0,2,0,4,1,0,0,2]`；<br>
第七次：`i=6`,`arr[6]`为0，删除，数组`[0,2,0,4,1,0,2]`；<br>
第八次：`i=7`,`arr[7]`不存在，结束循环；<br>
总共删除了5个0。<br>
```javascript
// 解决：删除所有0
var arr = [0,0,2,0,0,4,0,1,0,0,0,2];
for (var i = 0; i < arr.length; i++) {
  if (arr[i] == 0)
  arr.splice(i--, 1);
}
```

### 十二、有函数F，以下call及apply方法写法错误的是
```
A: F.apply(this, "")
B: F.apply(undefined, [])
C: F.call(null, [])
D: F.call(null, null)
```
正确答案：A<br>
分析：`apply()`方法需要以数组形式一次性传入所有调用函数。<br>
`call()`方法必须详细列出每个参数，C选项中只传了一个参数，只不过是数组形式。<br>

### 十三、toString()
```javascript
3.toString()     // => ???
3..toString()    // => ???
3...toString()   // => ???
```
分析：因为小数点是数字的有效部分，所以第一个点被认为是数字，第二个点是链接。得到的结果分别是 `ERROR` ，`3` ，`ERROR`。<br>
**数字转换为字符串：**`toString()` 或者 `String()`<br>
```javascript
num.toString();
num.toString(2)  // 二进制
```
（1）`null`和`undefined`转换需要用`String()`<br>
（2）对于`Number`和`Boolean`，`String()`和`toString()`相同<br>

### 十四、["1","2","3"].map(parseInt)
（1）`map()`方法：按照原始数组元素顺序依次处理元素，返回一个新数组。<br>
（2）`parseInt(string, radix)`<br>
```javascript
string:必须，要被解析的字符串
radix:可选，表示要解析的数字基数
```
当忽略参数 `radix` , 默认数字的基数如下:<br>
- 如果 `string` 以 "0x" 开头，`parseInt()` 会把 string 的其余部分解析为十六进制的整数。
- 如果 `string` 以 0 开头，那么 ECMAScript v3 允许 `parseInt()` 的一个实现把其后的字符解析为八进制或十六进制的数字。
- 如果 `string` 以 1 ~ 9 的数字开头，`parseInt()` 将把它解析为十进制的整数。

```javascript
parseInt(1, 0);//将十进制数1转化为十进制数，仍为1
parseInt(2, 1);//将一进制数2（不存在）转化为十进制数，NaN
parseInt(3, 2);//NaN
parseInt(4, 3);//NaN
parseInt(10, 4);//将四进制数10转化为十进制数为4
```
`arr.map(parseInt)`含义为对数组`arr`的每一项调用`parseInt()`方法，传入的参数为每一项的值和该值的索引。<br>
```
["1", "2", "3"].map(parseInt)  等价于
[parseInt("1", 0), parseInt("2", 1), parseInt("3", 2)]
```
结果为：`[1,NaN,NaN]`<br>
