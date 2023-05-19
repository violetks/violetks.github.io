---
layout:     post
title:      Vue Router
subtitle:   Vue路由配置&页面跳转&参数传递
date:       2023-01-06
author:     violetks
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - Vue2
---

> 路由重新渲染了视图，不是真正的页面跳转。<br>
**注意**：与Vue 2 匹配的是[Vue Router 3.x](https://v3.router.vuejs.org)，与Vue 3 匹配的是[Vue Router 4.x](https://router.vuejs.org/)。<br>

### 一、单页应用（SPA）与多页应用（MPA）的区别
**1、多页应用**<br>
有多个独立的页面的应用，每个公共资源（js、css等）需选择性重新加载，多页面跳转刷新所有资源。每一次页面跳转时，服务器都会返回一个新的html文档。常用于app或客户端等。<br>
**2、多页应用的优缺点：**<br>
（1）首屏时间快：首屏时间叫做页面首个屏幕的内容展现的时间，当我们访问页面的时候，服务器返回一个html，页面就会展示出来，这个过程只经历了一个HTTP请求，所以页面展示的速度非常快。<br>
（2）搜索引擎优化效果好（SEO）：搜索引擎在做网页排名的时候，要根据网页内容才能给网页权重，来进行网页的排名。搜索引擎是可以识别html内容的，而我们每个页面所有的内容都放在Html中，所以这种多页应用，SEO排名效果好。<br>
（3）页面切换慢：因为每次跳转都需要发出一个http请求，如果网络比较慢，在页面之间来回跳转时，就会发现明显的卡顿。<br>
**3、单页应用**<br>
只有一个Web主页面的应用，公共资源（js、css等）仅需加载一次，所有的内容都包含在主页面，对每一个功能模块组件化。单页应用的跳转就是切换相关组件，仅刷新局部资源。常用于PC端官网、购物等网站。<br>
第一次进入页面时会请求一个html文件，切换到其他组件时页面路径发生改变，但是并没有新的html文件请求，页面内容也变化了。**原理：**JS能检测到url的变化，通过这一点，可以用js动态的将当前页面的内容清除掉，然后将下一个页面的内容挂载到当前页面上，这个页面路由并不是通过服务端来做的，而是通过前端来做，判断页面到底是显示哪个组件，清除不需要的，显示需要的组件。这种过程就是单页应用，每次跳转的时候不需要再请求html文件。<br>
**4、单页应用的优缺点：**<br>
（1）页面切换快：页面每次切换跳转时，并不需要做html文件的请求，节约了很多http发送时延，切换页面的时候速度很快。<br>
（2）首屏时间慢：单页应用的首屏时间慢，首屏时需要请求一次html，同时还要发送一次js请求，两次请求回来了，首屏才会展示出来。相对于多页应用，首屏时间慢。<br>
（3）不利于SEO：SEO效果差，因为搜索引擎只认识html里的内容，不认识js的内容，而单页应用的内容都是靠js渲染生成出来的，搜索引擎不识别这部分内容，也就不会给一个好的排名，会导致单页应用做出来的网页在百度和谷歌上的排名差。<br>
**5、表格对比：**<br>
![SPA与MPA.png](/instructPic/SPA与MPA.png)

### 二、Vue中如何优化首页加载速度
1、路由懒加载，首页加载的时候，不让其他页面加载，减缓首页压力。<br>
2、element-ui等组件库按需加入。<br>
3、首先打包时可以分为多个模块，其次要缩小文件体积，一些第三方库可以通过外链的方式引入。<br>

### 三、`<router-link :to="...">`标签，默认渲染成`<a>`标签
to里的值可以是一个字符串路径，或者一个描述地址的对象。<br>
**1.不带参数跳转**
```html
<!-- 字符串 -->
<router-link to="/about"><button>跳转到about页面</button></router-link>
<!-- 对象 -->
<router-link :to="{path: '/about'}">跳转到about页面</router-link>
<!-- 命名路由 -->
<router-link :to="{name: 'about'}">跳转到about页面</router-link>
```
**2.带参数跳转**
```html
<!-- path + query，地址栏变成  /about?id=1&name=xx -->
<router-link :to="{path: '/about', query: {id:1, name:'xx'}}">跳转到about页面</router-link>
<!-- name + query，命名路由 -->
<router-link :to="{name: 'about', query: {id:1, name:'xx'}}">跳转到about页面</router-link>
<!-- path + params，这里提供了 path，params 会被忽略不生效 -->
<router-link :to="{path: '/about', params: {id:1, name:'xx'}}">跳转到about页面</router-link>
<!-- name + params，命名路由 -->
<router-link :to="{name: 'about', params: {id:1, name:'xx'}}">跳转到about页面</router-link>
```
```html
<!-- about页面接收参数 -->
<script>
export default {
  name: "about",
  created () {
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

### 四、函数中使用this.$router.push()跳转
**1.不带参数跳转**
```html
<template>
  <button @click="open">跳转到about页面</button>
</template>

<script>
export default {
  name: "index",
  methods: {
    open () {
      this.$router.push('/about');           // 字符串
      this.$router.push({path: '/about'});   // 对象
      this.$router.push({name: 'about'});    // 命名路由
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
    open1 () {
      this.$router.push({
        path: '/about',
        query: {id: 1, name: "xx"}
      });
    },

    // params方式
    open2 () {
      this.$router.push({
        name: 'about',     // 这里只能用name
        params: {id: 1, name: "xx"}
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
  created () {
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

### 五、Vue 2中$router和$route的区别
1. `$router`是一个VueRouter的实例，是一个**全局对象**，包含了所有路由关键的对象和属性，可以导航到不同的路由里。<br>
`this.$router.push({path: 'home'});`本质是向history栈中添加一个记录，点击后退会返回到上一个页面。<br>
`this.$router.replace({path: 'home'});` 替换路由，没有历史记录，一般使用replace来做404页面 `this.$router.replace('/')`。<br>
`this.$router.go(-1)`，后退。<br>
2. `$route`是`$router`跳转到的当前一个对象，每一个路由都会有一个route对象，是一个**局部对象**，可以获取对应的`name`,`path`,`params`,`query`等。<br>

### 六、验证登录后才可以点击查看页面
```javascript
// router/index.js
{
  path: "/home",
  name: "home",
  meta: { isAuth: true },
  component: () => import('@/pages/homes/home/index.vue')
}
```

### 七、vue-project项目中使用路由
第一步：`<el-menu>`标签上添加`router`，`<el-menu-item index="/posts/create">`表示跳转到的页面url，点击可以看到url改变，接下来添加相应页面。<br>
第二步：在views文件夹添加CreateArticle.vue和ListArticle.vue<br>
第三步：在router下的index.js中引入页面，配置路由<br>
第四步：App.vue中添加`<router-view></router-view>`路由出口<br>

### 八、几个注意点
1、路由组件通常存放在`pages`文件夹，一般组件通常存放在`components`文件夹；<br>
2、通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载；<br>
3、每个组件都有自己的`$route`属性，里面存储着自己的路由信息；<br>
4、整个应用只有一个router，可以通过组件的`$router`属性获取到；<br>

### 九、真正的页面跳转
```javascript
<a target="_blank" href="url">超链接</a>

// 在当前页面打开
window.location.href = "https://www.baidu.com/s?wd=vue";
// 打开一个新的浏览器窗口
window.open("https://www.baidu.com/s?wd=vue", "_blank", "width=1000, height=500", true);
```

### 十、创建路由实例
```javascript
// 创建并暴露一个路由实例
export default new VueRouter({
  routes: [
    {
      path: "/about",
      component: About
    },
    {
      // 动态路径参数，以冒号开头，像 /user/foo 和 /user/bar 都将映射到相同的路由。在 User 组件可以用 this.$route.params.id 接收
      path: "/user/:id",
      component: User
    },
    {
      path: "/home",
      component: Home,
      children: [ // 配置嵌套子路由
        {
          path: "news",
          component: News
        },
        {
          name: "message",
          path: "message/:id/:title",
          component: Message,
          // props的第一种写法，值为对象，该对象中的所有key-value都会以props的形式传给Message组件
          props: { a: 1, b: "hello" },

          // props的第二种写法，值为布尔值，为真时会把该路由组件收到的所有params参数，以props的形式传给Message组件
          props: true,

          // props的第三种写法，值为函数
          props($route){
            return { id: $route.query.id, title: $route.query.title }
          }
        }
      ]
    }
  ]
})
```
