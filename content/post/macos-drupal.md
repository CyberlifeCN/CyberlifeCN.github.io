---
title: "Mac OS 安装开源 CMS - Drupal"
date: 2018-08-03T09:44:55+08:00
url: "2018/08/03/macos-drupal"
categories: ["deploy"]
tags: ["linux","php"]
banner: "imgs/drupal.jpeg"
---

公司网站要升级为动态CMS，业务发展需要内容即使更新，而且要有与用户互动的需要。
查询了现有的开源CMS，主要一wordpress和drupal。
过去购买过一套drupal的主题，这次利用一下。

<!--more-->
## MacOS 升级自带PHP5.6 升级到 PHP7.1
输入下面命令，安装php7.1安装包，安装过程需要输入系统的密码
```
curl -s http://php-osx.liip.ch/install.sh | bash -s 7.1
```
过程有点慢，大概20分钟

## Mac High Sierra 如何开启apache2+php7
新版Mac系统10.13配置apache教程。Mac现在php升级到了7.1.7，apache为2.4.27。因此在新系统上搭建本地httpserver需要注意一些额外的东西。
确认本地apache是否开启，打开Safari，输入localhost，如显示未连接则未开启。
在在终端输入：
```
sudo apachectl start
```
在浏览器输入“http://localhost”，会显示“It works!”，说明服务器已经启动成功了。Apache默认的根目录在“/Library/WebServer/Documents/”下。

## apache2+php7配置
修改文件
```
cd /etc/apache2
sudo cp httpd.conf httpd.conf.bak
sudo vi httpd.conf
```
找到以下部分
```
ServerName your.example.com
Options FollowSymLinks
AllowOverride None
#LoadModule php7_module libexec/apache2/libphp7.so
# Apache默认的根目录在
DocumentRoot "/Library/WebServer/Documents/"
<Directory "/Library/WebServer/Documents/">
  Options Indexes FollowSymLinks Multiviews
  MultiviewsMatch Any
  AllowOverride All
  Require all granted
</Directory>
```
修改为
```
ServerName localhost
Options Indexes FollowSymLinks
AllowOverride All
LoadModule php7_module libexec/apache2/libphp7.so
# Apache默认的根目录在
DocumentRoot "/Users/thomas/php"
<Directory "/Users/thomas/php">
  Options Indexes FollowSymLinks Multiviews
  MultiviewsMatch Any
  AllowOverride All
  Require all granted
</Directory>
```
修改文件
```
cd /etc
sudo cp php.ini.default php.ini
sudo vi php.ini
```
重启服务器
```
sudo apachectl -k restart
```

进入php文件夹，生成index.php文件，在index中添加命令：
```
vi /Users/thomas/php/index.php
<?php phpinfo(); ?>
```
打开localhost，查看结果，如图则表示配置apache+php成功。

## 下载Drupal
* [官网下载地址](https://www.drupal.org/download)

## 安装
```
cd /Users/thomas/php
tar -xvf drupal-8.5.6.tar
mv drupal-8.5.6 drupal
cd drupal/sites/default
cp default.settings.php settings.php
cd /Users/thomas/php
chmod -R 777 drupal
```
修改drupal/.htaccess
```
<IfModule mod_rewrite.c>
  RewriteEngine on
  RewriteBase /drupal
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule ^(.*)$ index.php?q=$1 [L,QSA]
```

## 访问
http://localhost/drupal

<!--more-->
