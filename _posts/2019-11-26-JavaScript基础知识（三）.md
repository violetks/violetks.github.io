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