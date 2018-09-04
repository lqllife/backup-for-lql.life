---
title: Git多分支平行发展（一个仓库包含多个不同的项目）
date: 2018-06-29 16:11:49
tags: 原创
categories: git
---

### 背景
最近在用laravel开发微信小程序的接口，因为服务器PHP版本的问题，分别用了`laravel 5.6(php 7.1，开发环境)`  和 `laravel 5.4 (php 5.6，服务器环境)`，开发完成后，两个项目绝大部分的代码都差不多，不想再建一个仓库放`php 5.6`版的代码，便试着在原有仓库新建分支来存储`php 5.6`的项目，捣鼓的一上午终于搞出来了，现在记录下。

主要步骤：
1. 在远程和本地仓库中新建一个分支（我建的是`laravel54`）；
2. 删除远程`laravel54`分支上的所有文件；
3. 将新项目的文件推送到`laravel54`上。

文章中的代码没有高亮化，看着可能不太舒服，其他平台地址：[segmentfault](https://segmentfault.com/a/1190000015430066) [简书](https://www.jianshu.com/p/922590337264)

<!--more-->

### 流程

#### 新建一个文件夹(我的是laravel5.4)
#### 创建git本地仓库并关联远程仓库

```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4
$ git init
Initialized empty Git repository in D:/PHP/xampp/htdocs/apple/laravel5.4/.git/

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git remote add origin git@xxxxxxxxxxxxxxxxxxxxx.git

```
#### 建立远程仓库分支 laravel54

注意，建立远程分支后，暂时还不能在本地建立其他分支，因为刚创建的git仓库默认的master分支要在第一次commit之后才会真正建立，这时创建其他分支会报错，解决办法在后面说明。

```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git branch laravel54
fatal: Not a valid object name: 'master'.
```

####  随便新建一个文件，里面可以不用写任何东西，然后commit
    
```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ touch clearTheBranch

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git add clearTheBranch

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git commit -m'clean this branch'
[master (root-commit) c7c5349] clean this branch
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 clearTheBranch
```
    
####  推送之前先更新（下面的代码是最核心的代码）
    
新建的分支默认与master分支一样，所以要在推送之前先把分支上的内容拉下来。

```git

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git pull origin laravel54
warning: no common commits
remote: Enumerating objects: 2453, done.
remote: Counting objects: 100% (2453/2453), done.
remote: Compressing objects: 100% (2320/2320), done.
Receiviremote: Total 2453 (delta 1547), reused 229 (delta 89)
Receiving objects: 100% (2453/2453), 1.40 MiB | 1.38 MiB/s, done.
Resolving deltas: 100% (1547/1547), done.
From xxxxxxxxxxxxxxxxxxxxx
 * branch            laravel54 -> FETCH_HEAD
 * [new branch]      laravel54 -> origin/laravel54
fatal: refusing to merge unrelated histories

```

这个时候会报错，是正常现象，，原因是本地仓库和远程仓库不一样，有类似两者有完全不相同的commit历史，只需要在在pull的同时加上一行代码即可，代码如下：

```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$  git pull origin laravel54 --allow-unrelated-histories
From xxxxxxxxxxxxxxxxxxxxx
 * branch            laravel54 -> FETCH_HEAD
Merge made by the 'recursive' strategy.
...
```

这步操作的时候，会有merge的commit说明，简单写下，这个只是merge的说明，并不会真正和mater合并。
经过这一步操作，就已经把远程仓库laravel54分支里面的文件都拉下来了，接下来建立与远程仓库对应的本地分支。
    
#### 建立并切换到本地分支 laravel54
    
```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git branch laravel54

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
$ git checkout laravel54
Switched to branch 'laravel54'

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$

// 也可以直接用 git checkout -b laravel54

```

####  删除本地仓库里的所有文件（除了.git的文件夹），然后推送
    
```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git add .

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git commit -m'delete all files'
[laravel54 d8bf12d] delete all files
215 files changed, 32019 deletions(-)
...

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git push origin laravel54
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 683 bytes | 341.00 KiB/s, done.
Total 6 (delta 1), reused 1 (delta 0)
remote: Powered by Gitee.com
To xxxxxxxxxxxxxxxxxxxxx.git
   c810298..d8bf12d  laravel54 -> laravel54

```
这个时候，远程仓库的`laravel54`分支便和本地仓库的`laravel54`分支一样都是空白的，这样就可以随心所欲的推送了。
    
#### 新项目推送  
 
这时就可以把之前的项目拷进这个文件夹里面，然后按照正常的推送流程进行推送。

```git
沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git add .
warning: LF will be replaced by CRLF in .env.example.
The file will have its original line endings in your working directory.
...

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git commit -m'laravel54 push'
[laravel54 eba0954] laravel54 push
 113 files changed, 11583 insertions(+)
 create mode 100644 .env.example
 create mode 100644 .gitattributes
 ...

沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
$ git push origin laravel54
Enumerating objects: 152, done.
Counting objects: 100% (152/152), done.
Delta compression using up to 4 threads.
Compressing objects: 100% (126/126), done.
Writing objects: 100% (151/151), 211.43 KiB | 2.94 MiB/s, done.
Total 151 (delta 14), reused 42 (delta 4)
remote: Resolving deltas: 100% (14/14), done.
remote: Powered by Gitee.com
To xxxxxxxxxxxxxxxxxxxxx.git
   d8bf12d..eba0954  laravel54 -> laravel54

```
    
### 小总结

#### 不用担心推送错分支导致分支合并

经过如上操作，就可以实现master分支和laravel54是两个完全不同的项目，可以保持两个项目平行发展，因为两个分支的提交历史不一样，所以即便是误操作，提交错了分支，git会给出相应的报错，不会把两个分支直接合并，下面是代码演示：

1. 本地laravel54向远程master分支推送  

    ```git
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ touch a.txt
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git add a.txt
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git commit -m'test file a.txt'
    [laravel54 e246705] test file a.txt
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 a.txt
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git push origin master
    To xxxxxxxxxxxxxxxxxxxxx.git
     ! [rejected]        master -> master (fetch first)
    error: failed to push some refs to 'git@xxxxxxxxxxxxxxxxxxxxx.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

    //本地版本回退
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git log
    commit e246705e9894a658d3fb2ea8f51b1ee0dd8ff834 (HEAD -> laravel54)
    Author: muyan <xiao7shuang@qq.com>
    Date:   Fri Jun 29 14:50:35 2018 +0800
    
        test file a.txt
    
    commit eba09541b60e496127f6280c3db9611f72504744 (origin/laravel54)
    Author: muyan <xiao7shuang@qq.com>
    Date:   Fri Jun 29 14:36:48 2018 +0800
    
        laravel54 push
    
    commit d8bf12d9df1337f9f7e0de723608796a490f66dc
    Author: muyan <xiao7shuang@qq.com>
    Date:   Fri Jun 29 14:24:02 2018 +0800
    
        delete all files
    
    commit dcfb2b00f4f8ad0f1f672cde9b9e9ddc79d85239 (master)
    Merge: c7c5349 c810298
    Author: muyan <xiao7shuang@qq.com>
    Date:   Fri Jun 29 13:56:24 2018 +0800
    
        Merge branch 'laravel54' of xxxxxxxxxxxxxxxxxxxxx
    
        for claering the branch
    
    commit c7c5349779f868fbc1cf18d742d634c9d3267c32
    Author: muyan <xiao7shuang@qq.com>
    Date:   Fri Jun 29 13:55:01 2018 +0800
    
        clean this branch

    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git reset eba09541b60e496127f6280c3db9611f72504744    //回退到上一次提交之前的版本

    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (laravel54)
    $ git status
    On branch laravel54
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
            a.txt
    
    nothing added to commit but untracked files present (use "git add" to track)

    ```

2.  本地master分支向远程master分支推送
   
    ```git
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
    $ git add a.txt
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
    $ git commit -m 'test file a'
    [master d3e8bba] test file a
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 a.txt
    
    沐@ MINGW64 /d/PHP/xampp/htdocs/apple/laravel5.4 (master)
    $ git push origin master
    To xxxxxxxxxxxxxxxxxxxxx.git
     ! [rejected]        master -> master (fetch first)
    error: failed to push some refs to 'git@xxxxxxxxxxxxxxxxxxxxx.git'
    hint: Updates were rejected because the remote contains work that you do
    hint: not have locally. This is usually caused by another repository pushing
    hint: to the same ref. You may want to first integrate the remote changes
    hint: (e.g., 'git pull ...') before pushing again.
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

    ```
    
    
#### 理论上可以实现无限平行分支，同样若一个项目中有版本（稳定版、开发版等），也可以跟据此思路扩展。
