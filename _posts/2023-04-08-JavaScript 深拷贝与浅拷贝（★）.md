---
layout:     post
title:      JavaScript 深拷贝与浅拷贝（★）
date:       2023-04-08
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、深拷贝与浅拷贝
如何区分深拷贝与浅拷贝，简单点来说，就是假设 B 复制了 A，当修改 A 时，看 B 是否会发生变化，如果 B 也跟着变了，说明这是浅拷贝，如果 B 没变，那就是深拷贝。<br>
```javascript
// 浅拷贝：
let a = [0, 1, 2, 3, 4];
let b = a;
console.log(a === b);
a[0] = 1;
console.log(a, b);
```

![浅拷贝.png](/instructPic/浅拷贝.png)

### 二、基本数据类型和引用数据类型的数据存储方式：栈和堆
**1、基本数据类型——名值存储在栈内存中，**例如`let a = 1;`<br>
![栈内存1.jpg](/instructPic/栈内存1.jpg)

当`b = a`复制时，栈内存会新开辟一个内存：<br>
![栈内存2.jpg](/instructPic/栈内存2.jpg)

所以修改`a = 2`不会影响 b，但这也算不上深拷贝，因为深拷贝本身只针对较为复杂的 object 类型数据。<br>

**2、引用数据类型——名存在栈内存中，值存在堆内存中，栈内存提供一个引用的地址指向堆内存中的值。**<br>
![堆内存1.jpg](/instructPic/堆内存1.jpg)

当`b = a`进行拷贝时，其实复制的是 a 的引用地址，而并非堆里面的值。<br>
![堆内存2.jpg](/instructPic/堆内存2.jpg)

`a[0] = 1`进行数组修改时，由于 a 与 b 指向的是同一个地址，所以 b 也受影响，这就是浅拷贝。<br>
![堆内存3.jpg](/instructPic/堆内存3.jpg)

要是在堆内存中也开辟一个新的内存专门为 b 存放值，就像基本类型那样，岂不就达到深拷贝的效果了。<br>
![堆内存4.jpg](/instructPic/堆内存4.jpg)

### 三、实现深拷贝的方法（★）
1、递归复制**所有层级属性**。<br>
```javascript
function deepClone (obj) {
  let objClone = Array.isArray(obj) ? [] : {};
  if (obj && typeof obj === "object") {
    for (key in obj) {
      if (obj.hasOwnProperty(key)) {
        // 判断 obj 子元素是否为对象，如果是，递归复制
        if (obj[key] && typeof obj[key] === "object") {
          objClone[key] = deepClone(obj[key]);
        } else {
          // 如果不是，简单复制
          objClone[key] = obj[key];
        }
      }
    }
  }
  return objClone;
}
let a = [1, 2, 3, 4];
let b = deepClone(a);
a[0] = 2;
console.log(a, b);
```

![深拷贝1.png](/instructPic/深拷贝1.png)

2、借用 JSON 对象的`parse`和`stringify`。<br>
```javascript
let a = [0, 1, [2, 3], 4];
let b = JSON.parse(JSON.stringify(a));
a[0] = 1;
a[2][0] = 1;
console.log(a, b);
```

![深拷贝2.png](/instructPic/深拷贝2.png)

3、借用 JQuery 的`extend`方法。<br>
```javascript
$.extend([deep], target, object1, ..., objectN)
```

- `deep`：是否深拷贝，true/false。
- `target`：Object 类型，目标对象，其他对象的成员属性将被附加到该对象上。
- `object1, ..., objectN`：Object 类型，可选，被合并的对象。

```javascript
let a = [0, 1, [2, 3], 4];
let b = $.extend(true, [], a);
a[0] = 1;
a[2][0] = 1;
console.log(a, b);
```

![深拷贝3.png](/instructPic/深拷贝3.png)

4、借用 Lodash（JavaScript工具库）中的`_.cloneDeep`。<br>

### 四、以下方法不是深拷贝
**深拷贝是拷贝对象各个层级的属性。**<br>
```javascript
let a = [1, 2, 3, 4];
let b = a.slice();
a[0] = 2;
console.log(a, b);
```

![假拷贝1.png](/instructPic/假拷贝1.png)

```javascript
let a = [0, 1, [2, 3], 4];
let b = a.slice();
a[0] = 1;
a[2][0] = 1;
console.log(a, b);
```

![假拷贝2.png](/instructPic/假拷贝2.png)

b 对象的一级属性确实不受影响了，但是二级属性还是没能拷贝成功，仍然脱离不了 a 的控制，说明`slice`根本不是真正的深拷贝。<br>

![假拷贝3.jpg](/instructPic/假拷贝3.jpg)

第一层的属性确实深拷贝，拥有了独立的内存，但更深的属性却仍然公用了地址，所以才会造成上面的问题。<br>
`concat`与`slice`都不是真正的深拷贝。<br>