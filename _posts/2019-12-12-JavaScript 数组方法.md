---
layout:     post
title:      JavaScript 数组方法
date:       2019-12-12
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、JavaScript 数组方法有哪些（★）
**1、push()：**向数组的末尾添加一个或者多个元素，并返回新的长度。<br>

![push.png](/instructPic/push.png)

**2、pop()：**用于删除并返回数组的最后一个元素。<br>

![pop.png](/instructPic/pop.png)

**3、unshift()：**可向数组的开头添加一个或多个元素，并返回新的长度。<br>

![unshift.png](/instructPic/unshift.png)

**4、shift()：**用于把数组的第一个元素从其中删除，并返回第一个元素的值。<br>

![shift.png](/instructPic/shift.png)

**5、isArray()：**用来判断一个对象是不是数组，返回 true/false。<br>

![isArray.png](/instructPic/isArray.png)

**6、map()：**返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。`map()`方法按照原始数组元素顺序依次处理元素。参数：currentValue 当前元素的值、index 当前元素的索引值、arr 当前元素属于的数组对象。<br>

![map.png](/instructPic/map.png)

**7、filter()：**创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。`filter()`不会对空数组进行检测，不会改变原始数组。参数：currentValue 当前元素的值、index 当前元素的索引值、arr 当前元素属于的数组对象。<br>

![filter.png](/instructPic/filter.png)

**8、every()：**用于检测数组所有元素是否都符合指定条件（通过函数提供）。`every()`方法使用指定函数检测数组中的所有元素：<br>
（1）如果数组中检测到有一个元素不满足，则整个表达式返回 false，且剩余的元素不会再进行检测。<br>
（2）如果所有元素都满足条件，则返回 true。<br>
`every()`不会对空数组进行检测，不会改变原始数组。参数：currentValue 当前元素的值、index 当前元素的索引值、arr 当前元素属于的数组对象。<br>

![every.png](/instructPic/every.png)

**9、some()：**用于检测数组中的元素是否满足指定条件（函数提供）。`some()`方法会依次执行数组的每个元素：<br>
（1）如果有一个元素满足条件，则表达式返回 true，剩余的元素不会再执行检测。<br>
（2）如果没有满足条件的元素，则返回 false。<br>
`some()`不会对空数组进行检测，不会改变原始数组。参数：currentValue 当前元素的值、index 当前元素的索引值、arr 当前元素属于的数组对象。<br>

![some.png](/instructPic/some.png)

**10、splice()：**用于添加或删除数组中的元素。会改变原始数组。如果仅删除一个元素，则返回一个元素的数组。如果未删除任何元素，则返回空数组。参数：index 必须，规定从何处添加/删除元素。该参数是开始插入和删除的数组元素的下标，必须是数字。howmany 可选，规定应该删除多少元素。必须是数字，但可以是 “0”，如果未规定此参数，则删除从 index 开始到原数组结尾的所有元素。item…itemX 可选，要添加到数组的新元素。<br>

![splice1.png](/instructPic/splice1.png)

![splice2.png](/instructPic/splice2.png)

![splice3.png](/instructPic/splice3.png)

**11、slice()：**可从已有的数组中返回选定的元素。`slice()`方法可提取字符串的某个部分，并以新的字符串返回被提取的部分，不会改变原始数组。参数：start 规定从何处开始选取。如果该参数为负数，则表示从原数组的倒数第几个元素开始提取，slice(-2) 表示提取原数组中的倒数第二个元素到最后一个元素（包含最后一个元素）。end 规定从何处结束选取。没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果该参数为负数，则它表示在原数组中的倒数第几个元素结束抽取。<br>

![slice1.png](/instructPic/slice1.png)

![slice2.png](/instructPic/slice2.png)

**12、indexOf()：**可返回某个指定的字符串值在字符串中首次出现的位置。如果没有找到匹配的字符串则返回 -1。`indexOf()`方法区分大小写。参数：search value 必须，规定需要检查的字符串值 start 可选，规定在自负串开始检索的位置，如果省略，则将从字符串的首字符开始检索。<br>

![indexOf.png](/instructPic/indexOf.png)

**13、includes()：**判断一个数组是否包含一个指定的值，返回 true/false。参数：searchElement 必须，需要查找的元素值 fromIndex 从该索引处开始查找 searchElement。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索。默认为 0。<br>

![includes.png](/instructPic/includes.png)

**14、concat()：**用于连接两个或多个数组。该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。<br>

![concat.png](/instructPic/concat.png)

**15、join()：**用于把数组中的所有元素放入一个字符串。该元素是通过指定的分隔符进行分隔的。<br>

![join.png](/instructPic/join.png)

**16、split()：**用于把一个字符串分割成字符串数组，不改变原始字符串。参数：separator 可选。字符串或正则表达式，从该参数指定的地方分割 string Object。limit 可选。该参数可指定返回的数组的最大长度。<br>

![split.png](/instructPic/split.png)

**17、forEach()：**用于调用数组的每个元素，并将元素传递给回调函数。<br>

![forEach.png](/instructPic/forEach.png)

**18、sort()：**用于对数组的元素进行排序。数组在原数组上进行排序，不生成副本。<br>

![sort.png](/instructPic/sort.png)

**19、reverse()：**用于颠倒数组中元素的顺序。<br>

![reverse.png](/instructPic/reverse.png)

**20、find()：**返回通过测试（函数内判断）的数组的第一个元素的值。`find()`方法为数组中的每个元素都调用一次函数执行：<br>
（1）当数组中的元素在测试条件时返回 true 时，`find()`返回符合条件的元素，之后的值不会再调用执行函数。<br>
（2）如果没有符合条件的元素返回 undefined。<br>
`find()`对于空数组，函数是不会执行的，不改变数组的原始值。<br>

![find.png](/instructPic/find.png)

**21、findIndex()：**返回传入一个测试条件（函数）符合条件的数组第一个元素位置。`findIndex()`方法为数组中的每个元素都调用一次函数执行：<br>
（1）当数组中的元素在测试条件时返回 true 时，`findIndex()`返回符合条件的元素的索引位置，之后的值不会再调用执行函数。<br>
（2）如果没有符合条件的元素返回 -1。<br>
`findIndex()`对于空数组，函数是不会执行的，不改变数组的原始值。<br>

![findIndex.png](/instructPic/findIndex.png)

**22、fill()：**用于将一个固定值替换数组的元素。参数：value 必须，填充的值；start 可选，开始填充的位置
；end 可选，停止填充的位置，默认为 array.length。<br>

![fill.png](/instructPic/fill.png)

**23、keys()、values()、entries()：**`keys()`是对键名的遍历、`values()`对键值的遍历、`entries()`是对键值对的遍历。<br>

![entries.png](/instructPic/entries.png)

### 二、forEach() 和 map() 的区别？循环能否中断？（★）
**1、相同点：**<br>
（1）都只能遍历数组。<br>
（2）参数相同：item（当前每一项）、index（索引值）、arr（原数组），`forEach()`和`map()`都相当于封装好的单层 for 循环。<br>
（3）匿名函数中的 this 都是指向 window。<br>
（4）都是循环遍历数组中的每一项。<br>
**2、不同点：**`map()`会分配内存空间存储新数组并返回，不会改变原数组，`forEach()`不会返回数据，会改变原数组。<br>
3、`forEach()`和`map()`本身不可以终止循环。本身指的是通过条件使用`return`或者`break`跳出循环。但是可以用抛出 throw new Error()，通过 try catch 去捕获这个错误去终止循环。<br>
```javascript
let list = [1, 2, 3, 4, 5, 6];
try{
  list.map(item => {
    if (item === 3) {
      throw new Error()
    }
    console.log(item)
  })
} catch {}
// 输出结果：1 2
```

### 三、JavaScript 获取数组最后一项有哪些方法（★）
**1、length**<br>
```javascript
let arr = [1, 2, 3];
arr[arr.length - 1] // 3
```

**2、pop()，删除数组的最后一位并返回，会修改原数组。**<br>
```javascript
let arr = [1, 2, 3];
arr.pop() // 3
```

**3、slice()，返回值为包含最后一位元素的新数组。**<br>
```javascript
let arr = [1, 2, 3];
arr.slice(-1) // [3]
```

**4、at()，ES2022 新特性。**<br>
```javascript
let arr = [1, 2, 3];
arr.at(-1) // 3
```

**5、reverse()，可以用于颠倒数组中元素的顺序，最前面的元素会变成最后面的元素。**<br>
```javascript
let arr = [1, 2, 3];
arr.reverse()[0]
```