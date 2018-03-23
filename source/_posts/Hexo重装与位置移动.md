---
title: Hexo重装与位置移动
date: 2018-03-23 15:34:54
tags: Hexo 
categories: 原创 
---

　　之前写过一篇关于[Hexo--在另一台电脑上写博客](http://lql.life/2017/04/27/Hexo-%E5%9C%A8%E5%8F%A6%E4%B8%80%E5%8F%B0%E7%94%B5%E8%84%91%E4%B8%8A%E5%86%99%E5%8D%9A%E5%AE%A2/)的博文，当时对hexo并不是很熟悉，稀里糊涂搞好后，便匆匆记录了下来。在今天重装的时候，才发现是自己给自己挖坑了，其实无论是换台电脑写博客或者重新安装都很简单，现在详细说明下。

<!--more-->

## 基础环境搭建

### 安装hexo

　　电脑中已经安装了`git`和`node.js`，直接随便找个文件夹在 `git bash` 中执行 `npm install -g hexo-cli`，这一步是安装`hexo`。
检测`hexo`是否成功安装，在 `git bash` 执行 `hexo v` 看到`hexo`的版本号就说明安装成功了。

　　之后创建本地`hexo`仓库，`hexo init <folder>` 也可以`cd`到目标目录，执行`hexo init`（这里用`blog`文件夹做说明 `hexo init <blog>` 或者新建一个`blog`的文件夹然后执行 `hexo init`），这一步文件夹内会生成一些基本的文件，这些都是`hexo`自动部署的，跟`git init` 同一个道理。
　　然后 执行 `npm install hexo-deployer-git --save` 安装`hexo`提供的快速方便的一键部署功能，然后改 `_config.yml` 中最下面的配置文件：

```
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```

　　至此，全部安装工作已经完成，可以在`blog`的目录下用`hexo s`测试是否正常。


### ssh生成

　　若本机中已经有`ssh`，直接添加到`github`的`ssh`里面即可，本地没有话，先生成  `ssh-keygen -t rsa -C "qq-email@qq.com"` （执行期间会让选择放置ssh的目录和ssh密码），用文本编辑器打开ssh.pub文件，拷贝其中的内容，将其添加到 `github`的 `Add SSH Key`，添加完成后用 `ssh -T git@github.com`若提示 `Hi *****! You've successfully authenticated, but GitHub does not provide shell access.`，这就说明与`github`连接成功了。


## 重装和迁移

### 重装

　　这个概念跟网上说的，如何在另外一台电脑写博客是一个意思，没有很多繁琐的操作，**只需要把 `source`、`themes` 、 `_config.yml` 覆盖到 `blog` 的那个文件夹即可** ，只要`hexo`环境没问题，`hexo`的一系列操作都没问题（在第一次`hexo d`可能会有些慢，耐心等待）。
　　同样，在新电脑上只要把环境搭建好，只要把两个文件夹一个配置文件覆盖过去，都没问题。
　　包括在备份时也只需要把这两个文件夹一个配置文件备份好，其他的都不用管了。

### 迁移

　　至于说迁移，只要本机的`hexo`环境没问题，把`blog`文件夹随便放哪都可以。


## 总结

　　折腾了一下午总结出来的：

1. 换系统或者换电脑需要重装 `hexo` 的时候，只需要在`hexo`环境搭建好的基础上，只需要把 `source`、`themes` 、 `_config.yml` 覆盖到新的博客目录下即可。
2. 只要本机的`hexo`环境没问题，把`blog`文件夹随便放哪都可以。
3. 应该是`hexo`版本更新了的缘故，`hexo d`时只能在`git bash`中执行，其他（window环境）命令行模式都是提示无法获取远程仓库或者没有权限。

