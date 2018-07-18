---
title: PHPStorm配置Xdebug
date: 2017-06-21 10:49:05
tags: [PHPStorm,Xdebug,PHP]
categories: 原创
---

配置分为两部分，一是`Xdebug`的下载和安装，二是`PHPStorm`上的对`Xdebug`的配置。

<!--more-->

## Xdebug下载

### 下载Xdebug

查看`phpinfo`，搞清楚php版本等信息：
![Markdown](https://upload-images.jianshu.io/upload_images/4950628-7b05d8470198581c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

根据PHP版本[下载](https://xdebug.org/download.php)合适版本的`Xdebug `

![Markdown](https:////upload-images.jianshu.io/upload_images/4950628-3b08f334e4825d79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  配置php.ini

在`php.ini`的最后加入下面这些代码。

`zend_extension`的路径是`Xdebug`文件的目录。

```php
[Xdebug]

zend_extension = C:\xampp\php\ext\php_xdebug-2.5.4-5.6-vc11.dll
xdebug.remote_enable = 1
xdebug.remote_host = localhost
xdebug.remote_port = 9000
xdebug.remote_handler = dbgp
xdebug.remote_autostart = 1
xdebug.idekey = PHPSTORM

```


### 重启apache

###  查看是否配置成功

#### phpinfo() 

![Markdown](https://upload-images.jianshu.io/upload_images/4950628-579afdd2e9205fe5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### PHPstorm上

![Markdown](https://upload-images.jianshu.io/upload_images/4950628-dfc628d2f22bcc87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Markdown](https://upload-images.jianshu.io/upload_images/4950628-3547f474af145503.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###  PHPstorm配置

**已下配置信息是根据我本地的配置，而做的配置，请勿直接复制。**

![Markdown](https://upload-images.jianshu.io/upload_images/4950628-70e87373ddcb3757.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**如上图：这里配置端口（9000）的需要和在php.ini中的配置要一致，不一定非要用9000端口，只要是一个未被占用的端口都行。**

![Markdown](https://upload-images.jianshu.io/upload_images/4950628-45e1f5d664947d3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图：

1. phpstorm中的IDE key需要和在php.ini中的配置要一致，只要两者配置一样都可以，不需要非要写成PHPSTORM；
2. Host和Port是要调试的本地虚拟域名配置项。

## 参考

###  PHPstorm官方文档

1. [Xdebug安装指南](https://confluence.jetbrains.com/display/PhpStorm/Xdebug+Installation+Guide)

2. [验证您的调试配置](https://confluence.jetbrains.com/display/PhpStorm/Validating+Your+Debugging+Configuration)

3. [使用Xdebug和PhpStorm进行零配置Web应用程序调试](https://confluence.jetbrains.com/display/PhpStorm/Zero-configuration+Web+Application+Debugging+with+Xdebug+and+PhpStorm)

### 其他

1. [phpstorm配置Xdebug进行调试PHP教程](http://www.jb51.net/article/58069.htm)
2. [[本地环境phpStorm10+XDebug配置和断点调试](http://www.cnblogs.com/xujian2016/p/5548921.html)](http://www.cnblogs.com/xujian2016/p/5548921.html)