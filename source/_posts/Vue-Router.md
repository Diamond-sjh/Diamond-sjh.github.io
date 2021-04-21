---
title: Vue Router
date: 2018-12-15 22:41:12
tags: vue
categories: 笔记总结
typora-root-url: Vue-Router
---

<meta name="referrer" content="no-referrer"/>

# 路由router

## 什么是路由

组件切换是通过变换url地址的锚点信息而显示不同的组件，路由也是如此，也是 浏览器地址信息 与 组件 的映射关系体现，但**路由**是组件切换SPA的**封装版本**，使用起来要更加方便、高效

[官网参考](https://router.vuejs.org/zh/)

![01](01.png)

![02](02.png)

上图

- 浏览器锚点发生变化，会被onhashchange感知
- 然后window.location.hash获得变换后的锚点信息
- 通过锚点获得用于显示组件的标签名称
- 标签名称赋予给component标签的is属性
- 组件就呈现

以上锚点找到对应组件的过程就是<font color=red>路由</font>

路由对 component标签、onhashchange、window.location.hash、<a href="#/锚点">等原始内容做了<font color=red>封装</font>，是更高级组件切换的体现



## 在 vue 中使用 vue-router

### 安装路由

`vue-router路由`是一个js功能模块，在使用的时候需要先安装

运行指令

```javascript
 yarn add vue-router
```



### 应用路由

1. 在入口文件处导入路由模块(index.js)

   ```javascript
   import VueRouter from 'vue-router'
   ```

   

2. 注册路由模块(index.js)

   把导入的路由模块注册给Vue

   Vue.use(xxx)就是要把某个功能模块注册给Vue，

   该操作类似通过Vue.component()方式给Vue注册组件，

   注册多个组件需要多次执行Vue.component()，而Vue.use()可以一次性注册全部的组件，使用更给力，其本身也有其他功能，后续会介绍。

   ```javascript
   Vue.use(VueRouter)
   ```

3. 导入要显示组件(index.js)

   把要显示的子级组件导入进来

   ```javascript
   import Home from './home.vue';
   import About from './about.vue';
   import Movie from './movie.vue';
   ```

   > 事先把对应的 home.vue、about.vue、movie.vue子组件和内容创建好

4. 创建路由对象(index.js)
   创建路由对象，并把hash和组件的对应关系设置好

   ```javascript
   const router = new VueRouter({ // 配置对象中，要提供 hash 地址 到 组件之间的 对应关系
     routes: [ // 这个 routes 就是 路由 规则 的数组，里面要放很多的对应关系
       // { path: 'hash地址', component: 组件对象 }
       { path: '/home', component: Home },
       { path: '/user', component: Movie },
       { path: '/about', component: About }
     ]
   })
   ```

5. 挂载路由(index.js)
   把创建好的 router 对象，挂载到 主vm 对象上

   ```javascript
   const vm = new Vue({
     el: '#app',
     render: c => c(App),
     router // 把 创建的路由对象挂载到 VM 实例上，与 router:router 效果一致
   })
   ```

6. 创建路由链接(App.vue)

   在主vue组件中，通过router-link方式应用超链接

   - router-link标签：用以创建 路由的 hash锚点 超链接
   - to 属性，表示 点击此链接，要跳转到哪个 hash锚点 请求地址

   ```html
   <router-link to="/home"> 首页 </router-link>
   <router-link to="/user"> 会员 </router-link>
   <router-link to="/movie"> 电影 </router-link>
   ```

   > to 属性中，不需要以 # 开头

   router-link编译后会变成具体的html标签效果：

   ![03](03.png)

7. 设置容器(App.vue)

   在App.vue根组件中通过**router-view**标签设置显示组件的容器

   ```html
   <router-view> </router-view>
   ```

   > router-view的作用类似于component标签，设定占位符，用以显示各个业务子组件



路由规则的匹配过程：

- 用户点击 页面的 路由链接router-link，点击的一瞬间，就会修改 浏览器 地址栏 中的 Hash 锚点地址信息，
- 当 hash 信息被修改以后，会立即被  路由 监听到   (路由有封装onhashchange事件)
- 在onhashchange事件里边感知变化后的hash锚点信息(window.location.hash)，然后与对应的组件进行联系
- 组件被显示，具体是通过路由占位符rouer-view进行显示

## redirect重定向

用户第一次访问网站页面("/根目录"首页)时，地址栏里边没有“hash锚点”的信息，
也就没有对应的组件用于显示，明显项目体验不好，现在可以通过<font color=red>redirect</font>实现重定向效果
即 设定“/根目录”与一个**具体路由**联系起来，这样即使浏览器地址栏没有任何锚点信息，也有**默认**的组件进行显示

使用示例：

```javascript
const router = new VueRouter({
  routes:[
    {path:'/', redirect: '/mv'},
    {path:'/mv', component: Movie},
  ]
})

```

> 当访问"/根目录"时，就重定向到"/mv锚点"，进而显示Movie组件



## 路由按钮高亮

[官网参考](https://router.vuejs.org/zh/api/#router-link)

![04](04.png)

在App.vue主页面中(router-link超链标签使用页面)通过`router-link-active`选择器设置如下样式：

```html
<style lang="less" scoped>
.router-link-active {background-color: lightgreen;}
</style>

```

> 当前超链按钮被单击选中会有<font style="background-color:lightgreen">浅绿</font>背景颜色高亮显示



被点击激活的按钮，本身有class属性值，直接进行css样式设定即可：

![05](05.png)



## 嵌套路由（子路由）

路由的应用是在`App.vue根组件`中通过单击超链按钮切换显示不同`子组件`，具体表现就是父、子组件嵌套关系

有的应用比较复杂，子组件内部还有子组件，形成了   1级App.vue组件-->2级业务组件-->3级业务组件  的效果，现在学习如何在  2级组件  中切换显示  3级组件

使用步骤：

1. 在2级组件中设置  切换标签 和 显示的组件容器

   ```html
   <router-link to='/music/hongkong'>香港</router-link>
   <router-link to='/music/taiwan'>台湾</router-link>
   
   <router-view></router-view>
   
   ```

   > router-link：设置超链按钮
   >
   > router-view：第3级组件显示的容器

2. 在对应的路由规则中，通过 <font color=red>children 属性</font>，定义子路由规则：

   ```javascript
   var router = new VueRouter({
     routes: [
       { path: '/', redirect: '/user' },
       { path: '/home', component: Home },
       { path: '/user', component: User },
       {
         path: '/movie',
         component: Movie,
         children: [
           { path: '/movie/hongkong', component: Hongkong },
           { path: '/movie/taiwan', component: Taiwan },
           { path: '/movie/dalu', component: Dalu }
         ]
       }
    ]
   })
   
   ```



重定向

如果需要<font color=red>redirect</font>重定向效果可以像如下**两种**方式设置，**任选一个**即可：

```js
var router = new VueRouter({
 routes: [
   { path: '/', redirect: '/user' },
   { path: '/home', component: Home },
   { path: '/user', component: User },
   {
     path: '/movie',
     component: Movie,
     redirect: '/movie/hongkong', 				// 1) 方式重定向
     children: [
       { path: '', redirect:'/movie/hongkong' },	// 2) 方式重定向
       { path: '/movie/hongkong', component: Hongkong },
       { path: '/movie/taiwan', component: Taiwan },
       { path: '/movie/dalu', component: Dalu }
     ]
   }
 ]
})

```

> 重定向注意：
>
> 方式1  设置**redirect**成员的同时也需要**component**成员



## 路由传参

使用步骤：

1. 在路由中设定参数

   ```javascript
   const router = new VueRouter({
     routes:[
       {
         path: '/music,
         component: Music,
         children: [
           {path:'/dt/:mid', component: Detail, props:true}
         ]
       }
     ]
   })
   
   ```

   > :mid       表示当前路由有一个参数，名称为mid,  :冒号    是必须的(需要时可以通过相同的方式声明多个参数)
   > props:true      表示当前路由有参数需要传递

2. 在router-link超链地方把需要的参数设置好

   ```html
   <router-link :to="/dt/1001">XXX</router-link>
   
   ```

   > 通过to属性对“参数”一并进行设置

   router-link默认生成`a标签`，如果想生成其他标签，可以通过如下方式实现

   ```html
   <router-link tag="li" :to="/dt/1001">XXX</router-link>
   
   ```

   > tag="li" 表示生成li标签，并且超链效果仍然有效，可以[参考](https://router.vuejs.org/zh/api/#tag)

3. 参数的接收和使用

   ```html
   <p>电影详情展示---{{ mid }}</p>
   <script>
   export default {
     // 通过props接收的路由参数信息，名称为mid
     props:['mid']
   }
   </script>
   
   ```

   > 要通过props对路由参数进行接收
   >
   > props对两种数据都可以接收：
   >
   > 1) 路由参数
   >
   > 2) 父组件给传递过来的数据



## 编程式导航

导航：一个路由切换到另外一个路由去(/mv     /dt/102)

### 什么是

router-link是通过静态超链接按钮的方式把各个用于显示的组件给声明好，单击哪个按钮就显示哪个组件，其被称为<font color="red">声明式导航</font>

有时并不能通过单击**超链按钮**来显示一个组件，例如判断用户是登录状态就显示业务组件页面，否则显示登录组件页面，这时需要通过`程序代码`实现组件的判断显示，通过编写程序代码的方式实现组件显示就称为<font color=red>编程式导航</font>

### 使用语法

```javascript
this.$router.push(路由地址)   // 根据路由信息显示组件,可以是router-link标签的to属性值
this.$router.go(n)			// n可以是 负数、0、正数，表示页面要 回退、刷新、前进 
this.$router.forward()		// 前进
this.$router.back()			// 后退

```

> 灵感来自bom浏览器对象模型
>
> window.history.go()
>
> window.history.back()
>
> window.history.forward()



[官网参考](https://router.vuejs.org/zh/guide/essentials/navigation.html)

![06](06.png)

## 路由守卫

路由对象有一个名称为**beforeEach**的方法，每个路由在加载组件之前都要调用它，在这个方法中可以做逻辑判断，限制要做什么或不做什么，这个过程称为`路由守卫`，有着一夫当关万夫莫开的效果

![07](07.png)

### 语法

```javascript
router.beforeEach((to, from, next) => { /* 导航守卫 处理逻辑 */ })

```

> 这里的 router 就是 new VueRouter 得到的 路由对象
>
> beforeEach()是路由在显示组件**之前**要执行的代码

参数1

- to:是一个对象，保存着将要访问路由相关的参数

- from:是一个对象，保存着离开的那个页面的相关路由参数

- next:是一个函数，对后续的执行起着 拦截 或 放行 的作用

  如果没有问题请**一定**执行next()方法，以进行后续操作，

  next()方法也可以通过传递路由信息实现其他组件的显示

  例如：next('/login')   显示登录组件