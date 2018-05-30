---
title: Hexo--SSH的使用
date: 2017-04-28 13:42:14
tags: Hexo
categories: 原创
---
`SSH keys` - 让本地`git`项目与远程的`github`建立联系。

这篇博客主要介绍 `SSH` 的生成、生成多个 `SSH` 。

<!--more-->

## 首次生成SSH

### 检查SSH keys的设置

首先需要检查电脑上现有的`ssh key`：

```
$ cd ~/. ssh 检查本机的ssh密钥
```

如果提示：`No such file or directory` 说明是在当前电脑上第一次使用`git`。

### 生成新的SSH Key：

```
$ ssh-keygen -t rsa -C "邮件地址@youremail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
```

注意1: 此处的邮箱地址，可以输入自己的邮箱地址；

注意2: 此处的 `C` 的是大写的 `C` ；

然后系统会要输入密码：

```
Enter passphrase (empty for no passphrase):<输入加密串>
Enter same passphrase again:<再次输入加密串>
```

在回车中会提示输入一个密码，这个密码会在提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往项目里提交内容。

注意：输入密码的时候没有 `*` 字样的，直接输入就可以了。



### 添加SSH Key到GitHub

在本机设置 `SSH Key` 之后，需要添加到 `GitHub` 上，以完成 `SSH` 链接的设置。

*   1、打开本地 `C:\Documents and Settings\Administrator\.ssh\id_rsa.pub` 文件。此文件里面内容为刚才生成人密钥。如果看不到这个文件，需要设置显示隐藏文件。准确的复制这个文件的内容，才能保证设置的成功。

*   2、登陆 `github` 系统。点击右上角的 `Account Settings`--->`SSH Public keys` ---> `add another public keys`

*   3、把本地生成的密钥复制到里面（key文本框中）， 点击 `add key` 就ok了


### 测试

可以输入下面的命令，看看设置是否成功，`git@github.com` 的部分不要修改：

```
$ ssh -T git@github.com
```

如果是下面的反馈：

```
The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?
```

不要紧张，输入 `yes` 就好，然后会看到：

```
Hi xxx! You've successfully authenticated, but GitHub does not provide shell access.
```

## 生成多个shh

### 1.生成指定名字的密钥

`ssh-keygen -t rsa -C "邮箱地址" -f ~/.ssh/github_jslite`

> 会生成 `github_jslite` 和 `github_jslite.pub` 这两个文件

### 2.密钥复制到托管平台上

`vim ~/.ssh/github_jslite.pub`

> 打开公钥文件 `github_jslite.pub` ，并把内容复制至代码托管平台上

### 3.修改config文件

`vim ~/.ssh/config` #修改config文件，如果没有创建 `config`

``` git
Host jslite.github.com
HostName github.com
User git
IdentityFile ~/.ssh/github_jslite

Host abc.github.com
HostName github.com
User git
IdentityFile ~/.ssh/github_abc

```

### 添加到博客的config文件中

```
git remote add origin git@jslite.github.com:JSLite/JSLite.git 
```

### 4.测试

`ssh -T git@jslite.github.com` # `@`后面跟上定义的 `Host`。

此部分摘自SegmentFault[小弟调调](https://segmentfault.com/q/1010000002599364)的回答。