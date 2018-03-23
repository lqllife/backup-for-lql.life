---
title: Git常用命令图解
date: 2017-05-05 09:02:01
tags: 转载
categories: git
---

Git 是一款非常优秀的版本控制工具，但是苦于Git晦涩难懂的man pages，还有众多的命令选项和怪异的用法，Git有点难学。

这篇文章分享我学习过程中收藏的一些好图，并围绕这些图讲讲我对Git的理解，希望对大家有所帮助。

<!--more-->


## Git 工作流程

了解Git，首先要弄清楚对象在被Git管理过程中所处的4个阶段，分别是：

1）工作目录

2）index(又称为暂存区)

3）本地仓库

4）远程仓库

管理过程图如下：

[![git-commands-commonly-used-graphic-0](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-0.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-0.png)

从时间先后来讲，工作目录的内容是你当前看到的，也是最新的；

index区标记了你当前工作目录中，哪些内容是被git管理的；

而本地仓库保存了对象被提交过的各个版本，比起工作目录和暂存区的内容，它要更旧一些；

远程仓库是本地仓库的异地备份，远程仓库的内容可能被分布在多个地点的处于协作关系的本地仓库修改，因此它可能与本地仓库同步，也可能不同步， 但是它的内容是最旧的  。

任何对象都是在工作目录中诞生和被修改；任何修改都是从进入index区才开始被版本控制；只有把修改提交到本地仓库，该修改才能在仓库中留下痕迹；而要与协作者分享本地的修改，可以把它们push到远程仓库来共享。

图最上方的add、commit、push等，展示了git仓库的产生过程。反过来，我们可以从远程历史仓库中获得本地仓库的最后一个版本，clone到本地，从本地检出对象的各个版本到index暂存区或工作目录中，从而实现任何对象或整个仓库的任意阶段状态的”回滚”。当正向和反向都能自由切换后，git就强大到无所不能了。

一开始接触这些概念可能比较绕，其实在git入门阶段，可以先抛开远程仓库不看，只了解管理本地仓库的”3棵树”就够了。

如下图：

[![git-commands-commonly-used-graphic-1](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-1.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-1.png)

## Git常用命令

在开始之前，我们需要把下面的图看懂：

[![git-commands-commonly-used-graphic-2](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-2.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-2.png)

**上图说明：**

*   HEAD，头，它始终指向当前所处分支的最新的提交点。你所处的分支变化了，或者产生了新的提交点，HEAD就会跟着改变。
*   working directory，它是你的工作目录，也是当前你看到的东西。你的工作目录是与版本、分支相关的。
*   stage的东西虽然看不见，但是执行git status就会看到哪些对象的修改将在下一次commit的时候被放进本地仓库。这些东西称为stage。

### commit

commit把暂存区的内容存入到本地仓库，并使得当前分支的HEAD向后移动一个提交点。

如果对最后一次commit不满意，可以使用 `git commit --amend `来进行撤销，修改之后再提交。

如图所示的，ed489被4ca87取代，但是git log里看不到ed489的影子，这也正是amend的本意：原地修改，让上一次提交不露痕迹。

[![git-commands-commonly-used-graphic-3](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-3.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-3.png)

### checkout

checkout用来检出并切换分支。checkout成功后，HEAD会指向被检出分支的最后一次提交点。

对应的，工作目录、暂存区也都会与当前的分支进行匹配。下图是执行 `git checkout maint `后的结果：

[![git-commands-commonly-used-graphic-4](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-4.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-4.png)

### reset

reset命令把当前分支指向另一个位置，并且相应的变动工作目录和索引。

如下图，执行 `git reset HEAD~3 `后，当前分支相当于回滚了3个提交点，由ed489回到了b325c：

[![git-commands-commonly-used-graphic-5](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-5.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-5.png)
reset有3种常用的模式：

–soft，只改变提交点，暂存区和工作目录的内容都不改变
–mixed，改变提交点，同时改变暂存区的内容。这是默认的回滚方式
–hard，暂存区、工作目录的内容都会被修改到与提交点完全一致的状态

### diff

在commit、merge、rebase、打patch之前，通常都需要看看这次提交都干了些什么，于是diff命令就派上用场了：

[![git-commands-commonly-used-graphic-6](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-6.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-6.png)

来比较下上图中5种不同的diff方式：

1.  比较不同的提交点之间的异同，用 `git diff 提交点1 提交点2 `
2.  比较当前分支与其他分支的异同，用 `git diff 其他分支名称 `
3.  在当前分支内部进行比较，比较最新提交点与当前工作目录，用 `git diff HEAD `
4.  在当前分支内部进行比较，比较最新提交点与暂存区的内容，用 `git diff --cached `
5.  在当前分支内部进行比较，比较暂存区与当前工作目录，用 `git diff `
    看起来有点复杂？是的，记不住的时候就看看这些图吧。

### merge  

merge命令把不同的分支合并起来。

如下图，HEAD处于master分支的ed489提交点上，other分支处于33104提交点上，项目负责人看了下觉得other分支的代码写的不错，于是想把代码合并到master分支，因此直接执行 `git merge other `，如果没有发生冲突，other就成功合并到master分支了。

[![git-commands-commonly-used-graphic-7](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-7.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-7.png)

### rebase

rebase又称为衍合，是合并的另外一种选择。

merge把两个分支合并到一起进行提交，无论从它们公共的父节点开始(如上图，other分支与master分支公共的父节点b325c)，被合并的分支(other分支)发生过多少次提交，合并都只会在当前的分支上产生一次提交日志，如上图的f8bc5。所以merge产生的提交日志不是线性的，万一某天需要回滚，就只能把merge整体回滚。而rebase可以理解为verbosely merge，完全重演下图分支topic的演化过程到master分支上。如下图：

[![git-commands-commonly-used-graphic-8](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-8.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-8.png)
在开始阶段，我们处于topic分支上，执行 `git rebase master `，那么169a6和2c33a上发生的事情都在master分支上重演一遍，分别对应于master分支上的e57cf和f7e63，最后checkout切换回到topic分支。

这一点与merge是一样的，合并前后所处的分支并没有改变。 `git rebase master `，通俗的解释就是topic分支想站在master的肩膀上继续下去。

### cherry-pick

cherry-pick命令复制一个提交点所做的工作，把它完整的应用到当前分支的某个提交点上。

rebase可以认为是自动化的线性的cherry-pick。

例如执行 `git cherry-pick 2c33a `:

[![git-commands-commonly-used-graphic-9](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-9.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-9.png)

### 正反过程对比

理解了上面最晦涩的几个命令，我们来从正反两个方向对比下版本在本地的3个阶段之间是如何转化的。

如下图(history就是本地仓库)：

[![git-commands-commonly-used-graphic-10](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-10.png)](http://blog.mimvp.com/wp-content/uploads/2015/02/git-commands-commonly-used-graphic-10.png)

如果觉得从本地工作目录到本地历史库每次都要经过index暂存区过渡不方便，可以采用图形右边的方式，把两步合并为一步。

## Some Tips

### 如何管理空文件夹

git本身不会对空文件夹进行版本控制，如果希望项目被clone后自带一些空目录，那么可以：

*   用git实现远程备份在要被管理的空目录下创建.gitignore文件。
*   在.gitignore文件内写入如下代码。第一行忽略所有文件。第二行除了.gitignore文件不被忽略。
    `* `
    `!.gitignore `

git本地仓库的初始化采用 `git init . `即可。如果需要搭建异地备份，除了可以使用开源的gitlab等来搭建服务器外，还可以 `git --bare init . `来初始化远服务器仓库，然后把本地仓库推送到服务器仓库。这样可以轻松实现代码或文档的异地备份，即使把代码备份到本地也是可以的(这或许可以避免rm -rf *的悲剧哦)：
 `git remote add origin git://127.0.0.1/abc.git `
然后建立本地到本地的ssh信任关系，enjoy yourself！

### 无密码登陆git服务器

调试技巧：`ssh -v git@github.com`

### GIT与知识管理

日常工作中，我习惯用git来管理github和本地的代码；也用git来管理各种配置文件，例如.vimrc，.bashrc等，以及各种技术文档。

感谢Linux大神为我们写了这么好的工具！

<div class="text-right">*摘自[米扑博客](http://blog.mimvp.com/2015/02/git-commands-commonly-used-graphic/)*</div>