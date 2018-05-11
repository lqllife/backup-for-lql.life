---
title: win下MySQL 8.0.11的安装、修改密码、开启远程访问
date: 2018-05-11 10:23:26
tags: 原创
categories: MySQL
---

昨天匆匆安装了MySQL，并修改了密码、开启远程访问，现在记录分享下，希望对你能有所帮助。
安装环境：win server 2012 R2
MySQL版本：8.0.11 zip免安装版

<!--more-->

[下载地址](https://dev.mysql.com/downloads/mysql/)

下载后，进行解压，我的目录为：`D:\APP\MySQL`

### 新建配置文件 my.ini，内容如下：

```ini
[Client]
port = 3306
 
[mysqld]
port = 3306
basedir=D:/APP/MySQL
datadir=D:/APP/MySQL/data
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

#忘记密码时使用
#skip-grant-tables
#设置协议认证方式
default_authentication_plugin=mysql_native_password

[mysql]
default-character-set=utf8
```

### 设置环境变量
### 以管理员身份运行 cmd 否则可能会出现权限不足的问题，下列操作均在cmd中运行
### 安装并启动MySQL服务

`mysqld --install`

返回结果为 `Service successfully installed`

`net start MySQL` 或在服务中启动mysql服务

### 安装

`mysqld --initialize-insecure`
这个命令在运行时，光标一直闪，表示正在安装中，直到cmd可以进行输入，即是安装完成；
我看别人有这样安装的：
`mysqld --initialize --console`
两者的区别是上面这个会展示安装过程并生成一个默认的数据库密码，而我用的方法，不展示安装过程，默认密码为空。

### 设置密码

```mysql
use mysql;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '111111';
FLUSH PRIVILEGES;
```

这期间还涉及MySQL的密码认证插件是`mysql_native_password`，而现在使用的是`caching_sha2_password`。

因为当前有很多数据库工具和链接包都不支持`caching_sha2_password`，为了方便，我暂时还是改回了`mysql_native_password`认证插件。

在MySQL中执行命令：

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`
修改密码验证插件，同时修改密码。

如果想默认使用`mysql_native_password`插件认证，可以在配置文件中配置`default_authentication_plugin`项，这个在新建`my.ini`中已经设置过了。 [参考链接](http://www.jb51.net/article/139244.htm)

```
[mysqld]
default_authentication_plugin=mysql_native_password
```


### 开启远程访问

```mysql
CREATE USER 'root'@'%' IDENTIFIED BY '111111';
GRANT ALL ON *.* TO 'root'@'%';
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '111111';
```

![执行过程](https://upload-images.jianshu.io/upload_images/4950628-e9a0365eee4178fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/645)

在网上看到了两种[开启远程方法](http://www.bubuko.com/infodetail-2594533.html)的方法：
在使用第一种方法时会报这样的错误：

![报错](https://upload-images.jianshu.io/upload_images/4950628-aaf6135c438e3f68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/591)

开启远程访问这块还分为root账户的远程访问或者指定某个非root账户，网上有很多，就不说明了。

> 安装好后，至今还没有什么问题，若你预遇见了问题，欢迎和我交流。
