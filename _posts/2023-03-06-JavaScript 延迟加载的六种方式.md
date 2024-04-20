---
layout:     post
title:      JavaScript 延迟加载的六种方式
date:       2023-03-06
author:     violetks
header-img: img/post-bg-css.png
catalog: true
tags:
    - JavaScript
---

### 一、概念
**JavaScript 延迟加载**，也就是等页面加载完成之后再加载 JavaScript 文件，有助于提高页面加载速度。<br>
**延迟脚本：**带`async`或`defer`属性的`script`标签，以及通过`document.createElement('script')`创建并且没有指定`script.async=false`的脚本默认为**异步延迟脚本**（必须为非内联脚本）。<br>
一般情况下，浏览器在解析 HTML 文档时，如果遇到`<script>`会停下对 HTML 文档的解析，先去处理脚本。如果脚本是内联的，浏览器会先执行这段内联的脚本；如果是外链的，会先加载脚本，再执行。等脚本执行结束浏览器才继续解析 HTML 文档。<br>

### 二、方式
一般有以下六种方式：<br>
- async 属性
- defer 属性
- 动态创建 DOM
- 使用 jQuery 的 getScript 方法
- 使用 setTimeout 延迟方法
- 让 JavaScript 最后加载

#### 1、async 属性
```html
<script src="./async1.js" async></script>
<script src="./async2.js" async></script>
```
有`async`标签的 JS，JS 的加载执行和 HTML 的解析渲染并行。也就是异步加载，不阻塞 HTML 文档的解析。<br>
设置了`async`的脚本将会并行加载，不阻碍 HTML 文档的解析，当脚本加载完成，如果此时文档还没解析完成，则会终止解析，先执行该脚本，执行结束后再继续解析；如果此时文档已经解析完成了，那就是立即执行该脚本。所以设置该属性的外部脚本的执行时机并不确定，根据网络状况可能会在 DOMContentLoaded 事件之前，也可能在 DOMContentLoaded 事件之后。所以在脚本里可能会获取不到 HTML 元素，因为此时元素可能还没有被解析。<br>
模块脚本设置`async`属性，该脚本及其所有依赖将会并行加载，不阻碍 HTML 文档的解析，加载完成后逻辑与普通脚本相同。<br>
有多个`async`外部脚本时，浏览器会在解析 HTML 文档的同时去并行加载这些外部脚本，执行顺序由加载完成顺序决定，谁先加载完成，谁就先执行。<br>
通过`document.createElement("script")`创建的`<script>`标签，通过其`src`属性设置引用脚本默认为`async`异步，通过其`textContent`设置内嵌脚本信息默认为`async`同步。<br>
```javascript
console.log('这是内嵌的JS脚本');
document.addEventListener('DOMContentLoaded', function () {
    console.log('这是页面的DOMContentLoaded事件');
});

// 创建异步脚本
const script = document.createElement("script");
script.src = "./common.js";
document.body.appendChild(script);

// 创建同步脚本
const script = document.createElement("script");
script.textContent = "console.log('这是创建出来的同步脚本')";
document.body.appendChild(script);
```

![async.png](/instructPic/async.png)

#### 2、defer 属性
```html
<script src="./defer1.js" defer></script>
<script src="./defer2.js" defer></script>
```
有`defer`标签的 JS，JS 的加载和 HTML 的解析渲染并行，但会在 HTML 解析完成后执行，在触发 DOMContentLoaded 事件前执行。<br>
设置了`defer`的外部脚本，不会阻碍文档的解析，只会在文档解析的同时去加载脚本信息，当文档解析完成后，暂时阻止 DOMContentLoaded 事件的执行，去执行已经加载好的脚本信息，如果此时脚本还未加载完成，则会等待脚本加载完成后，再执行该脚本。执行完成后，再触发 DOMContentLoaded 事件。<br>
有多个`defer`外部脚本时，浏览器会在文档解析的同时并行的加载这些脚本。当 HTML 文档解析完成后，会先检测这些脚本是否全部加载完成，若全部加载完成，则会按照它们在 HTML 文档中的先后顺序从上到下依次执行；若是没有全部加载完成，则会先等待其下载完成后，再**按照它们在 HTML 文档中的先后顺序从上到下依次执行**。全部脚本执行结束后，再触发 DOMContentLoaded 事件。<br>
内联脚本、模块脚本设置`defer`属性无效，因为模块脚本默认`defer`。<br>
```javascript
// 正常加载顺序从上到下
<script src="./common.js"></script> // console.log('这是普通外链的js文件');
// 设置 defer 属性
<script src="./defer1.js" defer></script> // console.log('这是设置了defer属性外链的js文件');
// 内嵌脚本
<script>
    console.log('这是内嵌的JS脚本');
    document.addEventListener('DOMContentLoaded', function () {
        console.log('这是页面的DOMContentLoaded事件');
    });
</script>
```

![defer.png](/instructPic/defer.png)

#### 3、async 和 defer 同时存在
如果页面上同时存在`async`和`defer`的`<script>`标签，那么这两个脚本之间的执行顺序是不确定的，两者几乎是同时开始加载的，根据加载完成时机，有下面几种情况：<br>
（1）如果`async`脚本先加载完，`defer`脚本后加载完，则先执行`async`脚本。<br>
（2）如果`defer`脚本先加载完，`async`脚本在文档解析完成并且 DOMContentLoaded 事件之后加载完，则是`defer`脚本先执行。<br>
（3）如果在文档解析完成后，DOMContentLoaded 事件之前，`async`的异步脚本和`defer`脚本都加载完成了，那么先执行`async`脚本，再执行`defer`脚本，因为`async`的优先级高于`defer`。<br>
```javascript
// 正常加载顺序从上到下
// 设置 async 属性
<script src="./async1.js" async></script> // console.log('这是async外链的js文件');
// 设置 defer 属性
<script src="./defer1.js" defer></script> // console.log('这是设置了defer属性外链的js文件');
// 内嵌脚本
<script>
    console.log('这是内嵌的JS脚本');
    document.addEventListener('DOMContentLoaded', function () {
        console.log('这是页面的DOMContentLoaded事件');
    });
</script>
```
执行结果一：`async`脚本先执行。<br>
![async先执行.png](/instructPic/async先执行.png)

执行结果二：`defer`脚本先执行。<br>
![defer先执行.png](/instructPic/defer先执行.png)

（4）如果一个`<script>`标签同时使用了`async`和`defer`属性，那此时浏览器会以`async`的特性去加载脚本，因为`async`的优先级高于`defer`。<br>
```javascript
// 正常加载顺序从上到下
// 同时设置 async 和 defer 属性
<script src="./test.js" async defer></script> // console.log('这是同时设置了async和defer的脚本');
// 内嵌脚本
<script>
    console.log('这是内嵌的JS脚本');
    document.addEventListener('DOMContentLoaded', function () {
        console.log('这是页面的DOMContentLoaded事件');
    });
</script>
```

![async和defer.png](/instructPic/async和defer.png)

（5）不同 script 的加载和执行时机<br>
![执行时机.png](/instructPic/执行时机.png)

#### 4、动态创建 DOM 方式
我们可以对文档的加载事件进行监听，当文档加载完成后再动态地创建 script 标签来引入 JS 脚本。<br>
![动态创建DOM.png](/instructPic/动态创建DOM.png)

#### 5、使用 jQuery 的 getScript 方法
![jQuery.png](/instructPic/jQuery.png)

#### 6、使用 setTimeout 延迟方法
![setTimeout延迟.png](/instructPic/setTimeout延迟.png)

#### 7、让 JavaScript 最后加载
把 JS 外部引入的文件放到页面底部，来让 JS 最后引入，从而加快页面加载速度。<br>