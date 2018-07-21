---
title: "利用 Directory Lister 搭建简单实用的目录列表"
date: 2018-07-21T15:52:55+08:00
url: "2018/07/21/directory-lister"
categories: ["deploy"]
tags: ["linux","翻墙"]
banner: "imgs/directory-lister.png"
---

在之前的 《[Ubuntu 16 下安装 NextCloud 及配置](http://cyber-life.cn/2018/07/14/ubuntu16-nextcloud/)》 文章中，分享过可以利用免费开源的 nextcloud 搭建私有云存储，但是这对于个人来说没有必要，比如在写博客的时候有需要分享的脚本和程序，还是存储在简单的 FTP 空间中，搭建一个简单的目录列表程序（Directory Lister）比较好。

<!--more-->

## 环境要求

Directory Lister 要求 PHP 5.3+ 版本以上. 更多关于 PHP 的信息, 可以参考 http://www.php.net.

## 安装

1. 下载和解压 Directory Lister
2. 拷贝文件

```
cp resources/default.config.php resources/config.php
```

3. 将 index.php 文件和 resources 目录放置到您想要展示的目录下，例如我把它们放置到

```
mv index.php /var/www/html/nextcloud/data/thomas/files/sfw
mv resources /var/www/html/nextcloud/data/thomas/files/sfw
```

4. 之后上传文件到 index.php 所在目录下即可
5. 为了在主站访问点直接访问到此网页，我加了一个软链接

```
sudo ln -s /var/www/html/nextcloud/data/thomas/files/sfw/index.php /var/www/html/index.php
sudo ln -s /var/www/html/nextcloud/data/thomas/files/sfw/linux /var/www/html/linux
sudo ln -s /var/www/html/nextcloud/data/thomas/files/sfw/macos /var/www/html/macos
```

6. 隐藏用户不应访问到目录，修改文件

```
vi /var/www/html/nextcloud/data/thomas/files/sfw/resources/config.php
// Hidden files
'hidden_files' => array(
    '.ht*',
    '*/.ht*',
    'resources',
    'resources/*',
    'analytics.inc',
    'header.php',
    'footer.php',
    'index.html.bak',
    'nextcloud',
    'nextcloud/*'
),
```

7. 演示效果 http://home.cloudancing.cn

* [directorylister官网](http://www.directorylister.com)
* [5款简单实用的免费目录列表程序](http://www.laozuo.org/6300.html)
<!--more-->
