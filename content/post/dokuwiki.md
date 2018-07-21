---
title: "开源 DokuWiki 安装"
date: 2018-07-21T19:04:55+08:00
url: "2018/07/21/dokuwiki"
categories: ["deploy"]
tags: ["linux","php"]
banner: "imgs/dokuwiki.jpg"
---

技术团队要搭建一个文档网站，对比了好多开源的 wiki 软件.

<!--more-->

dokuwiki最简单，可以实现分分钟快速搭建，界面漂亮，功能全，可以索引提取等等；

## 优点
* 用dokuwiki，只要能运行php的就能用，不需要数据库；每一个你保存的wikiword其实都是以txt保存的。
* 在内网任何一台电脑上都可以访问，设了vpn或动态域名的话，外网也可以访问。
* 自己用，不在意安全性的问题的话，把文件夹压缩成zip包，直接迁移。
* 各种插件丰富，中文资源尚可，我自己装了能够显示freemind格式导图文件的插件。
* 以前的老毛病，什么不能删缓存旧版本了，现在都有插件解决了。
* 换行别扭也有插件解决了，用起来跟论坛，跟知呼一模一样。
* 模版文件很少，简单修改一下，不用太花哨，主要是资料记载。
* 记录密码，可以用插件进行加密，需要看到的话，需要登录，需要看密码的钥匙。
* 嘿嘿。。H1~H6，定制一下，非常清晰。
* 代码高亮足够漂亮，还有更漂亮的console插件。

## 安装
Step 0: Make sure your server meets the requirements.
Step 1: Download the newest release from the Download Page.
Step 2: Read the security page before you begin installing. Take it seriously. If in doubt, ask on the mailing list
Step 3: Unpack the distribution tarball and upload/copy the files to your webspace.
Step 4: Open the install.php file in your browser and follow the instructions. (For additional information, see the installer page.)
Step 5: Enjoy your DokuWiki install and browse through the manual to discover what you can do with it.

* [DokuWiki官网](https://www.dokuwiki.org)
<!--more-->
