---
title: HEXO--绑定域名&公益404页面
date: 2017-04-28 10:51:34
tags: Hexo
categories: 原创
---

这篇博客主要是记录 `Hexo` 绑定域名和添加腾讯404公益页面。
<!--more-->

## 绑定域名

** 所需材料：**

1.域名 - 已在 [godaddy](https://sg.godaddy.com/zh?ci=/) 购买。

2.DNSpod账号 - 免费[注册](https://www.qcloud.com/login?s_ur=)或者使用QQ登录。
​    
### 建立CNAME文件

在根目录下的 `source` 目录下创建一个 `CNAME` 文件，不带后缀，在文件中写入域名 `lql.life`。

可通过编辑器创建或者在git中输入 `touch CNAME` -> `vim CNAME` ，然后按 `i` 输入 `lql.life`，按 `Esc` 再输入 `:wq` 回车即可。

最后一步是 `hexo g -d` 生成本地静态页面并部署到github的仓库上。

### 记录博客的iP地址

在git中输入 `ping Mine-MuYan.github.io`

![Markdown](//upload-images.jianshu.io/upload_images/4950628-5981bd2e4fb03a16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 使用DNSpod解析域名

DNSpod解析域名这里使用DNSpod来进行解析, 原因：很快，免费。

登录DNSpod：可以直接用QQ可以登录点击进入添加记录页面在DNSPod中添加“A记录”，注意要添加带“www”的和不带“www”两个记录,分别是@和www的ip都填之前解析出来的github网站ip。

![Markdown](//upload-images.jianshu.io/upload_images/4950628-db7fff904332e9a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### godaddy设置DNS

在godaddy的[设置域名的DNS](https://dcc.godaddy.com/manage/lql.life/dns)把域名服务器改成下面两个。

![Markdown](//upload-images.jianshu.io/upload_images/4950628-910767a08a90df94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`f1g1ns1.dnspod.net` 和 `f1g1ns2.dnspod.net`，等待一会，访问 `lql.life` 就可。

### 整体预览

这是当访问域名可以访问到博客时的域名DNS管理页面。

![Markdown](//upload-images.jianshu.io/upload_images/4950628-fec694a874a57083.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 设置404页面

### 腾讯404页面的作用：

1.当访问到站内不存在的页面时，会跳转此页，比github的404页面好看些；

2.腾讯公益404页面，寻找丢失儿童，让大家一起关注此项公益事业！

### 操作说明

新建 404.html 页面，放到`主题目录`下的 `source` 目录下，内容如下：
```html
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/"
          homePageName="返回博客主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```

最后一步是 `hexo g -d` 生成本地静态页面并部署到github的仓库上。

[技术支持](http://theme-next.iissnan.com/theme-settings.html#volunteer-404)

### 注意
1.只能在绑定顶级域名的情况下，404页面才能生效；

2.本地 `http://localhost:4000` 不生效。

