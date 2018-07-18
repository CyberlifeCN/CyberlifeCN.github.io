---
title: "Ubuntu 16 下安装 NextCloud 及配置"
date: 2018-07-14T21:00:35+08:00
url: "2018/07/14/ubuntu16-NextCloud"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/nextcloud4.jpg"
---

我在上周购买的 NanoPi Neo2 金甲套装上安装 NextCloud操作如下

<!--more-->
## 1.更新软件列表
```
apt update
```

## 2.安装Apache
```
apt install -y apache2
```

## 3.安装MariaDB
```
apt install -y mariadb-server
```

## 4.安装PHP7
```
apt install -y libapache2-mod-php7.0
```

## 5.安装PHP各种扩展
```
apt install -y php7.0-gd php7.0-json php7.0-mysql php7.0-curl php7.0-mbstring php7.0-intl php7.0-mcrypt php-imagick php7.0-xml php7.0-zip
```

## 6.启用Apache模块
```
a2enmod rewrite
a2enmod headers
a2enmod dir
a2enmod env
a2enmod mime
service apache2 restart
```

## 7.安装NextCloud
```
cd /tmp/
wget https://download.nextcloud.com/server/releases/latest-13.tar.bz2
tar -jxvf latest-*.bz2
mv nextcloud /var/www/html/
mkdir /var/www/html/nextcloud/data
chown -R www-data:www-data /var/www/html/nextcloud
```

## 8.创建数据库
```
sudo mysql -u root -p
create database nextclouddb;
grant all on nextclouddb.* to 'nextclouduser'@'localhost' identified by 'nextclouduser';
quit
```

## 9.安装NextCloud
浏览器访问http://服务器IP，进行安装
{{< gallery "/imgs/nextcloud2.png" >}}

## [优化]10.启用PHP OPcache

在php.ini中进行如下修改
```
opcache.enable=1
opcache.enable_cli=1
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.memory_consumption=128
opcache.save_comments=1
opcache.revalidate_freq=1
```

## [优化]11.使用Cron执行后台任务
```
crontab -u www-data -e
*/15  *  *  *  * php -f /var/www/html/nextcloud/cron.php
```
<!--more-->
