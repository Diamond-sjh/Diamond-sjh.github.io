---
title: JS中的类和对象,new关键字
date: 2017-03-20 15:10:11
tags: javascript
categories: 笔记总结
typora-root-url: JS中的类和对象
---

<meta name="referrer" content="no-referrer"/>

### 1、什么是类

- **生活中**：一类、种类
- **编程中**：
  - 类指的是抽象的名称(`构造函数`)：例如，狗🐶
  - **class**关键字，**ES6之前没有类的概念**。 
  - 在ES3或ES5中通过**构造函数** 来创建对象
  - 构造函数：
    - 内置的：Object、Date、Array等
    - 自定义：例如，Dog......

### 2、什么是对象

- **生活中**：万物皆对象。任何具体的事和物都可以看成对象
- **编程中**：对象由**属性**和**方法**组成（或由键值对）。 具体的**实例，实例对象**
  - 属性：对象的静态特征，例如某人的，姓名、年龄、身高、性别等
  - 方法：对象的功能特征，例如某人，画画、写代码等
  - **注意事项**：
    - 方法的值用什么表示，用函数来表示
    - 对象方法中的**this，指向调用者**。

### 3、类(构造函数)和对象的关系

- 类是对象的模板
- 对象是类的具体实例（通过关键字 instanceof 检测一个对象是否属于某一个类型）
  - 语法：**对象名 instanceof 构造函数名; 返回布尔值**
- 创建对象得通过类（构造函数）创建
- new 构造函数() → 具体的实例(实例对象)

### 4、创建对象

- **语法：自定义构造函数（类）**

  ```javascript
  function 构造函数名(行参...){
   　　this.key = value；
      .......
  }
  // 注意规范：构造函数名首字母要大写  帕斯卡（每个单词首字母大写）  驼峰（从第二个单词开始首字母大写） 
  ```

- 语法：**new**关键字创建对象

  ```javascript
  var 对象名 = new 构造函数名(实参...);
  ```

### 5、new关键字的执行过程

- 构造函数在执行时，内部的this指向当前创建的对象

- 过程：

- - 首先会向内存申请一块空间，存放对象。
  - this关键字会指向内存中存放对象的空间。（this代表了当前创建的对象）
  - 通过this关键字向内存中的对象中添加属性和方法
  - 会把this返回给外部接收的变量

- 图解：

![01](01.png)

- 代码：

```javascript
 //构造函数
 function Student(name,age,gender) {
   // 属性
   this.name = name;
   this.age = age;
   this.gender = gender;
   // 方法
   this.sayHi = function() {
     // 方法内部：this 代表的是调用方法的对象
     console.log('我叫什么' + this.name)
   };
   this.writeCode = function() {
     console.log('我会写code');
   }
 }
 // 创建对象
 var zs = new Student('张三',10,'男');
 // 使用对象
 zs.writeCode();
```