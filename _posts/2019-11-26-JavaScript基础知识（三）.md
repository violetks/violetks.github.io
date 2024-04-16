---
layout:     post
title:      JavaScript基础知识（三）
date:       2019-11-26
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、数组和伪数组的区别
**1、数组是一个特殊对象，与常规对象的区别：**<br>
（1）当有新元素添加到列表中时，自动更新 length 属性。<br>
（2）可以通过设置 length 属性改变数组长度。<br>
（3）能继承 Array.prototype 中的方法。<br>
（4）是 Array 类型。<br>
**2、伪数组的特点：**<br>
（1）伪数组又称为类数组，类似数组的对象。<br>
（2）具有 length 属性，但 length 属性不是动态的，不会随着成员的变化而变化。<br>
（3）和数组一样，按索引方式存储数据。<br>
（4）没有数组的 push、forEach 等方法。<br>
（5）伪数组本质是一个 Object，而真实的数组是一个 Array。<br>
**3、常见伪数组**<br>
（1）函数内部的 arguments 对象。<br>

![arguments.jpg](/instructPic/arguments.jpg)

（2）调用 document.getElementsByTagName、document.childNodes 之类，获取的DOM对象列表都属于伪数组。<br>
```html
<div class="nameList">
    <div class="name1">拉拉</div>
    <div class="name2">迪西</div>
    <div class="name3">小波</div>
    <div class="name4">丁丁</div>
</div>
<script>
    let nodeList = document.getElementsByTagName('div');
    console.log(nodeList);
    let childNodes = nodeList[0].childNodes;
    console.log(childNodes);
</script>
```

![DOMNodes.png](/instructPic/DOMNodes.png)

（3）jQuery 中的 $() 方法获取到的全部是伪数组，本质都是HTML标签序列。<br>
```html
<div class="name1">拉拉</div>
<div class="name2">迪西</div>
<div class="name3">小波</div>
<div class="name4">丁丁</div>
<script>
    $(document).ready(function() {
        let dom1 = $('div');
        console.log(dom1, '1111111111111111');
    })
</script>
```

![jQueryList.png](/instructPic/jQueryList.png)

（4）自定义的伪数组。<br>
```javascript
let arrLike = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
```

（5）文件域的 files 属性是一个伪数组。<br>
```html
<input type="file" id="fileList">
<script>
    let fileNode = document.querySelector("#fileList");
    fileNode.addEventListener('change', function() {
        console.log(fileNode.files)
    })
</script>
```

![fileList.png](/instructPic/fileList.png)

**4、伪数组转换为真数组**<br>
（1）声明一个空数组，遍历伪数组元素依次添加到空数组。<br>
```javascript
let arrLike = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
let arr = [];
for (let i = 0; i < arrLike.length; i++) {
    arr.push(arrLike[i])
}
```

（2）使用 Array.prototype.slice.call() 或 Array.prototype.apply()。<br>
（3）使用 ES6 的扩展运算符。<br>
（4）使用 ES6 中数组的新方法 Array.from()。<br>
```javascript
let arrLike = {
    0: 'a',
    1: 'b',
    2: 'c',
    length: 3
};
let arr1 = Array.prototype.slice.call(arrLike);
let arr2 = Array.prototype.slice.apply(arrLike);
let arr3 = [...arrLike];
let arr4 = Array.from(arrLike);
```
**5、转换须知**<br>
（1）转换后的数组长度由length属性决定。索引不连续时转换结果是连续的，会自动补位。<br>
```javascript
let arrLike = {
    length: 4,
    0: 0,
    1: 1,
    3: 3,
    4: 4,
    5: 5,
};
console.log(Array.from(arrLike)) // [0, 1, undefined, 3]
```

（2）仅考虑 0 或正整数的索引。<br>
```javascript
let arrLike = {
    length: 4,
    '-1': -1,
    '0': 0,
    a: 'a',
    1: 1
};
console.log(Array.from(arrLike)); // [0, 1, undefined, undefined]
```

（3）使用 slice 转换产生稀疏数组。<br>
```javascript
let arrLike = {
    length: 4,
    '-1': -1,
    '0': 0,
    a: 'a',
    1: 1
};
console.log(Array.prototype.slice.call(arrLike)); //[0, 1, empty × 2]
```

### 二、判断变量是不是数组，有哪些方法
**1、Array.isArray()**<br>
**（1）准确性：**可以准确判断一个变量是否是数组，不会将其他引用类型误判为数组。<br>
**（2）跨窗口和跨框架支持：**可以在不同的窗口（window）和框架（iframe）中正常工作，不受上下文环境的限制。<br>
```javascript
let arr = [1, 2, 3];
console.log(Array.isArray(arr)); // true

let obj = { name: "John", age: 25 };
console.log(Array.isArray(obj)); // false

let str = "Hello";
console.log(Array.isArray(str)); // false
```

**2、instanceof**<br>
判断逻辑是检查原型链上是否存在构造函数的原型。<br>
```javascript
let arr = [1, 2, 3];
console.log(arr instanceof Array); // true

let obj = { name: "John", age: 25 };
console.log(obj instanceof Array); // false

let str = "Hello";
console.log(str instanceof Array); // false

// 误判情况
function MyArray() {
  // 自定义的类似数组的对象
  this.length = 0;
}
MyArray.prototype = Array.prototype;

const myArr = new MyArray();

console.log(myArr instanceof Array); // true
```

在上述示例中，定义了一个名为 MyArray 的类似数组的对象。该对象具有类似数组的行为，包括 length 属性。通过指定 MyArray.prototype = Array.prototype，使该对象继承了 Array.prototype 上的方法和属性。<br>
然后，创建了一个 myArr 的实例，并使用 instanceof 运算符判断 myArr 是否是数组。判断结果是 true。<br>
因为 instanceof 运算符判断一个对象是否属于某个类时，不仅仅判断对象的构造函数是否是该类，还会检查对象的原型链上是否存在该类的原型。在本例中，myArr 的原型对象为 Array.prototype，因此会被判断为数组。<br>
这种情况下，instanceof 的判断结果会产生误解，因为 myArr 并非通过数组字面量或构造函数创建的真正数组对象。<br>

**3、Object.prototype.toString.call()**<br>
用于获取对象类型的方法，会返回一个表示对象类型的字符串。对于数组类型的变量，返回的字符串是"[object Array]"。<br>
**（1）简单高效：**简单易懂，一行代码即可完成判断，且性能较好。<br>
**（2）兼容性好：**适用于所有标准的 JavaScript 环境。<br>
**（1）准确性高：**由于数组对象继承了 Array.prototype，因此返回的字符串 "[object Array]" 是数组特有的，可以准确判断是否为数组。<br>
```javascript
let arr = [1, 2, 3];
let arrType = Object.prototype.toString.call(arr);
console.log(arrType === "[object Array]"); // true

let obj = { name: "John", age: 25 };
let objType = Object.prototype.toString.call(obj);
console.log(objType === "[ Array]"); // false

let str = "Hello";
let strType = Object.prototype.toString.call(str);
console.log(strType === "[object Array]"); // false
```

**4、Array.prototype.isPrototypeOf()**<br>
判断一个对象是否是另一个对象的原型。<br>
```javascript
let arr = [1, 2, 3];
console.log(Array.prototype.isPrototypeOf(arr)); // true
```

**5、constructor**<br>
判断变量的 constructor 是否等于 Array 来确定是否是数组。<br>
如果对象的 constructor 属性被修改，或者是用户自定义的类，就可能导致判断结果失效。<br>
```javascript
function isArray(obj) {
  return obj.constructor === Array;
}

console.log(isArray([])); // true
console.log(isArray({})); // false

// 误判情况
let obj = { name: "John", age: 25 };
console.log(isArray(obj)); // false

obj.constructor = Array;
console.log(isArray(obj)); // true
```

**6、typeof**<br>
typeof 运算符会将数组、null 和其他引用类型都归类为 "object"，而无法准确判断一个对象是否是数组。这是因为在 JavaScript 中，数组是一种特殊的对象类型。<br>
```javascript
let arr = [1, 2, 3]
let obj = { name: 'John' }
console.log(typeof arr === 'object' && typeof arr.length === 'number');  // true
console.log(typeof obj === 'object' && typeof obj.length === 'number');  // false
```