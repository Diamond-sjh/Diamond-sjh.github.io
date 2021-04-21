---
title: 函数内this指向以及改变this指向
date: 2017-05-20 15:32:29
tags: javascript
categories: 笔记总结
---

# 1、函数也是对象

- 函数的创建==>底层都是new关键字创建函数

  - 函数声明：

    ```javascript
    function sum(a,b) {
    	console.log(a + b);
    }
    ```

  - 函数表达式：

    ```javascript
    var sum = function sum(a,b) {
    	console.log(a + b);
    }
    ```

  - new关键字创建函数：

    ```javascript
    // 语法：var 变量名 = new Function ('形参1'，'形参2'，...，'函数体中的代码')
    var sum = new Function('a','b','console.log(a + b)');
    sum(10,20);
    ```

    

# 2、函数内部this的指向

> this的指向关键是看函数的调用方法

## 2.1 普通函数中this的指向

- this指向window

- 代码：

  ```javascript
  // 【在普通函数中this指向window】
  // function fn() {
  //   console.log(this);
  // }
  // fn();
  
  // var fn = function() {
  //   console.log(this);
  // };
  // fn();
  
  // (function(){
  //   console.log(this);
  // })();
  
  
  function fn1() {
    function fn2() {
      console.log(this);
    }
    fn2();
  }
  fn1();
  ```

  

## 2.2 构造函数中this的指向

- this指向当前创建的对象----在内存中开辟的空间

- 代码：

  ```javascript
   // 【在构造函数中this指向当前创建的对象】
  function Student(name,age) {
      this.name = name;
      this.age =age;
  }
  // 原型中添加方法
  Student.prototype.sayHi = function() {
      console.log('我叫' + this.name);
  };
  
  var stu1 = new Student('张三',10);	// ==>this指向stu1
  var stu2 = new Student('李四',10);	// ==>this指向stu2
  ```



## 2.3 对象方法中的this指向

- this指向调用者

- 代码：

  ```javascript
  // 【在方法中，this指向调用者。  对象.方法（）】
  function Student(name,age) {
      this.name = name;
      this.age =age;
  }
  // 原型中添加方法
  Student.prototype.sayHi = function() {
      console.log('我叫' + this.name);
  };
  
  var stu1 = new Student('张三',10);	// ==>this指向stu1
  stu1.sayHi();	// ==>方法sayHi中的this指向stu1
  ```

  

## 2.4 定时器中this的指向

- this指向window

- 代码：

  ```javascript
  // 【在定时器中this指向windnow】
  setTimeout(function(){
     console.log(this);
  },5000);
  ```

  

## 2.5 事件处理程序中this指向

- this指向事件源

- 代码：

  ```javascript
   // 【在事件处理程序中，this指向事件源】
   document.onclick = function() {
   	console.log(this);
   };
  ```

  

# 3、改变this的指向

## 3.1 call方法

- 语法：函数名.call(调用者，实参1...)

- 作用：函数被借用时，会立即执行，并且函数体内的this会指向调用者(借用者)

- 代码：

  ```javascript
   function fn(name, age) {
     this.name = name;
     this.age = age;
     console.log(this == obj);	//==>true
   }
  
   // 对象字面量
   var obj = {};
   fn.call(obj, '李四', 11);	//==>fn中的this指向obj
  ```

  

## 3.2 apply方法

- 语法：函数名.apply(调用者，[实参1，实参2,....])  ==>参数以数组的形式

- 作用：函数被借用是，会立即执行，并且函数体内的this会指向调用者(借用者)

- 代码：

  ```javascript
   function fn(name, age) {
     this.name = name;
     this.age = age;
     console.log(this == obj);	//==>true
   }
  
   // 对象字面量
   var obj = {};
   fn.call(obj, ['李四', 11]);	//==>fn中的this指向obj
  ```

  

## 3.3 bind方法

- 语法：函数名.bind(调用者，实参1....)

- 作用：函数被借用时，不会立即执行，而是**返回一个新的函数**。需要自己手动调用新的函数。

- 代码：

  ```javascript
  function fn(name, age) {
     this.name = name;
     this.age = age;
     console.log(this == obj);	//==>true
   }
  
   // 对象字面量
   var obj = {};
   var newFn = fn.bind(obj, '李四', 11);
   newFn();
  // fn.bind(obj, '李四', 11)();
  ```

  

## 3.4 使用

- 代码：

  ```javascript
  //	this指向window
  var name = 'window';
  var obj = {name = 'obj'};
  setTimeout(function(){
  	console.log(this.name);	//==>输出window
  },2000)
  //	this指向obj
  var name = 'window';
  var obj = {name = 'obj'};
  setTimeout(function(){
  	console.log(this.name);	//==>输出obj
  }.bind(obj),2000)	//	bind不会立即执行
  ```

  

- 伪数组借用数组的push方法实现增加

- 代码：

  ```javascript
  var arr = [10,20,30,40];
  //	arr.push(50);	//	push为数组中的方法
  var obj = {
  	0:10,
  	1:20,
  	2:30,
  	3:40,
  	length:4
  }
  //	console.log(arr instanceof Array);	==>true
  //	console.log(obj instanceof Array);	==>false
  //	Array.prototype.push	==>构造函数Array的原型中的push方法
  Array.prototype.push.call(obj,50);	//	==>改变push中this指向为obj
  ```

  