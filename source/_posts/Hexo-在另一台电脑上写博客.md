---
title: Hexo--在另一台电脑上写博客
date: 2017-04-27 13:26:44
tags: Hexo 
categories: 原创 
---
** 换了个电脑写博客，把之前电脑的Hexo备份了一下，但是主题没备份，这次是重装Hexo，重新安装NexT主题。**

`2018年3月底` 第三次重装后重新整理，地址：[Hexo重装与位置移动](http://localhost:4000/2018/03/23/Hexo%E9%87%8D%E8%A3%85%E4%B8%8E%E4%BD%8D%E7%BD%AE%E7%A7%BB%E5%8A%A8/)

<!--more-->

# 安装HEXO

## 安装前准备

 ** 这些步骤是正常的安装`HEXO`的步骤。 **

### 在当前电脑上安装好git和nodejs

`git`是必须要安装的，安装`nodejs`是为了安装`hexo`方便。

### 添加SSH-Key到github上

#### 先进行全局配置

``` git
  git config --global user.email "xiao7shuang@qq.com"
  git config --global user.name "iSelly"
```

#### 生成ssh

  ```
   ssh-keygen -t rsa -C "xiao7shuang@qq.com"
  ```
  第一次回车是选择ssh生成的路径，直接按回车，默认生成在C盘下，可以自己自定义，注意盘符要大写；

  第二次回车是设置ssh的密码，如果直接按回车，在 `hexo d` 时，不用输密码即可部署到github上，如果设置了密码，则需要输入密码后才可以部署到github上。如果忘记了密码，貌似不能找回，只能重新生成ssh。

#### 添加SSH到github上

  用文本编辑器打开ssh.pub文件，拷贝其中的内容，将其添加到Add SSH Key

#### 验证

  ```
    ssh -T git@github.com
  ```

 然后输入yes

 >[SSH的使用](http://lql.life/2017/04/28/Hexo-SSH%E7%9A%84%E4%BD%BF%E7%94%A8/)

## 正式安装hexo

  ```
  npm install -g hexo
  ```
  然后，执行`init`命令初始化`hexo`到指定的目录
  ```
  hexo init <folder>
  ```
  也可以`cd`到目标目录，执行`hexo init`

  至此，全部安装工作已经完成。

  在`hexo init`目录下用`hexo s`测试是否正常

  > 参考地址  [hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)

# 迁移HEXO
1：将根目录下的`_config.yml`覆盖掉当前文件

2：把`source`文件夹覆盖掉当前文件夹

# 安装next主题

1：下载/克隆最新版

`cd your-hexo-site`

`git clone https://github.com/iissnan/hexo-theme-next themes/next`

或者去https://github.com/iissnan/hexo-theme-next/releases 下载，解压所下载的压缩包至站点的`themes`目录下，并将解压后的文件夹名称`hexo-theme-next-0.4.0`更改为`next`。

> 参考地址 [NexT--开始使用](http://theme-next.iissnan.com/getting-started.html)

2：启用主题

与所有`Hexo`主题启用的模式一样。当克隆/下载完成后，打开`站点配置文件`， 找到`theme`字段，并将其值更改为`next`。

# 要修改的地方

## High一下
将音乐放在`themes/next/source/music`文件夹下

在`themes/next/layout/_partials/header.swig`的64行添加代码 [High.js](https://github.com/Mine-MuYan/my_hexo/blob/master/high.js)

## sidebar的网易云音乐

在`themes/next/layout/_macro/sidebar.swig`的95行加入：

``` html
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=450 src="//music.163.com/outchain/player?type=0&id=601795690&auto=0&height=430"></iframe>
```

预览效果要先`hexo clean` -> `hexo g` 才可以，不然一直显示不出来。

# 注意

在`hexo d` 时：

如果出现 `error deployer not found:git` 的报错

这是因为 `hexo 3.0` 不适配 `hexo 2.0` 的bug

执行: `npm install hexo-deployer-git --save`  即可

