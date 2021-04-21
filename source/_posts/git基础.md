---
title: git基础
date: 2016-10-28 16:54:13
tags: git
categories: 工具使用
---

## 1、git和github是什么？

git是一个工具（软件），github是一个代码托管平台，类似的还有**码云**

作用：我们可以在自己的电脑上通过git这个软件从github上下载（拉取）代码，也可以将我们本地电脑上的代码上传到github上。

## 2、github账号的注册：

1.打开github官网：https://github.com

2.填入自己的信息，完成注册之后，登录(Sign in)上就ok了。

## 3、git的安装：

1、根据自己电脑情况，选择应该安装的软件。

2、下载地址：https://git-scm.com/downloads

3、一路next安装即可。

4、在命令行中输入以下命令查看 Git 是否安装成功。

```bash
$ git --version

# 如果看到类似 git version 2.21.0.windows.1 ，表示安装成功了
```

5、安装完成之后，在电脑任何一个位置，点击鼠标右键，会多了两个选项：

​		-- Git GUI Here  

​		-- Git Bash Here

- Git GUI Here  的作用是，在此创建一个**可视化**的git窗口，用来上传或拉取代码或其他操作

- Git Bash Here 的作用是，在此创建一个git**命令行**窗口，用来上传或拉取代码或其他操作

6、我们经常使用的是Git Bash Here 这个命令行窗口，在里面输入一些命令就可以进行操作。

## 4、使用Git管理自己的代码

> 初次使用Git，会让我们配置用户的信息，配置方式如下：

```bash
# --global 会将配置项保存到用户配置
$ git config --global user.name "xxx"
$ git config --global user.email "xxx"
```

### 4.1 git名词解读：

**Workspace : 工作区**

- 工作目录是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。

**Index / Stage  : 暂存区**

- 暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 有时候也被称作`‘索引’'，不过一般说法还是叫暂存区域。

**Repository ： 仓库区（本地仓库**）

- Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。

**Remote ： 远程仓库**

> 工作区新建的文件和Git没有任何关系；文件被添加到暂存区，才叫做被Git管理过
>
> 代码不能越过暂存区而直接从工作区提交到仓库区

**Untracked files：未跟踪**

- 表示还没有被 Git 管理过，既没有进入过暂存区，更没有进入过仓库区。

**staged：已暂存**

- 表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中

**committed：已提交**

- 表示数据已经安全的保存在本地数据库中。

**modified：已修改**

- 表示修改了暂存区的文件，但还没提交到仓库区。

### 4.2 git常用命令：

**git clone 仓库地址**  --- 用来从**远程仓库**（github）上克隆项目

**git init**   ---- 初始化（新建**仓库区**（本地仓库）），在本地电脑的一个文件夹中执行此命令，会生成一个叫.git的隐藏文件夹

**git pull 仓库地址**  ----- 用来从远程仓库中下载代码   和clone很相似

**git add .**    ----- 将本地项目中新增、修改的文件添加到**暂存区**中，注意，后面是个英文状态下的点符号

**git commit -m '描述'**   ------   提交暂存区的代码到**仓库区**（本地仓库），并添加将要提交的描述

**git push 仓库地址**   -----  将暂存区的代码上传到**远程仓库**（github）中



### 4.3 进阶命令：

#### 状态操作：

**gitk** ---- 查看状态

**git status** ---- 显示有变更的文件

**git log** ---- 显示当前分支的版本历史

**git log --oneline** ---- 简略查看历史版本

#### 分支操作：

**git branch**   ---- 查看当前分支 （默认是master分支  --  主分支）

**git branch  分支名**    -----  创建一个分支（注意：**目前**我们创建分支，**必须在master分支上去创建**）

**git checkout  分支名**    ---- 切换分支

**git checkout -b 分支名** ---- 创建并切换分支

**git merge  分支名**   ----   将你想要合并的分支合并到当前分支上

**git push origin 【空格】【冒号】【需要删除的分支名字】**---通过代码删除远程分支

#### 撤销操作：

**git checkout [file]** ---- 恢复暂存区的指定文件到工作区

**git checkout .** ---- 恢复暂存区的所有文件到工作区

**git checkout [commit] [file]** ---- 恢复某个commit的指定文件到暂存区和工作区

**git reset [file]** ---- 重置暂存区的指定文件，与上一次 commit 保持一致，但工作区不变

**git reset --hard** ---- 重置暂存区与工作区，与上一次commit保持一致

#### 拉取和更新：

**git pull** ---- 拉取远程代码到当前分支，并和本地分支合并

**git pull [remote] [branch]** ---- 取回远程仓库的变化，并与本地指定分支合并



需要注意的是，我们平时在实际工作中，是不允许对主分支的东西进行操作的，也千万不要对主分支进行合并、提交、更新等。我们需要创建另外一个分支（测试分支），然后在这个分支中去合并其他组员的分支，最终将测试分支中的代码放到服务器上去测试、运行

### 4.4 示例：

仓库地址示例：git@github.com:AIMIYue.git  /    https://gitee.com/ityuer/test.git

组长和组员需要执行以下命令：

1.git clone git@github.com:AIMIYue.git    ---- 克隆项目（拉取代码，虽然没什么代码）

2.git branch  ---  查看分支

3.git  branch  zhangsan 、git branch  lisi 、 git branch  wangwu  --- 创建以自己姓名命名的分支，好区分

4.git branch   --- 再次查看分支  看有没有创建成功。绿色的是当前的分支，白色的是未被选中的其他分支

5.git checkout zhangsan   --- 切换到自己的分支，这里是切换到了张三的分支上

6.测试下看能不能在自己的分支上提交代码，在你的文件夹中新建一个文件，然后执行第七步

7.git add .    --- 将自己创建、修改、删除的文件添加到暂存区

8.git commit -m ‘first comit’  ----  进行提交（提交到本地仓库），并写上提交信息

9.git push  git@github.com:AIMIYue.git   ---  上传代码，上传完成后去github上刷新查看有没有自己的分支及自己的代码

10.如果成功的话，那么接下来就可以进行代码开发了，自己不断的写代码，写完了之后执行上述步骤（从第五步开始）

11.项目开发完成后，组长需要新建分支，然后合并自己和其他组员的分支，命令见下一步

12. git  branch  dev  ---- 创建测试分支，用来合并代码

    git checkout dev    ---  切换到dev分支上

    git merge origin/zhangsan 、 git merge origin/lisi 、 git merge origin/wangwu --- 合并远程分支

    git add.     --- 添加到暂存区

    git commit -m ‘测试分支提交’  ---  提交测试分支

    git  push  git@github.com:AIMIYue.git  ---  将测试分支上传到仓库中

13. 此时，项目已经完成，各组员可以各自从dev分支上拉取代码，然后运行就可以了



### 4.5 注意事项：

1.当创建完分支后，远程仓库不会有分支（因为此时创建的是本地分支），需要切换到新创建的分支，然后进行一次代码提交，远程仓库才会有你新创建的分支

2.进行代码提交，必须按照严格的顺序执行命令，不允许颠倒(add-->commit-->push)

3.每次add之前，要先看一下你目前处于哪个分支，可使用git branch查看，在命令行中的每句话末尾也都会有分支标识

4.如果不小心add了，发现分支错了，或者不小执行了add命令，需要使用   git rm  --cached 文件名 命令将add的文件从缓存区中删除。也可以使用  git rm -r --cached 文件名 命令删除某一个文件夹下的文件

5.创建并切换分支可以合并为一个命令，例如：git checkout - b zhangsan

6.可以使用git status 来查看发生变动的文件

7.小组项目的分支创建，一定**要在master分支上去创建其他的分支**！





关于更为详细的git的其他命令，可点击下方链接：

链接：http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html

<https://www.liaoxuefeng.com/wiki/896043488029600> 

layui：<https://www.layui.com/> 

layer：<http://layer.layui.com/> 

JQUI：<https://www.jqueryui.org.cn/tutorial/27.html> 