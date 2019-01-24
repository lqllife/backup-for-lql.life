---
title: xshell/xftp登陆Win10 Linux子系统
date: 2019-01-23 11:51:49
tags: linux
categories: shell
---

### 备份文件

   ```shell
   cd /etc/ssh
   sudo cp sshd_config sshd_config.bak
   ```

<!--more-->

### 修改sshd_config

   ```shell
   sudo vim sshd_config
   Port 2233  #修改端口
   ListenAddress 0.0.0.0  #打开本地监听
   #StrictModes yes  #注释掉
   PasswordAuthentication yes  #允许密码登陆
   ```

### 重启ssh

   `sudo service ssh restart`

### xshell登录

   ```shell
   ip：127.0.0.1
   协议：ssh
   端口：2233
   ```
   
### xftp登录

   ```shell
   ip：127.0.0.1
   协议：sftp
   端口：22
   方式：password
   ```
