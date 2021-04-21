---
title: JS中简单的几种继承方法
date: 2017-04-15 22:10:11
tags: javascript
categories: 笔记总结
typora-root-url: JS中简单的几种继承方法
---

<meta name="referrer" content="no-referrer"/>

## 1、继承介绍

> ES6之前没有方法直接实现继承，这里的继承是利用技巧方法来模拟实现；
>
> 作用：减少代码或简化代码

- 生活：子承父业
- 编程：类与类之间的关系。子类继承父类中的成员
  - 不使用继承，类与类之间可能有重复的属性和方法：
    - 学生类：姓名、年龄、性别、学号、打招呼、跑
    - 医生类：姓名、年龄、性别、钱、打招呼、跑
    - 老师类：姓名、年龄、性别、工号、打招呼、跑
    - ......
  - 使用继承，把不同类之间相同的属性和方法重新定义为一个类的：
    - 人类[父类]：姓名、年龄、性别、打招呼、跑
    - 学生类[子类]→人类[父类]
    - 医生类[子类]→人类[父类]
    - 老师类[子类]→人类[父类]
    - ......

## 2、原型继承

- 优缺点：

  > - **优点：完美继承了方法**
  > - **缺点：无法完美继承属性**

- 如何实现原型继承：

  > - ①先更改子类的原型prototype指向父类的一个实例对象。
  >   -  子类.prototype = new 父类(); 
  > - ②再给子类的原型(即new 父类())设置一个constructor指向该子类
  >   -  子类.prototype.constructor = 子类； 

- 图解：

　![继承](继承.png)

- 代码：

  ```javascript
  // 人类 → 父类
   function Person() {
     this.name = '名字';
     this.age = 10;
     this.gender = '男';
   }
   Person.prototype.sayHi = function () { console.log('你好'); };
   Person.prototype.eat = function () { console.log('我会吃。。。'); };
   Person.prototype.play = function () { console.log('我会玩'); };
  
  
   // 学生类 → 子类
   function Student() {
     this.stuId = 1000;
   }
   // 子类的原型prototyp指向父类的一个实例对象
   Student.prototype = new Person();
  
   // 添加一个constructor成员
   Student.prototype.constructor = Student;
  
   // 如何实现原型继承：
   // 给子类的原型prototype重新赋值为父类的一个实例对象。
   // 利用了原型链上属性或方法的查找规则。
  
  
   // 创建一个学生对象
   var stu1 = new Student();
   console.log(stu1.constructor)
  ```

## 3、借用继承

- 本质就是改变父类构造函数中this指向子类(这里使用call方法)，子类调用父类属性

- 优缺点：

  > - **优点：完美继承了属性**
  > - **缺点：无法继承方法。**

### 3.1 call方法

- 语法： `函数名.call(调用者,函数实参，函数的实参...)`; 

- 作用：该函数会立即执行，函数体内的this在被call时，this指向调用者。

- 代码：

  ```javascript
  function Person(userName,age) {
     console.log(this===obj);
     this.userName = userName;
     this.age = age;
   } 
   // 需求：通过一种方式调用执行Person函数，并且函数内部this代表obj
   var obj = {};   // new Object()
   Person.call(obj,'张三',10);　　//==>Person中的this指向obj
  ```

### 3.2 使用call实现借用继承

- 如何实现：
  - 在子类中，通过call调用父类，并更改父类中this的指向子类对象。
- 图解：

　![call](call.png)

- 代码：

  ```javascript
  // 人类 → 父类
   function Person(name,age,gender) {
     this.name = name;
     this.age = age;
     this.gender = gender;
   }
   Person.prototype.sayHi = function () { console.log('hello'); };
   Person.prototype.eat = function () { console.log('eat。。。'); };
   Person.prototype.play = function () { console.log('play'); };
  
  
   // 学生类 → 子类
   function Student(name,age,gender,stuId) {
     // this关键字代表谁,代表的是一个学生对象，当前创建的对象 stu1 ,stu2
     // var obj = this;
     //【借用继承】
     // Person.call(obj,name,age,gender);
     Person.call(this,name,age,gender);
     this.stuId = stuId;
   }
  
  
   // 创建第一个学生对象
   var stu1 = new Student('张三',15,'男',1006);
   // 创建第二个学生对象
   var stu2 = new Student('李四',16,'女',1005);
  ```

### 3.3 利用apply实现继承(同上)

- 代码：

```javascript
  function person(name,age) { 
        this.name = name;
        this.age = age; 
    }
    function man(name,age){ 
        person.apply(this,[name,age]); //这里就是伪造成 person 的一个事例 
    }
    var Man = new man('wozien',12);  
    console.log(Man.name); 
    console.log(Man.age);
```

## 4、组合继承

- 如何实现：

  - 原型继承和借用继承同时使用。

- 代码：

  ```javascript
   // 人类 → 父类
   function Person(name,age,gender) {
     this.name = name;
     this.age = age;
     this.gender = gender;
   }
   Person.prototype.sayHi = function () { console.log('hello'); };
   Person.prototype.eat = function () { console.log('eat'); };
   Person.prototype.play = function () { console.log('play'); };
  
  
   // 学生类 → 子类
   function Student(name,age,gender,stuId) {
     //【借用继承】
     Person.call(this,name,age,gender);
     this.stuId = stuId;
   }
  
   // 【原型继承】
   Student.prototype = new Person();
   Student.prototype.constructor = Student;
  
  
   // 创建第一个学生对象
   var stu1 = new Student('张三',16,'男',1006);
   // 创建第二个学生对象
   var stu2 = new Student('李四',15,'女',1005);
  ```