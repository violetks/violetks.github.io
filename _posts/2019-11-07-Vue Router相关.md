---
layout:     post
title:      Vue Router相关
subtitle:   Vue路由配置&页面跳转&参数传递
date:       2019-11-07
author:     violetks
header-img: img/post-violet.jpg
catalog: true
tags:
    - Vue
---

> 路由重新渲染了视图，不是真正的页面跳转

<<<<<<< HEAD
### 一、`<router-link :to="...">`标签，默认渲染成`<a>`标签
=======
### 一、router-link :to="..." 标签，默认渲染成 a 标签
>>>>>>> 65576d3818df62d7d8130f5ccf0e8bd49f7fcde1
to里的值可以是一个字符串路径，或者一个描述地址的对象。<br>
**1.不带参数跳转**
```html
// 字符串
<router-link to="/about"><button>跳转到about页面</button></router-link>
// 对象
<router-link :to="{path: '/about'}">跳转到about页面</router-link>
// 命名路由
<router-link :to="{name: 'about'}">跳转到about页面</router-link>
```
**2.带参数跳转**
```html
<!-- 首页 -->
// path + query，地址栏变成  /about?id=1&name=xx
<router-link :to="{path: '/about', query: {id:1,name:'xx'}}">跳转到about页面</router-link>
// name + query，命名路由
<router-link :to="{name: 'about', query: {id:1,name:'xx'}}">跳转到about页面</router-link>
// path + params，这里提供了 path，params 会被忽略不生效
<router-link :to="{path: '/about', params: {id:1,name:'xx'}}">跳转到about页面</router-link>
// name + params，命名路由
<router-link :to="{name: 'about', params: {id:1,name:'xx'}}">跳转到about页面</router-link>
```
```html
<!-- about页面接收参数 -->
<script>
export default {
  name: "about",
  created() {
    // 以query方式接收参数：通过URL传递，类似get方式
    console.log(this.$route.query.id);
    console.log(this.$route.query.name);
	
    // 以params方式接收参数：类似post方式
    console.log(this.$route.params.id);
    console.log(this.$route.params.name);
  }
}
</script>
```
### 二、函数中使用this.$router.push()跳转
**1.不带参数跳转**
```html
<template>
  <button @click="open">跳转到about页面</button>
</template>

<script>
export default {
  name: "index",
  methods: {
    open() {
	  this.$router.push('/about');           // 字符串
	  this.$router.push({path:'/about'});    // 对象
	  this.$router.push({name:'about'});     // 命名路由
    }
  }
}
</script>
```
**2.带参数跳转**：`query`方式和`params`方式
```html
<!-- index首页 -->
<template>
  <button @click="open1">query方式跳转到about页面</button>
  <button @click="open2">params方式跳转到about页面</button>
</template>

<script>
export default {
  name: "index",
  methods: {
    // query方式
    open1() {
	  this.$router.push({
	    path:'/about',
	    query: {id: 1,name: "xx"}
	  });
    },
	
    // params方式
    open2() {
	  this.$router.push({
	    name:'about',     // 这里只能用name
	    params: {id: 1,name: "xx"}
	  });
    }
  }
}
</script>
```
```html
<!-- about页面接收参数 -->
<script>
export default {
  name: "about",
  created() {
    // 以query方式接收参数：通过URL传递，类似get方式
    console.log(this.$route.query.id);
    console.log(this.$route.query.name);
	
    // 以params方式接收参数：类似post方式
    console.log(this.$route.params.id);
    console.log(this.$route.params.name);
  }
}
</script>
```

### 三、Vue2.0中$router和$route的区别
（1）`$router`是VueRouter的实例，是一个**全局对象**，包含了所有路由关键的对象和属性。<br>
`this.$router.push({path:'home'});`本质是向history栈中添加一个记录，点击后退会返回到上一个页面。<br>
`this.$router.replace({path:'home'});` 替换路由，没有历史记录，一般使用replace来做404页面 `this.$router.replace('/')`。<br>
`this.$router.go(-1)`，后退。<br>
（2）`$route`是一个跳转到的对象，每一个路由都会有一个route对象，是一个**局部对象**，可以获取对应的`name`,`path`,`params`,`query`等。<br>

### 四、验证登录后才可以点击查看页面
```javascript
// router/index.js
{
  path: "/home",
  name: "home",
  meta: { isAuth:true },
  component: () => import('@/pages/homes/home/index.vue')
}
```

### 五、vue-project项目中使用路由
第一步：`<el-menu>`标签上添加`router`，`<el-menu-item index="/posts/create">`表示跳转到的页面url，点击可以看到url改变，接下来添加相应页面。<br>
第二步：在views文件夹添加CreateArticle.vue和ListArticle.vue<br>
第三步：在router下的index.js中引入页面，配置路由<br>
第四步：App.vue中添加`<router-view></router-view>`路由出口<br>

### 六、真正的页面跳转
```javascript
<a target="_blank" href="url">超链接</a>
// 在当前页面打开
window.location.href = "https://www.baidu.com/s?wd=vue";
// 打开一个新的浏览器窗口
window.open("https://www.baidu.com/s?wd=vue","_blank","width=1000,height=500",true);
```
