---
title: "centos7中yum安装ntfs-3g"
date: 2018-07-10T12:24:35+08:00
url: "2018/06/19/centos7-ntfs"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/ntfs.png"
---

CentOS默认源里没有ntfs3g，想要添加ntfs支持，无非是自己下载编译安装或者加源yum安装。

<!--more-->
新安装了一个CentOS7，用的是添加aliyun的epel源来yum安装的方式，简单易行。

### 加源
```
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```

### 安装
```
yum update;yum install ntfs-3g
yum install ntfs-3g
```
<!--more-->
