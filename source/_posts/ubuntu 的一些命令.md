---
title: ubuntu 的一些命令
date: 2019-03-22 18:20:00
tags: linux
categories: 原创
---

#### 关于用户

```shell
# 切换成root
sudo su		#这个时候输入的是lql的密码

# 输入root密码切换root用户
su - 或 su root 	#这个时候输入的是root的密码

# 重置root密码
sudo passwd

# 更改lql的权限为root

# 新增用户
adduser lql

# 新增用户组
groupadd webGroup

# 将用户添加到用户组中
usermod -g root lql

# 更改文件的所有者  chown 账号名称 文件或目录（-R递归）
chown -R lql web

# 更改文件的用户组  chgrp  组名   文件或目录
chgrp webGroup web

# 给目录配置权限
chmod -R 2775 web
# 2 - 打开 setGID 位，意味着新创建的子文件继承与目录相同的组，新创建的子目录继承父目录的 setGID 位。
# 7 - 为所有者提供 rwx 权限。
# 7 - 给组 rwx 权限。
# 5 - 为其他人提供 rx 权限。
```

<!--more-->

#### 修改默认启动级别

假设现在在图形用户界面，打开终端，输入如下命令：

```shell
 sudo systemctl set-default multi-user.target
```

现在如果想进入图形用户界面（仅进入一次，重启系统后仍然会进入命令行模式），可执行如下命令：

```shell
 sudo systemctl start lightdm
```

　　如果想设置为系统启动后默认进入图形用户界面，执行如下命令：

```shell
 systemctl set-default graphical.target
```

　　然后执行reboot命令重启系统即可。





#### 更换镜像源

1. 备份默认源地址

   `sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup`

2. 删除`sources.list`文件，并新建此文件，粘贴下面这些内容：

    ```shell
    # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse

    # 预发布软件源，不建议启用
    # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    ```

3. 更新源

   `apt-get update`

4. 软件操作的相关命令

   ```shell
   sudo apt-get update  更新源 
   sudo apt-get install package 安装包 
   sudo apt-get remove package 删除包 
   sudo apt-cache search package 搜索软件包 
   sudo apt-cache show package  获取包的相关信息，如说明、大小、版本等 
   sudo apt-get install package --reinstall   重新安装包 
   sudo apt-get -f install   修复安装 
   sudo apt-get remove package --purge 删除包，包括配置文件等 
   sudo apt-get build-dep package 安装相关的编译环境 
   sudo apt-get upgrade 更新已安装的包
   sudo apt-get dist-upgrade 升级系统 
   sudo apt-cache depends package 了解使用该包依赖那些包
   sudo apt-cache rdepends package 查看该包被哪些包依赖
   sudo apt-get source package  下载该包的源代码
   sudo apt-get clean && sudo apt-get autoclean 清理无用的包
   sudo apt-get check 检查是否有损坏的依赖
   ```



#### xshell/xftp登陆Win10 Linux子系统

1. 备份文件

   ```shell
   cd /etc/ssh
   sudo cp sshd_config sshd_config.bak
   ```

2. 修改sshd_config

   ```shell
   sudo vim sshd_config
   Port 2233  #修改端口
   ListenAddress 0.0.0.0  #打开本地监听
   #StrictModes yes  #注释掉
   PasswordAuthentication yes  #允许密码登陆
   ```

3. 重启ssh

   `sudo service ssh restart`

4. xshell/xftp登录

   ```shell
   # xshell连接
   ip：127.0.0.1
   协议：ssh
   端口：2233
   -------
   # xftp连接
   ip：127.0.0.1
   协议：sftp
   端口：22
   方式：password
   ```

   

#### 安装nginx+php+mysql

##### 安装nginx

1. 安装

   `apt-get -y install nginx`

   nginx默认根目录 `/usr/share/nginx/html`

2. 修改端口

   ```shell
   # 找到nginx.conf
   whereis nginx.conf
   # cd 到 /etc/nginx 目录下，在nginx.conf文件中没看到listen 80的字样，
   # 切换到/etc/nginx/sites-enabled/找到default文件
   server {
           listen 80 default_server;
           listen [::]:80 default_server;
   # 修改80为888
   # 重启nginx
   service nginx stop
   service nginx start
   ```

   

3. 配置文件

   ```shell
   server {
           listen 8881;
           listen [::]:8881;
   
           root /usr/share/nginx/web/yifu/public;
           index index.html index.htm index.nginx-debian.html index.php;
   
           server_name _;
   
           location / {
                   try_files $uri $uri/ =404;
           }
           location ~ \.php$ {
                   include snippets/fastcgi-php.conf;
                   fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
           }
   
   }
   ```



