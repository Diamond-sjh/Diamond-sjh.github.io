---
title: git的push端口错误解决
date: 2016-11-20 15:40:27
tags: git
categories: 错误解决
typora-root-url: git的push端口错误解决
---

<meta name="referrer" content="no-referrer"/>

# 出现的问题：

Git Bash 控制行命令：

git push 向远程git仓库ssh地址推送的时候，命令行提示Connection reset by 52.74.223.119 port 22，而git push 向远程git仓库http地址推送的时候正常推送

# 问题如图所示：

![00](00.png)

# 解决的方法：

## 在window防火墙设置22端口

### 1、window10安全中心-->高级设置

![01](01.png)

### 2、入站规则-->新建规则

![02](02.png)

### 3、选择端口

![03](03.png)

### 4、端口输入22

![04](04.png)

### 5、选择允许连接

![05](05.png)

###  6、下一步

![06](06.png)

### 7、添加名称和描述（随意填写）

![07](07.png)

