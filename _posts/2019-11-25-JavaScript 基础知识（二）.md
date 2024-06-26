---
layout:     post
title:      JavaScript 基础知识（二）
date:       2019-11-25
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、闭包是什么？有什么优点和缺点？（★）
```javascript
function fn() { // 外部函数
  var a = 5;
  return function () { // 内部函数
    console.log(a);
  }
}
```
闭包是指**有权访问另一个函数作用域中的局部变量的函数**。当其中一个内部函数在外部函数以外被调用时，就会形成闭包。<br>
**作用：**通过一系列方法，将函数内部的变量（局部变量）转化为全局变量。<br>
**优点：**① 能够读取函数内部的局部变量；②闭包就是函数的局部变量集合，让这些变量<font color="red">一直存在于内存中</font>，不会在调用结束后被垃圾回收机制回收；③不增加额外的全局变量。<br>
**缺点：**由于闭包会使函数中的变量存在于内存中，<font color="red">内存消耗很大，所以不能滥用闭包</font>。解决的办法是退出函数之前，将不使用的局部变量删除。<br>

### 二、this 的指向问题
1、普通函数执行，内部 this 指向全局对象 window。<br>
2、函数在定义的时候 this 是不确定的，只有在调用的时候可以确定。<br>
3、如果函数作为一个对象的方法被该对象调用，那么函数内的 this 则指向该对象。<br>
4、构造函数中的 this 是一个隐式对象，类似一个初始化的类型，所有方法和属性都挂载到了这个隐式对象身上，后续通过 new 关键字来调用，从而实现实例化。**构造函数中的 this 指向的是实例化之后的对象。**<br>

### 三、改变 this 指向的方法
箭头函数不能改变 this 指向。箭头函数内部没有 this 指向，箭头函数的 this 指向父级作用域的 this，如果没有则 this 指向 window。<br>
#### 1、通过 call/apply/bind 函数显示改变 this 指向。
语法：函数.call()、函数.apply()、函数.bind()。<br>
第一个参数相同：都代表 this 要指向的对象（若该参数为 undefined 或 null 或不传参，this 则默认指向全局 window）。<br>
除第一个传参外的其他参数不同：<br>
- call() 是参数列表 arguments
- apply() 是数组
- bind() 可以分多次传入，实现参数合并

（1）函数调用 call/apply/bind 改变 this 指向。<br>
```javascript
function fn (a, b) {
  console.log(this, a, b)
}
let obj = { name: 'violetks', age: 18 };

fn.call(obj, 666, 999);
fn.apply(obj, [666, 999]);
fn.bind(obj, 666, 999)(); // 因为调用 bind 后返回的是个函数，要想执行需要加 () 调用
```
函数 fn 中的 this 原本是指向 window，调用 call/apply/bind 方法会让 this 指向 obj，同时将 666、999 传给 a、b。<br>

（2）对象里的方法调用 call/apply 改变 this 指向。<br>
```javascript
let obj = {
  name: 'violetks',
  age: 18,
  study: function () {
    console.log(this);
  }
};
let person = { name: '张三', age: 34 };

obj.study.call(person);
obj.study.apply(person);
```
obj 对象里的 study 方法里的 this 原本是指向 obj，调用 call/apply 方法会让 this 变为指向 person。<br>

（3）call、apply、bind 的区别。<br>
**执行时机：**call()、apply() 是立即执行。bind() 不是立即执行，bind() 是返回绑定 this 之后的新函数，需要手动调用；如果这个新函数作为**构造函数**被调用，那么 this 不再指向传入 bind() 的第一个参数，而是指向新生成的对象。

#### 2、通过对象调用方法，指向对象本身。
在全局环境中调用一个函数，函数内部的 this 指向的是全局变量 window。<br>
通过一个对象来调用其内部的一个方法，该方法的执行上下文中的 this 指向对象本身。<br>
```javascript
var myObj = {
  name: 'violetks',
  showThis: function () {
    console.log(this)
  }
};
myObj.showThis()
```
通过 myObj 对象调用 showThis 方法，最终输出的 this 指向 myObj。<br>
原因：JS 执行引擎在执行`myObj.showThis()`时，实际上是将其转化成`call`函数执行，代码如：<br>
```javascript
myObj.showThis.call(myObj);
```

### 四、给 DOM 元素绑定事件有哪几种方法
1、使用内联，优先级最高，一个元素只能添加一个事件。<br>
```javascript
<input type="button" value="btn" onclick="alert('内联！');">
```
2、使用元素的`.onclick`方法，一个元素只能添加一个事件。<br>
```javascript
<input type="button" value="btn">
<script>
  var btn = document.getElementsByTagName("input")[0];
  btn.onclick = function(){
    alert('.onclick');
  }
</script>
```
3、使用事件监听`addEventListener`（IE 使用 attachEvent），给一个元素添加多个事件。<br>
```javascript
<input type="button" value="btn">
<script>
  var btn = document.getElementsByTagName("input")[0];
  btn.addEventListener("click",function(){
    alert('事件一');
  })
  btn.addEventListener("click",function(){
    alert('事件二');
  })
</script>
```

### 五、什么是 window 对象？什么是 document 对象？
**window 对象**是指浏览器打开的窗口。<br>
**document 对象**是文档对象，window 对象的一个属性。

### 六、document.write 和 innerHTML 的区别
`document.write`直接将内容写入页面的内容流，会导致重绘整个页面。<br>
`innerHTML`将内容写入某个 DOM 节点，可以重绘页面的一部分。<br>
#### innerHTML、innerText、outerHTML、outerText
- `innerHTML`：设置或获取标签所包含的 HTML 与文本信息。（不含标签本身）
- `innerText`：设置或获取标签所包含的文本信息。（不含标签本身）
- `outerHTML`：设置或获取标签本身以及所包含的 HTML 与文本信息。（包含标签本身）
- `outerText`：设置或获取标签本身以及所包含的文本信息。（包含标签本身）

```javascript
<div id="div1">
  qwe<p>content</p>

</div>

<script>
  var ele = document.getElementById("div1");
  ele.innerHTML    // qwe<p>content</p>
  ele.innerText    // qwecontent
  ele.outerHTML    // <div>qwe<p>content</p></div>
  ele.outerText    // qwecontent
</script>
```

### 七、JS 获取 HTML 元素的 8 种方法
- 通过 ID 获取（getElementById）
- 通过 name 属性（getElemsentsByName）
- 通过标签名（getElementsByTagName）
- 通过类名（getElementsByClassName）
- 通过选择器获取一个元素（querySelector）
- 通过选择器获取一组元素（querySelectorAll）
- 获取 HTML 标签（document.documentElement）
- 获取 body 元素（document.body）

### 八、JS 操作 DOM 的方法
（1）创建节点<br>
```css
createElement("标签名")    //创建一个具体的元素
createTextNode()           //创建一个文本节点
createDocumentFragment()   //创建一个 DOM 片段
createAttribute("属性名")  //创建一个属性节点
```
（2）插入节点<br>
`父节点.appendChild(新的子节点);`，在父节点的最后插入一个新的子节点。<br>
`父节点.insertBefore(新的子节点,参考节点);`，在参考节点前插入一个新节点，如果参考节点为 null，将在父节点最后插入一个子节点。<br>
（3）删除节点：父节点.removeChild(子节点)，想删除自己<br>
```css
node.parentNode.removeChild(node);
```
（4）复制节点：<br>
```css
node.cloneNode();     //只复制节点本身，不复制子节点
node.cloneNode(true); //既复制节点本身，也复制其所有的子节点
```
（5）替换节点：`replaceChild(new,old)`<br>
（6）获取节点的属性值<br>
方式一：**元素节点.属性**，比如：node.src，node.className，node.title。<br>
方式二：`node.getAttribute("属性名")`<br>
（7）设置节点的属性值<br>
方式一：`node.src = ""`<br>
方式二：`node.setAttribute(属性名，新的属性值)`<br>
（8）删除节点属性：`node.removeAttribute(属性名)`

#### DOM 中的三种节点类型
元素节点、文本节点、属性节点

#### DOM 元素常用属性
- ele.parentNode：返回当前元素的父节点
- ele.children：返回当前元素的所有子节点，只返回 HTML 节点
- ele.childNodes：返回当前元素的所有子节点，包括文本、HTML、属性节点。（回车也会当做一个节点）
- ele.firstChild：返回当前元素的第一个子节点对象
- ele.lastChild：返回当前元素的最后一个子节点对象
- ele.nextSibling：返回当前元素之后的下一个兄弟节点（包括文本节点、注释节点）
- ele.previousSibling：返回当前元素之前的上一个兄弟节点（包括文本节点、注释节点）
- ele.nextElementSibling：返回当前元素之后的下一个兄弟元素（不包括文本节点、注释节点）
- ele.previousElementSibling：返回当前元素之前的上一个兄弟元素（不包括文本节点、注释节点）

### 九、JS 中开头 "use strict" 是什么意思
`"use strict"`是一种 ECMAscript 5 添加的严格运行模式，这种模式使得 Javascript 在更严格的条件下运行。<br>
**严格模式的限制：**<br>
（1）不允许使用未声明的变量（对象也是一个变量）。<br>
（2）不允许删除变量或对象。<br>
（3）不允许删除函数。<br>
（4）不允许删除一个不允许删除的属性。<br>
（5）不允许变量重名。<br>
（6）不允许使用八进制、转义字符。<br>
（7）不允许对只读属性赋值。<br>
```javascript
"use strict";
var obj = {};
Object.defineProperty(obj,"x",{value:0,writable:false});
obj.x = 3.14     //报错
```
（8）不允许对一个使用 getter 方法读取的属性进行赋值。<br>
```javascript
"use strict";
var obj = {};
var obj = {get x() {return 0}};
obj.x = 3.14     //报错
```
（9）不能使用`eval`、`arguments`作为变量名。<br>
（10）由于一些安全原因，在作用域`eval()`创建的变量不能被调用。<br>
```javascript
"use strict";
eval("var x = 2");
alert(x);       //报错
```
**（11）禁止 this 关键字指向全局对象。**<br>
**为什么使用严格模式：**<br>
消除 JavaScript 语法的一些不合理、不严谨之处，减少一些怪异行为；消除代码运行的一些不安全之处，**保证代码运行的安全**；提高编译器效率，增加运行速度；另一方面，同样的代码，在**严格模式**中，可能会有不一样的运行结果；一些在“正常模式”下可以运行的语句，在“严格模式”下将不能运行。<br>

### 十、JS 中 eval 的功能
把对应的字符串解析成 JS 代码并运行。

### 十一、window.onload 和 $(document).ready() 的区别
- `window.onload=function(){}`方法是在网页中所有的元素（包括元素的所有关联文件）完全加载到浏览器后才执行的。一个页面中不能同时编写多个。
- `$(document).ready()`可以在 DOM 载入就绪时就对其进行操纵，并调用执行绑定的函数。同一页面能同时编写多个，简写为`$(function(){})`。

### 十二、JSON 与 JS 对象的转换
- `JSON.parse()`: JSON 字符串转换为 JS 对象
- `JSON.stringify()`: JS 对象转换成 JSON 字符串

### 十三、JS 异步处理的几种方式
**（1）回调函数（callback）**<br>
**概念**：回调是函数 A 被作为一个参数传递到函数 B 里，在函数 B 执行完后再执行。<br>
**优点**：回调函数是异步编程最基本的方法，简单、容易理解和部署。<br>
**缺点**：不利于代码的阅读和维护，各个部分之间高度耦合，流程会很混乱，而且每个任务只能指定一个回调函数。<br>
**注意区分**：回调函数和异步 回调并不一定就是异步。并没有直接关系。<br>
**（2）事件监听**<br>
采用事件驱动模式。任务的执行不取决代码的顺序，而**取决于某一个事件是否发生**。<br>
**监听函数有：on，bind，listen，addEventListener，observe**<br>
```javascript
// on
f1.on('done',f2);
// 或者
function f1(){
  setTimeout(function(){
    // f1 的任务代码
    f1.trigger('done');  // 执行完成后，立即触发 done 事件，开始执行 f2
  },1000);
}
```
**优点**：容易理解，可以绑定多个事件，每一个事件可以指定多个回调函数，而且可以去耦合，有利于实现模块化。<br>
**缺点**：整个程序都要变成事件驱动型，运行流程会变得不清晰。<br>
```javascript
element.onclick=function(){}
```
**优点**：写法兼容到主流浏览器<br>
**缺点**：当同一个 element 元素绑定多个事件时，只有最后一个事件会被添加，相当于一次只能添加一个事件。<br>
```javascript
//IE:attachEvent;三个方法执行顺序：3-2-1
elment.attachEvent("onclick",handler1);
elment.attachEvent("onclick",handler2);
elment.attachEvent("onclick",handler3);
```
```javascript
//标准 addEventListener;执行顺序：1-2-3
elment.addEvenListener("click",handler1,false);
elment.addEvenListener("click",handler2,false);
elment.addEvenListener("click",handler3,false);
```
**注意**：该方法的第三个参数是冒泡获取，是一个布尔值：当为 false 时表示由里向外（冒泡），true 表示由外向里（捕获）。<br>
**（3）发布/订阅（又称观察者模式）**<br>
```javascript
//jQuery 的一个插件 Ben Alman 的 Tiny Pub/Sub
jQuery.subscribe("done", f2);    //f2 向 jQuery 订阅 "done"
jQuery.unsubscribe("done", f2);  //取消订阅
//或者
function f1(){
  setTimeout(function () {
    // f1 的任务代码
    jQuery.publish("done");//f1 执行完成后，向 jQuery 发布 "done" 信号，从而引发 f2 的执行
  }, 1000);
}
```
**（4）promise 对象（promise 模式）**<br>
**promise 是一种模式，promise 可以帮忙管理异步方式返回的代码**。将代码进行封装并添加一个类似于事件处理的管理层。我们可以使用 promise 来注册代码，这些代码会在在 promise **成功或者失败后运行**。<br>
我们可以注册任意数量的函数在成功或者失败后运行，也可以在任何时候注册事件处理程序。<br>
**promise 有两种状态：1、等待（pending）；2、完成（settled）**。promise 会一直处于等待状态，直到它所包装的异步调用返回/超时/结束。这时 promise 状态变成完成。**完成状态分成两类：解决（resolved）；拒绝（rejected）**。<br>
`resolve()`函数说明 promise 已解决；`reject()`函数说明 promise 未能顺利完成。then 和 catch 相当于 onsucess 和 onfailure 事件的处理程序。<br>
```javascript
f1().then(f2).then(f3);
f1().then(f2).fail(f3);
```
**优点**：回调函数写成了**链式写法**，程序的流程可以看得很清楚，而且有一整套的配套方法，可以实现很多强大的功能。**如果一个任务已经完成，再添加回调函数，该回调函数会立即执行**。<br>
**缺点**：编写和理解都相对比较难。<br>
**（5）async/await**<br>
函数前面多加`async`关键字，函数返回的是一个 **Promise** 对象，所接收的值就是函数 **return** 的值。<br>
在`async`函数内部使用`await`命令，表示等待一个异步函数的返回。`await`后面跟着的是一个 **Promise** 对象，如果不是的话，系统会调用`Promise.resolve()`方法，将其转为一个`resolve`的 **Promise** 对象。<br>
```javascript
let bar = async function(){
    try{
        await Promise.reject('error')
    }catch(e){
        console.log(e)
    }
}
```

### 十四、JS 设置 CSS 样式的几种方式
**（1）设置 style 属性：**元素.style.样式属性="样式值"<br>
**（2）className 属性：**把动态调整后的样式先写在 CSS 样式表里<br>
```javascript
<style>
  .out{background-color:purple;}
  .over{background-color:yellow;}
</style>

<ul>
  <li>首页</li>

  <li>用户管理</li>

  <li>购物车</li>

</ul>

<script>
  var arr = document.getElementByTagName("li");
  for(var i = 0;i<arr.length;i++){
      arr[i].onmouseover = function(){
          this.className='over';
      }
      arr[i].onmouseout = function(){
          this.className='out';
      }
  }
</script>
```
**（3）setAttribute**<br>
```javascript
ele.setAttribute('height', 100);
ele.setAttribute('style','width: 100px !important');
```
**（4）使用 setProperty**<br>
```javascript
ele.style.setProperty('height', '300px', 'important');
```
**（5）设置 cssText**<br>
```javascript
element.style.cssText = 'height: 100px !important';
element.style.cssText += 'height: 100px !important';
```

### 十五、new Date() 获取的时间是什么时间
**运行环境的时间**：如果是**浏览器**那就是电脑时间或者手机时间；如果是 **node.js** 那就是服务器时间。<br>
前台获取的时间都用处不大、用户可以改、不安全。通常时间用于从后端传入、服务器的时间一定是统一的、解析时间戳。<br>
JS 中单独调用 new Date()，例如 document.write(new Date())，显示的结果是：Mar 31 10:10:43 UTC+0800 2012 这种格式的时间。但是用 new Date() 参与计算会自动转换为从 1970.1.1 开始的毫秒数。<br>
#### JS 获取当前时间戳
```javascript
// 方法一
var timestamp = Date.parse(new Date());
// 注意：Date.parse() 得到的结果将后三位（毫秒）转换成了 000 显示，使用时可能会出现问题。例如动态添加页面元素 id 的时候，不建议使用。

// 方法二
var timestamp = (new Date()).valueOf();
// 方法三
var timestamp = new Date().getTime();
// 方法四
var timestamp =+new Date();
```