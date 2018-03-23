---
title: PHP版本中的VC6,VC9,VC11,TS,NTS区别
date: 2017-06-21 9:49:18
tags: PHP
categories: 转载
---
`VC6：legacy Visual Studio 6 compiler`，就是使用这个编译器编译的
`VC9：Visual Studio 2008 compiler`，就是用微软的VS编辑器编译的
`TS：Thread Safe` 线程安全， 执行时会进行线程`（Thread）`安全检查
`NTS：Non Thread Safe` 非线程安全， 在执行时不进行线程`（Thread）`安全检查

<!--more-->

以`windows`为例，看看下载到得`php zip`的文件名

`php-5.4.4-nts-Win32-VC9-x86.zip`

`VC6：legacy Visual Studio 6 compiler`，就是使用这个编译器编译的。

`VC9：Visual Studio 2008 compiler`，就是用微软的VS编辑器编译的。

这个其实没有什么太大的影响，因为从`php 5.3`，已经没有`vc6`版本提供下载了

`TS：Thread Safe`线程安全， 执行时会进行线程`（Thread）`安全检查

`NTS：Non Thread Safe` 非线程安全， 在执行时不进行线程`（Thread）`安全检查

我使用`Apache+PHP`的模式下，一般是把`PHP`作为一个`Module load`到`apache`中，那么以`apache`父进程-多子进程的工作模式，是需要进行线程安全检查的，所以如果是以这种方式执行`php`，选择`ts`版本

那么如果是使用`fastcgi`，比如说用`php-fpm`管理`php`执行，则不需要进行线程安全检查，则选择`nts`版本的`php`

`php 5.5.0 beta 1`发布后, 安装出现问题, 家中电脑升级是成功的, 可公司的电脑一直提示无法加载到服务. 操作系统都一样的, 没什么区别`www.php.net`官网左侧的说明提醒了我.

`php-5.5.0beta1-Win32-VC11-x86`

安装包的名字也已经说明了, 要运行必须安装`vc11`, `x86`表示32位, 假如是`x64`就是64位, 位数对于安装`vc11`有帮助, 个人建议`vc11 x86`, `x64`两个版本都安装上比较好, 反正没冲突.

然后启动服务, 搞定`phpinfo`信息如下:

`VC6：legacy Visual Studio 6 compiler`，就是使用这个编译器编译的。

`VC9：Visual Studio 2008 compiler`，就是用微软的VS编辑器编译的。

由于`apache.or`g只提供`VC6`的版本，所以使用原版`apache`时只能使用`VC6`。（`www.apachelounge.com`上有`apache VC9`的版本提供，应该可以和`PHP VC9`配合，没用过）

`TS：Thread Safe` 线程安全， 执行时会进行线程`（Thread）`安全检查，以防止有新要求就启动新线程的`CGI`执行方式而耗尽系统资源

`NTS：Non Thread Safe` 非线程安全， 在执行时不进行线程`（Thread）`安全检查

`PHP`的两种执行方式：`ISAPI`和`FastCGI`。

`ISAPI``(Internet Server Application Programming Interface)`执行方式是以`DLL`动态库的形式使用，可以在被用户请求后执行，在处理完一个用户请求后不会马上消失，所以需要进行线程安全检查，这样来提高程序的执行效率，所以如果是以`ISAPI`来执行`PHP`，建议选择`hread Safe`版本

`apache`中的配置方式：

下面这个是加载`TS`版本的`php`必须的

`LoadModule php5_module “xxx/php5apache2_2.dll”`

下面这行可有可无

`AddType application/x-httpd-php-source .phpsAddType application/x-httpd-php .php .php5 .php4 .php3 .phtml .phpt`

`FastCGI`执行方式是以单一线程来执行操作，所以不需要进行线程的安全检查，除去线程安全检查的防护反而可以提高执行效率，所以，如果是以`FastCGI`来执行`PHP`，建议选择`Non Thread Safe`版本。

`apache`中的配置方式：

下面这两行是加载NTS版本的php必须的，不可以直接写成`Action application/x-httpd-php “c:/wamp/bin/php/php3.5.6/php-cgi.exe”！`

``` php
ScriptAlias /php/ "C:/wamp/bin/php/php3.5.6/"
Action application/x-httpd-php “/php/php-cgi.exe”
```

另外，还要有之前的`AddType application/x-httpd-php .php .php5 .php4 .php3 .phtml .phpt`，这样才能认识`php`格式的文件

这样配置完可能还会因为权限问题而无法用php-cgi.exe解析php网页，所以还要加上下面这段

``` php
<Directory "C:/wamp/bin/php/php5.3.6/">
AllowOverride None
Options None
Order allow,deny
Allow from all
</Directory>
```
官方并不建议你将`Non Thread Safe`应用于生产环境，所以我们选择`Thread Safe`版本的PHP来使用。

XAMPP在`http-xampp.conf`中默认配置是使用`ISAPI`的方式

原文：[PHP版本中的VC6,VC9,VC11,TS,NTS区别](http://www.tuicool.com/articles/6RF7be6)