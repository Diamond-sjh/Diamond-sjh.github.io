---
title: FormData对象
date: 2017-01-28 20:48:22
tags: H5
categories: 笔记总结
---

# H5新增FormData对象

FormData是h5中新增的一个内置对象。

FormData对象用以将数据编译成键值对，以便用`XMLHttpRequest`来发送数据。其主要用于发送表单数据，但亦可用于发送带键数据(keyed data)，而独立于表单使用。

以前 AJAX 操作只能提交字符串，现在可以提交 **二进制** 的数据

- 使用方法一（有form表单）

  ```html
  <form id="fm">
      <input type="text" name="user"><br>
      <input type="password" name="pwd"><br>
      <input type="radio" name="sex" value="男" checked>男
      <input type="radio" name="sex" value="女">女<br>
      <input type="file" name="pic"><br/>
      <input type="button" id="btn" value="提交">
  </form>
  
  <script>
      // 当点击提交按钮的时候，获取表单各项的值，然后将他们发送给服务器
          document.getElementById('btn').onclick = function () {
              // 获取表单各项的值
              /* var user = document.getElementsByName('user')[0].value;
              var pwd = document.getElementsByName('pwd')[0].value; */
  
              // 使用FormData来收集表单数据
              // 1. 有表单，找到表单
              var form = document.getElementById('fm');
              // 2. 实例化FormData，将表单DOM对象传递给FormData
              var fd = new FormData(form); // fd对象，里面包含了表单中所有的值
              // 把fd发送给服务器即可
              var xhr = new XMLHttpRequest();
              xhr.open('POST', '/fd'); // fd接口专门用于处理FormData类型的数据的
              // xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
              xhr.responseType = 'json';
              xhr.send(fd);
              xhr.onload = function () {
                  console.log(this.response);
              }
          }
  </script>
  ```

  ==上述使用FormData的时候，form表单中的各项必须有name属性。没有name属性是收集不到数据的==

- 使用方法二（没有form表单）

  ```php+HTML
  <input type="text" id="user"><br>
  <input type="password" id="pwd"><br>
  <input type="file" id="pic"><br/>
  <input type="button" id="btn" value="提交">
  
  <script>
      // 点击提交按钮的时候，收集表单各项的数据，然后将他们发送给fd接口
          document.getElementById('btn').onclick = function () {
              // 1. 没有form，只能先实例化FormData
              var fd = new FormData();
              // 2. 调用fd提供的append方法，向fd对象中，添加值
              // fd.append('key', 'value');
              fd.append('username', document.getElementById('user').value);
              fd.append('pwd', document.getElementById('pwd').value);
              // 如果是文件的话，value值必须使用文件对象
              // 获取文件对象
              // console.dir(document.getElementById('pic'));
              var fileObj = document.getElementById('pic').files[0];
              // fd.append('myfile', 文件对象);
              fd.append('myfile', fileObj);
  
              var xhr = new XMLHttpRequest();
              xhr.open('POST', '/fd');
              xhr.responseType = 'json';
              xhr.send(fd);
              xhr.onload = function () {
                  console.log(this.response);
              }
          }
  </script>
  ```

jQuery中使用FormData：

```html
	<form id="fm">
        <input type="text" name="user"><br>
        <input type="password" name="pwd"><br>
        <input type="radio" name="sex" value="男" checked>男
        <input type="radio" name="sex" value="女">女<br>
        <input type="file" name="pic"><br />
        <input type="button" id="btn" value="提交">
    </form>

    <script src="/jquery.js"></script>
    <script>

        $('#btn').click(function () {
            var fm = $('#fm');
            var fd = new FormData(fm[0]); // 这里fm必须是DOM对象
            console.log(fd);

            $.ajax({
                type: 'post',
                url: '/fd',

                // 如果data使用的是对象，ajax方法会把对象转成字符串，
                // 即把{name: 'zs', age: 18}转成name=zs&age=18
                // data: {name: 'zs', age: 18}, 
                data: fd,
                // processData: false, 表示不让jQuery把fd对象转成字符串，而是直接发送fd对象
                processData: false,
                // contentType：false，表示不让jQuery去设置content-type，让FormData去处理
                contentType: false,
                success: function (res) {
                    console.log(res);
                }
            });
        });


        // xhr.send('name=zs&age=18');

    </script>
```



参考链接：

https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/Using_FormData_Objects