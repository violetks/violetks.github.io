---
layout:     post
title:      JavaScript基础知识（二）
date:       2019-11-16
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、给DOM元素绑定事件有哪几种方法？
（1）使用内联，优先级最高，一个元素只能添加一个事件；<br>
```javascript
<input type="button" value="btn" onclick="alert('内联！');">
```
（2）使用元素的`.onclick`方法，一个元素只能添加一个事件；<br>
```javascript
<input type="button" value="btn">
<script>
  var btn = document.getElementsByTagName("input")[0];
  btn.onclick = function(){
    alert('.onclick');
  }
</script>
```
（3）使用事件监听`addEventListener`（IE使用attachEvent），给一个元素添加多个事件。<br>
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

### 二、闭包是什么？有什么优点和缺点？
解答：闭包是指**有权访问另一个函数作用域中的局部变量的函数**。当其中一个内部函数在外部函数以外被调用时，就会形成闭包。<br>
**优点：**① 能够读取函数内部的局部变量；②闭包就是函数的局部变量集合，让这些变量<font color="red">一直存在于内存中</font>，不会在调用结束后被垃圾回收机制回收；③不增加额外的全局变量。<br>
**缺点：**由于闭包会使函数中的变量存在于内存中，<font color="red">内存消耗很大，所以不能滥用闭包</font>；解决的办法是退出函数之前，将不使用的局部变量删除。<br>

### 三、this的指向问题？
（1）普通函数执行，内部this指向全局对象window。<br>
（2）函数在定义的时候this是不确定的，只有在调用的时候可以确定。<br>
（3）如果函数作为一个对象的方法被该对象调用，那么函数内的this则指向该对象。<br>
（4）构造函数中的this是一个隐式对象，类似一个初始化的类型，所有方法和属性都挂载到了这个隐式对象身上，后续通过new关键字来调用，从而实现实例化。<br>
#### 改变this指向的方法：
- 使用箭头函数<br>
- 在函数内部使用`_this=this;`<br>
- 使用call、apply、bind<br>
[JS中的call、apply、bind方法详解](https://www.cnblogs.com/moqiutao/p/7371988.html)
- new实例化一个对象<br>

```javascript
var a = 11;
function f(){
  this.a = 22;
  let b = ()=>{console.log(this.a)}
  b();
}
var obj = new f();
// 输出22
```

### 四、什么是window对象？什么是document对象？
**window对象**是指浏览器打开的窗口。<br>
**document对象**是文档对象，window对象的一个属性。

### 五、document.write和innerHTML的区别？
`document.write`直接将内容写入页面的内容流，会导致重绘整个页面。<br>
`innerHTML`将内容写入某个DOM节点，可以重绘页面的一部分<br>
#### innerHTML、innerText、outerHTML、outerText
- `innerHTML`：设置或获取标签所包含的HTML与文本信息。（不含标签本身）
- `innerText`：设置或获取标签所包含的文本信息。（不含标签本身）
- `outerHTML`：设置或获取标签本身以及所包含的HTML与文本信息。（包含标签本身）
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

### 六、JS获取HTML元素的8种方法
- 通过ID获取（getElementById）
- 通过name属性（getElemsentsByName）
- 通过标签名（getElementsByTagName）
- 通过类名（getElementsByClassName）
- 通过选择器获取一个元素（querySelector）
- 通过选择器获取一组元素（querySelectorAll）
- 获取HTML标签（document.documentElement）
- 获取body元素（document.body）

### 七、JS操作DOM的方法
（1）创建节点<br>
```css
createElement("标签名")    //创建一个具体的元素
createTextNode()           //创建一个文本节点
createDocumentFragment()   //创建一个DOM片段
createAttribute("属性名")  //创建一个属性节点
```
（2）插入节点<br>
`父节点.appendChild(新的子节点);`，在父节点的最后插入一个新的子节点。<br>
`父节点.insertBefore(新的子节点,参考节点);`，在参考节点前插入一个新节点，如果参考节点为null，将在父节点最后插入一个子节点。<br>
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
方式1：**元素节点.属性**，比如：node.src，node.className，node.title；<br>
方式2：`node.getAttribute("属性名");`<br>
（7）设置节点的属性值<br>
方式1：`node.src = ""；`<br>
方式2：`node.setAttribute(属性名，新的属性值)`<br>
（8）删除节点属性：`node.removeAttribute(属性名)`

#### DOM中的三种节点类型
元素节点、文本节点、属性节点

#### DOM元素常用属性
- ele.parentNode：返回当前元素的父节点
- ele.children：返回当前元素的所有子节点，只返回HTML节点
- ele.childNodes：返回当前元素的所有子节点，包括文本、HTML、属性节点。（回车也会当做一个节点）
- ele.firstChild：返回当前元素的第一个子节点对象
- ele.lastChild：返回当前元素的最后一个子节点对象
- ele.nextSibling：返回当前元素之后的下一个兄弟节点（包括文本节点、注释节点）
- ele.previousSibling：返回当前元素之前的上一个兄弟节点（包括文本节点、注释节点）
- ele.nextElementSibling：返回当前元素之后的下一个兄弟元素（不包括文本节点、注释节点）
- ele.previousElementSibling：返回当前元素之前的上一个兄弟元素（不包括文本节点、注释节点）

### 八、JS中开头"use strict"是什么意思？
`"use strict"`是一种ECMAscript 5 添加的严格运行模式，这种模式使得 Javascript 在更严格的条件下运行。<br>
**严格模式的限制：**<br>
（1）不允许使用未声明的变量（对象也是一个变量）；<br>
（2）不允许删除变量或对象；<br>
（3）不允许删除函数；<br>
（4）不允许删除一个不允许删除的属性；<br>
（5）不允许变量重名；<br>
（6）不允许使用八进制、转义字符；<br>
（7）不允许对只读属性赋值；<br>
```javascript
"use strict";
var obj = {};
Object.defineProperty(obj,"x",{value:0,writable:false});
obj.x = 3.14     //报错
```
（8）不允许对一个使用getter方法读取的属性进行赋值；<br>
```javascript
"use strict";
var obj = {};
var obj = {get x() {return 0}};
obj.x = 3.14     //报错
```
（9）不能使用`eval`、`arguments`作为变量名；<br>
（10）由于一些安全原因，在作用域`eval()`创建的变量不能被调用；<br>
```javascript
"use strict";
eval("var x = 2");
alert(x);       //报错
```
**（11）禁止this关键字指向全局对象；**<br>
**为什么使用严格模式：**<br>
消除JavaScript语法的一些不合理、不严谨之处，减少一些怪异行为；消除代码运行的一些不安全之处，**保证代码运行的安全**；提高编译器效率，增加运行速度；另一方面，同样的代码，在**严格模式**中，可能会有不一样的运行结果；一些在“正常模式”下可以运行的语句，在“严格模式”下将不能运行。<br>

### 九、JS中eval的功能？
把对应的字符串解析成JS代码并运行。

### 十、window.onload和$(document).ready()的区别
- `window.onload=function(){}`方法是在网页中所有的元素（包括元素的所有关联文件）完全加载到浏览器后才执行的。一个页面中不能同时编写多个。
- `$(document).ready()`可以在DOM载入就绪时就对其进行操纵，并调用执行绑定的函数。同一页面能同时编写多个，简写为`$(function(){})`。

### 十一、JSON与JS对象的转换
- `JSON.parse()`: JSON字符串转换为JS对象
- `JSON.stringify()`: JS对象转换成JSON字符串

### 十二、[JS的执行机制](https://www.cnblogs.com/intangible/p/8066979.html)
（1）JavaScript是一门**单线程**语言，是按照语句出现的顺序执行。<br>
（2）JavaScript事件循环<br>
分为：**同步任务**、**异步任务**<br>
- 任务进入执行栈之后，同步任务和异步任务分别进入不同的执行场所，同步的进入主线程，异步的进入`Event Table`并注册函数。
- 当指定的事情完成时，`Event Table`会将这个函数移入`Event Queue`。
- 主线程内的任务执行完毕为空之后，会去`Event Queue`中读取对应的函数，进入主线程执行。
- 上述过程会不断重复，也就是常说的`Event Loop` **事件循环**。

除了广义的同步任务和异步任务，对任务还有更精细的定义：<br>
- `macro-task(宏任务)`：包括整体代码script，setTimeout，setInterval
- `micro-task(微任务)`：Promise，process.nextTick

```javascript
setTimeout(function() {
    console.log('setTimeout');
})

new Promise(function(resolve) {
    console.log('promise');
}).then(function() {
    console.log('then');
})

console.log('console');

// 输出：promise console setTimeout
```

### 十三、[深拷贝与浅拷贝](https://www.cnblogs.com/echolun/p/7889848.html)
如何区分深拷贝与浅拷贝，简单点来说，就是假设B复制了A，当修改A时，看B是否会发生变化，如果B也跟着变了，说明这是浅拷贝，如果B没变，那就是深拷贝。<br>
涉及到基本数据类型和引用数据类型的数据存储方式：栈和堆。<br>
**实现深拷贝：**<br>
（1）递归复制**所有层级属性**。<br>
（2）借用JSON对象的`parse`和`stringify`。<br>
（3）借用JQuery的`extend`方法。<br>
（4）借用Lodash（JavaScript工具库）中的`_.cloneDeep`。<br>

### 十四、JS异步处理的几种方式
**（1）回调函数（callback）**<br>
**概念**：回调是函数A被作为一个参数传递到函数B里，在函数B执行完后再执行。<br>
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
    // f1的任务代码
    f1.trigger('done');  // 执行完成后，立即触发done事件，开始执行f2
  },1000);
}
```
**优点**：容易理解，可以绑定多个事件，每一个事件可以指定多个回调函数，而且可以去耦合，有利于实现模块化。<br>
**缺点**：整个程序都要变成事件驱动型，运行流程会变得不清晰。<br>
```javascript
element.onclick=function(){}
```
**优点**：写法兼容到主流浏览器<br>
**缺点**：当同一个element元素绑定多个事件时，只有最后一个事件会被添加，相当于一次只能添加一个事件；<br>
```javascript
//IE:attachEvent;三个方法执行顺序：3-2-1；
elment.attachEvent("onclick",handler1);
elment.attachEvent("onclick",handler2);
elment.attachEvent("onclick",handler3);
```
```javascript
//标准addEventListener;执行顺序：1-2-3
elment.addEvenListener("click",handler1,false);
elment.addEvenListener("click",handler2,false);
elment.addEvenListener("click",handler3,false);
```
**注意**：该方法的第三个参数是冒泡获取，是一个布尔值：当为false时表示由里向外（冒泡），true表示由外向里（捕获）。<br>
**（3）发布/订阅（又称观察者模式）**<br>
```javascript
//jQuery的一个插件 Ben Alman的Tiny Pub/Sub
jQuery.subscribe("done", f2);    //f2向jQuery订阅"done"
jQuery.unsubscribe("done", f2);  //取消订阅
//或者
function f1(){
  setTimeout(function () {
    // f1的任务代码
    jQuery.publish("done");//f1执行完成后，向jQuery发布"done"信号，从而引发f2的执行
  }, 1000);
}
```
**（4）promise对象（promise 模式）**<br>
**promise是一种模式，promise可以帮忙管理异步方式返回的代码**。将代码进行封装并添加一个类似于事件处理的管理层。我们可以使用promise来注册代码，这些代码会在在promise**成功或者失败后运行**。<br>
我们可以注册任意数量的函数在成功或者失败后运行，也可以在任何时候注册事件处理程序。<br>
**promise有两种状态：1、等待（pending）；2、完成（settled）**。promise会一直处于等待状态，直到它所包装的异步调用返回/超时/结束。这时promise状态变成完成。**完成状态分成两类：解决（resolved）；拒绝（rejected）**。<br>
`resolve()`函数告诉promise用户promise已解决；`reject()`函数告诉promise用户promise未能顺利完成。注意then和catch用法，可以将他们想象成onsucess和onfailure事件的处理程序。<br>
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

### 十五、JS设置CSS样式的几种方式
**（1）设置style属性：**元素.style.样式属性="样式值"<br>
**（2）className属性：**把动态调整后的样式先写在CSS样式表里<br>
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
**（4）使用setProperty**<br>
```javascript
ele.style.setProperty('height', '300px', 'important');
```
**（5）设置cssText**<br>
```javascript
element.style.cssText = 'height: 100px !important';
element.style.cssText += 'height: 100px !important';
```

### 十六、new Date()获取的时间是什么时间
**运行环境的时间**：如果是**浏览器**那就是电脑时间或者手机时间；如果是**node.js**那就是服务器时间<br>
前台获取的时间都用处不大、用户可以改、不安全；通常时间用于从后端传入、服务器的时间一定是统一的、解析时间戳；<br>
js中单独调用new Date()，例如document.write(new Date())；显示的结果是：Mar 31 10:10:43 UTC+0800 2012 这种格式的时间； 但是用new Date() 参与计算会自动转换为从1970.1.1开始的毫秒数。<br>
#### JS获取当前时间戳
```javascript
// 方法1
var timestamp =Date.parse(new Date());
// 注意：Date.parse()得到的结果将后三位（毫秒）转换成了000显示，使用时可能会出现问题。例如动态添加页面元素id的时候，不建议使用。

// 方法2
var timestamp =(new Date()).valueOf();
// 方法3
var timestamp=new Date().getTime();
// 方法4
var timestamp=+new Date();
```