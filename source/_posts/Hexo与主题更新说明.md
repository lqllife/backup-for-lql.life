---
title: Hexo与主题更新说明
date: 2019-09-04 11:14:11
tags: Hexo 
categories: 原创 
---

1. `next-reloaded`主题不可以升级，现在用的是6.7.0版本，最新版是7.4.0，使用`git pull`升级时会覆盖掉本地之前修改的东西（背景透明、萌萌哒、代码主题等…）；

2. 可以升级hexo的版本，但是在升级时不能运行`hexo s`，否则会报错（主题中文件丢失），

   <!--more-->

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