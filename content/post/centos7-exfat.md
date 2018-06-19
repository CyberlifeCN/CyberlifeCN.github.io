---
title: "在 CentOS7 中支持 exfat 格式的U盘"
date: 2018-06-19T16:35:35+08:00
url: "2018/06/19/centos7-exfat"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/exFAT.png"
---

需要安装epel库，Nux Dextop库，再安装fuse-exfat和exfat-utils包，即可识别exfat格式。

<!--more-->
Nux Dextop是类似CentOS、RHEL、ScientificLinux的第三方RPM仓库（比如：Ardour，Shutter等等）。目前，Nux Dextop对CentOS/RHEL 6|7可用。

## 安装

Nux Dextop库依赖于EPEL库，所有要先安装EPEL库(需要管理员权限)。
如果安装过则跳过。
```
$ su root
$ yum -y install epel-release
```

对于RHEL6/CentOS 6(复制/粘贴):
```
$ rpm -Uvh http://li.nux.ro/download/nux/dextop/el6/x86_64/nux-dextop-release-0-2.el6.nux.noarch.rpm
```
对于RHEL/CentOS 7(复制/粘贴):
```
$ rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
```

## 官方安装说明

检查Nux Dextop是否安装成功：
```
$ yum repolist
```
如果仓库列表中有Nux Dextop就安装成功。

## 提示

由于Nux Dextop仓库可能会与其他第三方库有冲突，比如（Repoforge和ATrpms）。
所以，建议默认情况下不启用Nux Dextop仓库。

打开/etc/yum.repos.d/nux-dextop.repo，将"enabled=1" 修改为 "enabled=0"。
```
$ sudo vi /etc/yum.repos.d/nux-dextop.repo
```
当需要使用Nux Dextop仓库时，显式启用仓库。
```
$ sudo yum --enablerepo=nux-dextop install <package-name>
```

## 安装exfat支持库文件
```
yum --enablerepo=nux-dextop  install fuse-exfat exfat-utils
```
<!--more-->
