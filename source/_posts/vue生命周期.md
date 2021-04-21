---
title: vue生命周期
date: 2018-11-28 17:19:55
tags: vue
categories: 笔记总结
---

<meta name="referrer" content="no-referrer"/>

# vue生命周期

## 什么是生命周期![vue生命周期00](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-ee7ea9e5-fad6-4763-8b50-eb42c2c0bed7/669bc78d-9174-4315-bbc3-b0c0839a8a39.png)

定义：生命周期是指vue实例(或者组件)从诞生到消亡所经历的各个阶段的总和

生命周期：

vue在使用的过程中，分为多个阶段，具体有[创建]()、[运行]()、[销毁]()，并且可以通过成员方法感知到

- 创建阶段由空白期、data初始化、methods初始化、模板渲染等组成
- 运行阶段分为 更新前 和 更新后 两部分
- 销毁阶段分为 销毁前 和 销毁后

不同阶段完成不同的任务，开发者可以利用各个阶段的特点完成业务需要的相关功能

beforeCreate：可以在这加个loading事件，在加载实例的时候触发

created：初始化完成时的事件写在这里，如在这结束loading事件，异步请求也适宜在这里调用

mounted：挂载元素，获取到DOM节点

updated：如果对数据同意处理，在这里写上相应函数

beforeDestroy：可以做一个确认停止事件的确认框

[生命周期参考](https://vue.docschina.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%A4%BA%E6%84%8F%E5%9B%BE)

## 生命周期函数分类

### 创建阶段

- 创建期间的生命周期函数：(特点：每个实例一辈子只执行一次，并且是自动的)

  - [beforeCreate]()：创建之前，此时 data 和 methods 尚未初始化

  - **[created]()** : <font color=red>第一个重要</font>的函数，此时，data 和 methods 已经创建好了，可以被访问，在此处非常适合做`data`数据初始化操作

  - [beforeMount]()：挂载容器模板结构之前，此时，容器的内容已经被Vue获取到了(容器本身还是未解析的原始内容)；

    现在Vue实例就被称为 **Virtual DOM(虚拟DOM)**

    ![vue生命周期01](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-ee7ea9e5-fad6-4763-8b50-eb42c2c0bed7/d41d7ac3-f77b-4d50-94ed-9f4e9dd38550.png)

  - **[mounted]()** <font color=red>第二个重要</font>的函数，此时，容器被vue渲染解析完毕；是操作初始**DOM**元素的最好时机

    ![vue生命周期02](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-ee7ea9e5-fad6-4763-8b50-eb42c2c0bed7/8891f4fd-9098-47c7-afee-b31848dca4b7.png)

    ```javascript
  beforeCreate() {
      console.group('---------beforeCreate调用--------')
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)   // undefined
      console.log('%c%s', 'color:red', 'data现在的样子：' + this.$data)  // undefined
      console.log('%c%s', 'color:red', 'methods方法现在的样子：' + this.getInfo) // undefined
    }
    
    ```
  
    > console.group() 按照组别对调试信息进行划分
  >
    > %c: 给调试信息设置样式，通过log的第2个参数体现
    >
    > $s: 通过string字符串方式输出信息，通过log的第3个参数体现
    >
    > this.$el:  获得到与Vue实例关联的容器
    >
    > this.$data: 获得data全部的成员，以对象形式返回
    >
    > this.getInfo: 把methods成员方法当做普通变量获取输出
  
    

    ```js
  created() {
      console.group('---------created调用--------')
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)    // undefined
      console.log('%c%s', 'color:red', 'data现在的样子：' + this.$data)  // 实体
      console.log('%c%s', 'color:red', 'methods方法：' + this.getDate) 	// 实体
    }
    
    ```
  
    > 在此函数中适合进行Vue数据初始化(获取首屏数据)操作

    ```js
  beforeMount() {
      console.group('---------beforeMount调用--------')
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el) // 实体
      console.log(this.$el) // 编译【前】的实体内容
    }
    
    ```
  
    ```js
  mounted() {
      console.group('---------mounted调用--------')   
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el) // 实体
      console.log(this.$el) // 编译【后】的实体内容
    }
    
    ```
  
    > 此时适合对页面dom元素做初始化操作

  



### 运行阶段

- 运行期间的生命周期函数：（特点：按需被调用 至少0次，最多不限制）

  - beforeUpdate：
    根据最新的数据重新解析渲染浏览器页面，此时  vue虚拟数据是最新的，但是页面还是旧的

  - updated：页面和数据都是最新的
    页面已经完成了更新，此时data数据和页面都是最新的

    ```
    beforeUpdate() {
      console.group('---------beforeUpdate调用--------')
      console.log(
        '%c%s',
        'color:red',
        'h2数据更新之前的效果：' + document.querySelector('h2').innerHTML
      )
    }
    
    ```

    ```
    updated() {
      console.group('---------updated调用--------')
      console.log(
        '%c%s',
        'color:red',
        'h2数据更新之后的效果：' + document.querySelector('h2').innerHTML
      )
    }
    
    ```

    ![vue生命周期03](https://vkceyugu.cdn.bspapp.com/VKCEYUGU-ee7ea9e5-fad6-4763-8b50-eb42c2c0bed7/f055d26b-9061-4030-8503-f04385874dbb.png)

    有 <font color=red>"Virtual DOM(虚拟DOM)"</font>，其是Vue实例获取到的div容器，是虚拟的(不是页面实实在在看到的)，该VirtualDOM 在Vue的生命周期运行阶段始终存在，随时感知数据变化，随时同步给页面

### 销毁阶段

- 销毁期间的生命周期函数：(特点：每个实例一辈子只执行一次)

  - beforeDestroy：将要销毁，处于销毁之前阶段，实例还正常可用

  - destroyed：销毁之后，Vue实例已经不工作了

    ```
    beforeDestroy() {
      console.group('---------beforeDestroy调用--------')
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)
    },
    
    
    ```

    ```
    destroyed() {
      console.group('---------destroyed调用--------')
      console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)
    }
    
    ```



完整应用示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <!--创建一个div容器，vue对该容器进行控制，设置要显示的内容-->
  <div id="app">
    <h2>{{ msg }}</h2>
  </div>
  
  <script src="./vue-2.6.10.js"></script>
  <script>
    var vm = new Vue({
      // 1) 生命周期创建阶段(4个函数),会自动执行
      beforeCreate(){
        // Vue实例已经创建完毕，但是相关的成员都没有，el、methods、data等等都没有
        console.group('--------beforeCreate发生调用--------')
        console.log('%c%s','color:red','el现在的样子：'+this.$el)     // undefined
        console.log('%c%s','color:red','data现在的样子：'+this.$data) // undefined
        console.log('%c%s','color:red','getDate现在的样子：'+this.getDate)  // undefined
      },
      created(){
        // 该阶段是一个【重要】阶段，此时data 和 methods已经准备好了，但是还没有去找div容器
        // 此阶段可以用于页面首屏数据获取操作，获取回来的数据存储给data的某个成员即可
        console.group('--------created发生调用--------')
        console.log('%c%s','color:red','el现在的样子：'+this.$el)      // undefined
        console.log('%c%s','color:red','data现在的样子：'+this.$data)  // 实体
        console.log('%c%s','color:red','getDate现在的样子：'+this.getDate)  // 实体
      },
      beforeMount(){
        // 此阶段完成了Vue实例对象 与 div容器联系的过程(本质是div容器已经被Vue实例获取到了)
        // 但是div容器的内容还是没有编译前的原生内容
        console.group('--------beforeMount发生调用--------')
        console.log('%c%s','color:red','el现在的样子：'+this.$el)      // 实体
        console.log(document.getElementsByTagName('h2')[0])  // 
      },
      mounted(){
        // 此阶段 Vue实例已经完成了div容器的内容的编译，并且编译好的内容也渲染给div容器了
        console.group('--------mounted发生调用--------')
        console.log('%c%s','color:red','el现在的样子：'+this.$el)      // 实体
        console.log(document.getElementsByTagName('h2')[0])  // 容器编译【后】实体内容
      },

      // 2) 生命周期运行阶段(2个函数),data数据变化后才会执行
      beforeUpdate() {
        console.group('---------beforeUpdate调用--------')
        console.log(
            '%c%s',
            'color:red',
            'h2数据更新【前】的效果：' + document.querySelector('h2').innerHTML
        )
      },
      updated() {
        console.group('---------updated调用--------')
        console.log(
          '%c%s',
          'color:red',
          'h2数据更新【后】的效果：' + document.querySelector('h2').innerHTML
        )
      },
      
      // 3) 生命周期销毁阶段(2个函数),只有vm调用$destroy()方法后才执行
      beforeDestroy() {
        console.group('---------beforeDestroy调用--------')
        console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)
      },
      destroyed() {
        console.group('---------destroyed调用--------')
        console.log('%c%s', 'color:red', 'el现在的样子：' + this.$el)
      },
      el: '#app',
      data: {
        msg: '生命周期学习篇'
      },
      methods: {
        getDate(){
          console.log('Sunday')
        }
      }
    })
  </script>
</body>
</html>

```





## 小结：

生命周期创建阶段的**created**和**mounted**两个函数最经常使用，其他了解即可

created：一般用于页面首屏data数据初始化使用

mounted：可对渲染完毕的页面做dom初始化操作(例如给dom元素设置事件)