---
title: 记一次Hexo升级
date: 2019-09-04 11:14:11
tags: Hexo 
categories: 原创 
---
本打算在工作不忙的时候，把博客中的丢失的图片重新补充下，为此还开通了阿里云oss服务，想把图片迁移到阿里云上。
在打开线上的博客后，发现特别慢，打开浏览器控制台后，发现`canvas`丢失了引用的js，而`fontawesome`的加载时间又特别长，经过一番搜索后先后修复了这两个问题，但是后来发现`hexo`和`next`的主题都不是最新版，我当前用的`hexo`是`3.7.0`，而最新版是`3.9.0`，`next`是`6.7.0`，而最新的是`7.4.0`，便先更新了`hexo`主题，发现冲突文件太多，一开始是一个一个文件合并，解决冲突，后来是在没耐心了就还原成之前的数据，因为之前这`next`主题中修改了很多文件，比如透明背景、萌萌哒等，如果要更新版本的话，这些都需要我再重新配置一遍，我的天，太多了，便放弃了更新`next`主题。后来就想，主题更新不了，把`hexo`升级下该没什么问题吧，于是便有了下面的这些操作，接口发现在打开页面的时候，会偶尔闪一下没有样式的页面，经过多方排查，后来发现是因为主题中`three_waves`插件导致的，就是这个波浪的动画特效，于是就先暂时将这插件禁用掉，果然没问题了，但是没有这个动画的话总感觉博客没灵魂了，得想办法还加上，不然怎么给人眼前一亮的感觉。

经过一番折腾后，怎么处理都不行，我甚至都有点想去github给作者提`issue`了，但是在看了项目唯一一个`issue`后，我突然觉得应该是我本地配置的问题，可能是升级`hexo`的问题，没办法又把`hexo`降回了原先的版本，果然没问题...

所以我讲`next`主题中.git文件夹删掉了，以后不再进行版本更新，根目录下的`package-lock.json`也加入了版本控制（之前一直都是用最新的版本，而且没事时就执行`npm install`，所以就没把`lock文件`加入版本控制），以后再迁移或更新的时候，关于`hexo`的执行命令就只有`hexo new ""`、`hexo clean`、`hexo g -d`了，不再执行`npm`相关的操作。下次有时间用`laravel`重新搭建一个博客，将博客迁移下。<!--more-->
 
下面是纠结的过程...
1. `next`主题不可以升级，现在用的是`6.7.0`版本，最新版是`7.4.0`，使用`git pull`升级时会覆盖掉本地之前修改的东西（背景透明、萌萌哒、代码主题等…），主题中的[next](https://github.com/theme-next/hexo-theme-next)主题已经删除版本控制，重新迁移时，要在主题文件夹下执行`npm install`；

2. 也不可以升级`hexo`的版本，如果升级的话，在升级时不能运行`hexo s`，否则会报错（主题中文件丢失），

   升级命令：

   ```
   用到的命令为：
   ncu 		// 检测package.json是否有更新
   ncu -u 	// 修改package.json中各依赖的版本 
   
   lql@lqls-MacBook-Air > ~/Project/Blog ncu
   Checking /Users/lql/Project/Blog/package.json
   [====================] 16/16 100%
   
    babel-eslint          ^10.0.1  →  ^10.0.3
    eslint                ^5.15.3  →   ^6.3.0
    hexo                   ^3.7.0  →   ^3.9.0
    hexo-deployer-git      ^1.0.0  →   ^2.0.0
    hexo-generator-feed    ^1.2.2  →   ^2.0.0
    hexo-helper-live2d     ^3.1.0  →   ^3.1.1
    hexo-renderer-ejs      ^0.3.1  →   ^1.0.0
    hexo-renderer-marked   ^0.3.2  →   ^2.0.0
    hexo-renderer-stylus   ^0.3.3  →   ^1.0.0
    hexo-server            ^0.3.1  →   ^1.0.0
   
   Run ncu -u to upgrade package.json
    lql@lqls-MacBook-Air > ~/Project/Blog ncu -u
   Upgrading /Users/lql/Project/Blog/package.json
   [====================] 16/16 100%
   
    babel-eslint          ^10.0.1  →  ^10.0.3
    eslint                ^5.15.3  →   ^6.3.0
    hexo                   ^3.7.0  →   ^3.9.0
    hexo-deployer-git      ^1.0.0  →   ^2.0.0
    hexo-generator-feed    ^1.2.2  →   ^2.0.0
    hexo-helper-live2d     ^3.1.0  →   ^3.1.1
    hexo-renderer-ejs      ^0.3.1  →   ^1.0.0
    hexo-renderer-marked   ^0.3.2  →   ^2.0.0
    hexo-renderer-stylus   ^0.3.3  →   ^1.0.0
    hexo-server            ^0.3.1  →   ^1.0.0
   
   Run npm install to install new versions.
   
    lql@lqls-MacBook-Air > ~/Project/Blog npm install
    ....
    
    lql@lqls-MacBook-Air > ~/Project/Blog hexo s
   ```
   
两个版本的对比：
![新版本](https://mine-blog.oss-cn-hangzhou.aliyuncs.com/blog/hexo/new.png?OSSAccessKeyId=LTAI4FnzWmZiZg5osdLPQNPi&Expires=1567657600&Signature=AQFLZtZnQKA4zPfa%2BDF59UG8LL0%3D)

![现在的版本](https://mine-blog.oss-cn-hangzhou.aliyuncs.com/blog/hexo/old.png?OSSAccessKeyId=LTAI4FnzWmZiZg5osdLPQNPi&Expires=1567657630&Signature=%2FRoARsWnJdODM8RheJh8ctKT91I%3D)