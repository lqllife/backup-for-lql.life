---
title: git详解以及使用遇到问题解决
date: 2017-02-27 09:02:01
tags: 转载
categories: git
---
Git代码管理是分布式管理方式系统.优点在于其极高的安全性和非常强大的分支管理。
<!--more-->
<div class="image-package">
![](http://upload-images.jianshu.io/upload_images/3424061-5d0bd9c085841158.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<div class="image-caption">整体流程图</div></div>

###  工作区(working directory):
就是本地的代码区,电脑能看到的目录,就是工作区。

###  暂存区(Index):
工作区下有一个隐藏的'.git'文件,其主要作用是存储Git自动创建的第一个master分支,还有指向master分支的HEAD指针.还有一个最重要的stage的暂存区.需要提交的文件修改通通放到暂存区,然后一次性提交到暂存区的所有修改。

###  本地仓:
本地的个人仓库.管理着个人的代码的版本信息。

###  远程仓:
线上的仓库.管理着推送到服务器的代码版本。

###  过程1: 由工作区<->暂存区

#### 工作区->暂存区:

用`git add .` 添加所有的修改或者`git add <文件名>`添加单个指定的文件修改到暂存区中。

#### 暂存区->工作区:

##### (1)`git rm --cached "文件路径"`
不删除物理文件,紧将物理文件删除；

##### (2)`git rm --f "文件路径"`
不仅将该文件从缓存中 删除,还将物理文件删除(不会放到垃圾桶；

##### (3)`git reset <版本号>`
利用版本库清空暂存区.原理:将当前分支重设(reset)到指定的<commit>或者HEAD(默认,如果不指定commit,默认是HEAD,即最新的一次提交)。

*   --hard
    `git reset --hard <commit号>`重设(reset)index和working directory，自从<commit>以来在working directory中的任何改变都被丢弃，并把HEAD指向<commit>。

*   --soft
    `git reset --hard <commit号>`index和working directory中的内容不作任何改变，仅仅把HEAD指向<commit>。这个模式的效果是，执行完毕后，自从<commit>以来的所有改变都会显示在git status的"Changes to be committed"中。

### 过程2:由暂存区<->本地仓库

#### 暂存区->本地仓库

*   用`git commit -m '本次commit的描述'`

#### 本地仓库->暂存区

*   `git reset <版本号>` 利用版本库清空暂存区.原理:将当前分支重设(reset)到指定的<commit>或者HEAD(默认,如果不指定commit,默认是HEAD,即最新的一次提交)。

*   --hard
    `git reset --hard <commit号>`重设(reset)index和working directory，自从<commit>以来在working directory中的任何改变都被丢弃，并把HEAD指向<commit>

*   --soft
    `git reset --hard <commit号>`index和working directory中的内容不作任何改变，仅仅把HEAD指向<commit>。这个模式的效果是，执行完毕后，自从<commit>以来的所有改变都会显示在git status的"Changes to be committed"中.退回到该次commit提交,但是该版本之后所有的修改都没有了,如果想要恢复,就要试图从还未关闭的终端中找到你想要的退回的版本号,或者用`git reflog`查找你想要的commit版本号.继续用`git reset --hard <commit号>`回退。

###  过程3: 由本地仓->远程仓库

用`git push`


### 在本地创建一个Git管理的项目

1.从线上拉项目到本地
用 `git clone "项目的地址路径"`克隆一个git管理的项目到本地
2.本地项目上传线上
`git init`
`git remote add origin "地址url"`

### Git上的常用查看操作

1.查看文本内容
`cat <文件名>`
2.查看git的状态
`git status`会告诉你在当前的分支上git的情况(有没有修改,修改的文件是什么等)
`➜ learngit git:(master) git status On branch master Your branch is up-to-date with 'origin/master'. nothing to commit, working directory clean`
3.查看更详细的文件修改
`git diff` 查看所有文件的不同
`git diff <文件名>` 查看指定文件的不同
diff 是difference的缩写.只能查看还未add(还没加入到暂存区)的修改
4.查看所有的commit信息
`git log`
`git log --pretty=oneline` 更简洁的查看log信息(只有commit号和描述信息)
5.查看所有的git操作过的命令,可以找到删除了的commit号
`git reflog`
6.查看分支合并图
`git log --graph`

### 冲突处理

#### 1 <<<<<<<HEAD

1 + 1 = 2 这个区间内的内容是当前开发者修改的

#### 2 =======

1 + 2 = 3 这个区间内的内容是其他开发者修改的

#### 3 >>>>>>>

需要人工判断具体是删除或者保留哪一个修改内容.并且把1,2,3行提示删除掉.

### Git分支操作

#### 分支基本操作

1.分支的创建和切换

*   `git branch "分支名"` 创建一个分支 `git checkout "分支名"` 切换到该分支
*   创建并切换到该分支 `git checkout -b "分支名"`

2.列出所有的分支

*   `git branch` 用*标记当前的分支
*   `git branch -a` 列出远程分支

3.合并分支

*   `git merge "分支名"`合并指定分支到当前分支
*   `git rebase "分支名"`合并指定分支到当前分支
    两者的区别在于会不会保留分支操作的记录,前者记录,后者不记录.建议用merge

4.删除分支

*   删除本地分支: `git branch -d "分支名"`
*   强制删除本地分支: `git branch -D "分支名"`
    如果提示`the branch XXX is not fully merged`(XXX分支有没有合并到当前分支的内容)
*   删除远程分支: `git push origin :<分支名>` (危险命令哦)

5.本地新建了一个分支要推送到线上,需要设置关联或者追踪.

*   第一次push: `git push origin 本地分支名:远程分支名`

6远程端生成了一个新的分支,拉取到本地.

*   先关联远程端分支到本地: `git branch --set-upstream-to=origin/<线上分支名> <本地分支名>`
*   然后`git pull`

### bug分支

用issue作为分支的开头标记,当有一个bug出现需要改的时候,先拉取一个issue分支,修改并合并到开发分支上,然后删除issue分支.

### feature分支

*   开发新的功能的时候,最好拉去一个新的分支,以feature开头.
*   丢掉一个没有合并过的分支,即删除分支.(当新功能正在或者已经开发完,领导突然说不要了...心累)
    `git branch -D <分支名>` 强制删除该分支内容

### 暂存的使用

1.`git stash` 暂存
2.`git stash list` 查看所有的暂存
3.`git stash apply` 从暂存开始执行,但是stash内容不会删除.
4.`git stash drop` 删除暂存
5.`git stash pop` 恢复暂存的同时删除暂存
6.`git stash apply stash@{0}`恢复指定的暂存

### 远程仓的操作

1.查看远程仓的信息
`git remote`
`git remote -v` 显示更详细的信息
2.将分支推送到远程仓
`git push origin 本地分支名:远程分支名`

### 标签的使用

1.`git tag <name>`新建一个标签
2.`git tag -a <name> -m "指定的标签信息"`
3.`git show <tag名字>` 查看详细的tag信息
4.`git tag` 查看所有的tag名字
5.`git tag -d <标签名字>`删除一个标签
6.`git push origin <标签名>`推送标签到远程
7.`git push origin --tags`一次性推送所有未推送的标签
8.删除远程仓上的tag

*   先删除本地的标签: `git tag -d <tag名字>`
*   然后删除远程端的标签: `git push origin :refs/tags/<tag名字>`

###  .gitignore的使用

iOS 用到的`.gitignore` [https://github.com/github/gitignore/blob/master/Objective-C.gitignore](https://github.com/github/gitignore/blob/master/Objective-C.gitignore)
在工作区创建一个名字为`.gitignore`文件.把要忽略的文件名字填进去,并提交到Git.Git会自动忽略这些文件的修改.把链接中的内容信息copy到`.gitignore`文件中.

记住一定要把`.gitignore`拉取到具体的分支上才有用.

*   `git check-ignore` 检查ignore内容

*   `git add -f <文件名>`即使在gitignore中文件,也可以提交.

*   `UserInterfaceState.xcuserstate` 的屏蔽
    在. gitignore文件中加入`UserInterfaceState.xcuserstate`.在终端中输入`git rm --cached <你的工程名字>.xcodeproj/project.xcworkspace/xcuserdata/<该电脑用户名>字.xcuserdatad/UserInterfaceState.xcuserstate`
    然后add,commit,push等操作.

### Git 配置全局信息

1.配置作者名称
`git config --global user.name "Your Name"`
2.配置个人邮箱
`git config --global user.email "email@example.com"`
3.配置git的颜色(文件名会标记上颜色)
`git config --global color.ui true`

### Git上需要注意的单词

*   repository 仓库
*   software 软件
*   mode 模式,风格
*   insertion 插入
*   commit 把...托给
*   stage 原意:舞台,Git中是暂存区的意思.
*   checkout 检出
*   branch 分支
*   modified 改进的,修改的
*   unstaged 还未加入到暂存区的 un + stage(暂存区)
*   untracked 无足迹的,还没有加入git管理的
*   origin 起源,起点
*   reset 重设
*   rebase
*   merge 合并
*   remote 遥远的,远程的
*   HEAD 表示当前版本
*   HEAD^ 上一个版本
*   HEAD^^ 上两个版本
*   HEAD~100 上100个版本

### Linux的一些简单操作和一些符号的解释

#### 命令操作

1.  目录/文件的操作

    *   `mkdir "目录名"` 在当前路径下创建一个文件夹
    *   `mvdir "目录1" "目录2"` 移动或者重命名一个目录 (如果目录2不存在,就直接修改目录1的名称为目录2)
    *   `rmdir "目录名"` 删除一个目录
    *   `touch` 创建一个文件
    *   `vim "文件名` 编辑文件,如果文件不存在,就创建并进入编辑.
    *   删除一个文件
        `sudo rm -rf <文件名>` 在上帝权限下删除,可以删除一个该用户没有权限的文件(包括系统文件),**不建议使用**
        `rm <文件名>`能删除当前用户权限下的文件
2.  转换目录

    *   `cd + "路径"` 进入该文件目录下(也可以将文件直接拖进来)
    *   `cd ..` 回到上级目录
    *   `cd .` 当前目录 cd / 回到根目录
    *   `cd test.rtf` 不可用. 不能cd到文件里.只能cd到目录.
        报错信息: `-bash: cd: test.rtf: Not a directory`
3.  拷贝/移动/删除
    `cp "需要拷贝的文件路径" "目标地址路径"`
    拷贝文件 例如: cp /Users/goulela/Desktop/test.rtf /Users/goulela/Desktop/文件夹2
    `mv "需要移动的文件路径" "目标地址路径"`
    移动文件 例如: mv 文件夹2 /Users/goulela/Desktop/创建文件夹/文件夹

4.  显示操作

    *   `file "文件名"` 显示文件的类型
    *   `wc "文件名"` 统计文件的字符数,词数,行数

    *   `pwd` 查看当前所在的目录

    *   `ls` 显示当前路径下有什么文件

    *   `ls + "目录名"` 显示特定的路径下有什么文件
    *   `ls -w` 显示中文
    *   `ls -l` 详细信息
    *   `ls -a` 显示所有文件,包括隐藏文件
5.  时间操作

    *   `date` 显示系统的当前日期和时间
    *   `cal` 显示日历
6.  网络与通信

    *   `ping "url"` 给一个远程主机发送 回应请求
    *   终止ping打印 `control + c`
    *   `who` 列出当前登录的所有用户
    *   `whoami` 显示当前正进行操作的用户名

#### 符号解释

1.  `-r` 就是向下递归，不管有多少级目录，一并删除
2.  `-f` 就是直接强行删除，不作任何提示的意思
3.  `-rf` 向下递归强制删除

<div class="text-right">*摘自[简书，作者：满聪](http://www.jianshu.com/p/8f0433090e2b)*</div>
