---
title: 解决安装Xdebug后访问本地项目特别慢
date: 2019-05-10 17:37:00
tags: PHP
categories: 原创
---

## 配置

```php
[Xdebug]
zend_extension = "E:\SoftWare\php\php-73\ext\php_xdebug-2.7.2-7.3-vc15-nts-x86_64.dll"
xdebug.remote_enable = 1
xdebug.remote_host = "127.0.0.1"
xdebug.remote_port = 9009
xdebug.remote_autostart = 1
xdebug.remote_handler = "dbgp"
xdebug.idekey = PHPSTORM
xdebug.profiler_enable = 0
xdebug.profiler_enable_trigger = 1
xdebug.default_enable = "Off"
xdebug.remote_mode = req
```

## 参考链接

[参考链接](https://stackoverflow.com/questions/17736304/enabling-xdebug-remote-debug-makes-apache-server-very-slow)

## 说明

1.  设置`xdebug.remote_enable = 0`或者`xdebug.remote_autostart = 0`，关掉这两项中的任意一项，就不会出现很慢的情况，但是关掉后就不能监听断点就行调试了;
2. `xdebug.remote_host`改成`127.0.0.1`，而不是`localhost`，phpstorm中的设置也是；
3. `xdebug.remote_port`只要是一个可用的端口并和phpstorm中的配置一样就行；
4. 剩下的配置的，按照配置就行了，不要问为什么，这样做才不会这么慢。