---
layout:     post
title:      JavaScript 执行机制（★）
date:       2023-04-05
author:     violetks
header-img: img/post-bg-js-version.jpg
catalog: true
tags:
    - JavaScript
---

### 一、JavaScript 是一门**单线程**语言，是按照语句出现的顺序执行。
### 二、**JavaScript 事件循环**分为：**同步任务**、**异步任务**。
- 任务进入执行栈之后，同步任务和异步任务分别进入不同的执行场所，同步的进入主线程，异步的进入`Event Table`并注册函数。
- 当指定的事情完成时，`Event Table`会将这个函数移入`Event Queue`。
- 主线程内的任务执行完毕为空之后，会去`Event Queue`中读取对应的函数，进入主线程执行。
- 上述过程会不断重复，也就是常说的`Event Loop` **事件循环**。

```javascript
let data = [];
$.ajax({
    url: www.javascript.com,
    data: data,
    success: () => {
      console.log('发送成功!');
    }
})
console.log('代码执行结束');
```

上面是一段简易的 Ajax 请求代码：<br>
1、Ajax 进入 Event Table，注册回调函数 success。<br>
2、执行 console.log('代码执行结束')。<br>
3、Ajax 事件完成，回调函数 success 进入 Event Queue。<br>
4、主线程从 Event Queue 读取回调函数 success 并执行。<br>

### 三、setTimeout 异步延时执行
```javascript
setTimeout(() => {
  task();
}, 3000)
console.log('执行console');
// 先执行 console.log 这个同步任务，再执行 task()
```

```javascript
setTimeout(() => {
  task()
}, 3000)
sleep(10000000)
// 控制台执行 task() 需要的时间远远超过 3 秒
```

上面代码执行过程：<br>
1、`task()`进入 Event Table 并注册，计时开始。<br>
2、执行`sleep`函数，很慢，非常慢，计时仍在继续。<br>
3、3 秒到了，计时事件`setTimeout`完成，`task()`进入 Event Queue，但是`sleep`还没执行完，只好等着。<br>
4、`sleep`终于执行完了，`task()`终于从 Event Queue 进入了主线程执行。<br>
总结：`setTimeout`这个函数，是经过指定时间后，把要执行的任务`task()`加入到 Event Queue中，又因为是单线程任务要一个一个执行，如果前面的任务需要的时间太久，那么只能等着，导致真正的延迟时间远远大于 3 秒。<br>
`setTimeout(fn,0)`指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。<br>
```javascript
// 代码 1
console.log('先执行这里');
setTimeout(() => {
  console.log('执行啦')
}, 0);
// 输出结果：
// 先执行这里
// 执行啦

// 代码 2
console.log('先执行这里');
setTimeout(() => {
  console.log('执行啦')
}, 3000);
// 输出结果：
// 先执行这里
// ... 3s later
// 执行啦
```

### 四、setInterval
`setInterval`会每隔指定的时间将注册的函数置入 Event Queue，如果前面的任务耗时太久，那么同样需要等待。<br>
唯一需要注意的一点是，对于`setInterval(fn,ms)`来说，我们已经知道不是每过`ms`秒会执行一次`fn`，而是每过`ms`秒，会有`fn`进入 Event Queue。**一旦`setInterval`的回调函数`fn`执行时间超过了延迟时间`ms`，那么就完全看不出来有时间间隔了。**<br>

### 五、除了广义的同步任务和异步任务，对任务还有更精细的定义。
- `macro-task(宏任务)`：包括整体代码 script，setTimeout，setInterval
- `micro-task(微任务)`：Promise，process.nextTick

不同类型的任务会进入对应的 Event Queue，比如`setTimeout`和`setInterval`会进入相同的 Event Queue。<br>
事件循环的顺序，决定 JS 代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。<br>

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

这段代码作为宏任务，进入主线程。<br>
1、先遇到`setTimeout`，那么将其回调函数注册后分发到宏任务 Event Queue。（注册过程与上同，下文不再描述）<br>
2、接下来遇到了`Promise`，`new Promise`立即执行，`then`函数分发到微任务 Event Queue。<br>
3、遇到`console.log()`，立即执行。<br>
4、整体代码 script 作为第一个宏任务执行结束，看看有哪些微任务？我们发现了`then`在微任务 Event Queue 里面，执行。<br>
5、第一轮事件循环结束，开始第二轮循环，当然要从宏任务 Event Queue 开始。我们发现了宏任务 Event Queue 中`setTimeout`对应的回调函数，立即执行。<br>
6、结束。<br>

![JS执行机制.png](/instructPic/JS执行机制.png)

```javascript
console.log('1');

setTimeout(function() {
  console.log('2');
  process.nextTick(function() {
    console.log('3');
  })
  new Promise(function(resolve) {
    console.log('4');
    resolve();
  }).then(function() {
    console.log('5')
  })
})
process.nextTick(function() {
  console.log('6');
})
new Promise(function(resolve) {
  console.log('7');
  resolve();
}).then(function() {
  console.log('8')
})

setTimeout(function() {
  console.log('9');
  process.nextTick(function() {
    console.log('10');
  })
  new Promise(function(resolve) {
    console.log('11');
    resolve();
  }).then(function() {
    console.log('12')
  })
})
```

第一轮事件循环流程分析如下：<br>
- 整体 script 作为第一个宏任务进入主线程，遇到`console.log`，输出 1。
- 遇到`setTimeout`，其回调函数被分发到宏任务 Event Queue 中。我们暂且记为`setTimeout1`。
- 遇到`process.nextTick()`，其回调函数被分发到微任务 Event Queue 中。我们记为`process1`。
- 遇到`Promise`，`new Promise`直接执行，输出 7。`then`被分发到微任务 Event Queue 中。我们记为`then1`。
- 又遇到了`setTimeout`，其回调函数被分发到宏任务 Event Queue 中，我们记为`setTimeout2`。

宏任务 Event Queue | 微任务 Event Queue
---|---
setTimeout1 |	process1
setTimeout2 |	then1

- 上表是第一轮事件循环宏任务结束时各 Event Queue 的情况，此时已经输出了 1 和 7。
- 我们发现了`process1`和`then1`两个微任务。
- 执行`process1`，输出 6。
- 执行`then1`，输出 8。

第一轮事件循环正式结束，这一轮的结果是输出 1，7，6，8。那么第二轮时间循环从`setTimeout1`宏任务开始：<br>
- 首先输出 2。接下来遇到了`process.nextTick()`，同样将其分发到微任务 Event Queue 中，记为`process2`。`new Promise`立即执行输出 4，`then`也分发到微任务 Event Queue 中，记为`then2`。

宏任务 Event Queue | 微任务 Event Queue
---|---
setTimeout2 |	process2
 |	then2

- 第二轮事件循环宏任务结束，我们发现有`process2`和`then2`两个微任务可以执行。<br>
- 输出 3。
- 输出 5。
- 第二轮事件循环结束，第二轮输出 2，4，3，5。
- 第三轮事件循环开始，此时只剩`setTimeout2`了，执行。
- 直接输出 9。
- 将`process.nextTick()`分发到微任务 Event Queue 中。记为`process3`。
- 直接执行`new Promise`，输出 11。
- 将`then`分发到微任务 Event Queue 中，记为`then3`。

宏任务 Event Queue | 微任务 Event Queue
---|---
 |	process3
 |	then3

- 第三轮事件循环宏任务执行结束，执行两个微任务`process3`和`then3`。
- 输出 10。
- 输出 12。
- 第三轮事件循环结束，第三轮输出 9，11，10，12。

整段代码，共进行了三次事件循环，完整的输出为 1，7，6，8，2，4，3，5，9，11，10，12。