---
title: "centos7 安装 php7"
date: 2018-12-04T17:34:55+08:00
url: "2018/12/04/centos7-php7"
categories: ["deploy"]
tags: ["linux","php"]
banner: "imgs/nginx_php7.0_mariadb.jpg"
---

php7 的安装方法多种多样，总结一下

<!--more-->

## linux 卸载php php-fpm
yum uninstall php php-fpm

## 安装编译工具和扩展库
yum -y install gcc make gd-devel libjpeg-devel libpng-devel libxml2-devel bzip2-devel libcurl-devel
yum install openssl openssl-devel

## 下载php7并解压
wget http://cn2.php.net/distributions/php-7.1.0.tar.gz
tar zxvf php-7.1.0.tar.gz
cd php-7.1.0

## 编译安装php7
./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-gd --enable-pdo --with-openssl --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-zlib --enable-mbstring --with-zlib-dir=/usr/local/zlib --with-curl=/usr/local/curl --enable-mysqlnd-compression-support --enable-mbstring --enable-zip --enable-fpm  
make && sudo make install

## 配置php7
cp php.ini-production /usr/local/php/etc/php.ini
cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
cp /usr/local/php/etc/php-fpm.d/www.conf.default /usr/local/php/etc/php-fpm.d/www.conf
cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
ln -s /etc/init.d/php-fpm /usr/bin/

## 配置nginx
vi /etc/nginx/locatiion.d/php.conf
location ~ \.php$ {
		root           /usr/share/nginx/html;
		fastcgi_pass   127.0.0.1:9099;
		fastcgi_index  index.php;
		fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
		include        fastcgi_params;
	}
## 启动nginx和php服务器
nginx - s start
php-fpm start

## 创建info.php测试文件
vi /usr/share/nginx/html/info.php
<?php
	phpinfo();
?>

## 浏览器访问
curl http://127.0.0.1:8080/info.php

<!--more-->