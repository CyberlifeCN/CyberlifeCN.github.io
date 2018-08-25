---
title: "使用1-bay NAS Dock DIY自已的NAS服务器"
date: 2018-08-25T21:21:55+08:00
url: "2018/08/25/NAS-Dock-for-NanoPi-NEO"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/1-bay-NAS-Dock.jpeg"
---

1-bay NAS Dock是一个用于搭建迷你、小巧的桌上型NAS（Network Attached Storage：网络附属存储）设备的扩展底板，它采用了高速稳定的专业级USB 3.0 to SATA转换芯片(JSM568), 可直接安装使用2.5寸小硬盘，并采用TI公司DC-DC芯片实现稳定可靠的12V-5V电源转换，支持板载RTC时钟备份电池；我们还基于最新主线内核Linux-4.11和Debian-Jessie 为其移植了开源NAS软件系统OpenMediaVault，另外配上我们专门为其定制的精致喷砂金属铝外壳，就能够快速的搭建属于你的专用数据存储服务器。

<!--more-->

## 硬件配置
1. NAS Dock v1.2 支持 NanoPi NEO/NEO2;
2. Screw set;
3. 2.5寸 SATA接口的硬盘;
4. NanoPi NEO2(1GB内存) 带散热片
{{< gallery "/imgs/Step0-v1.2.jpeg" >}}

## 铝合金外壳
{{< gallery "/imgs/Step1-v1.2.jpg" >}}

## 下载 OpenMediaVault
* [nanopi-neo2_debian-nas-jessie_4.x.y_YYYYMMDD.img.zip](https://www.mediafire.com/folder/ah4i6w029912b/NanoPi-NEO2)

## 烧录镜像 TF Card
* 解压缩 nanopi-neoN_debian-nas-jessie_4.x.y_YYYYMMDD.img.zip
* 使用 Etcher 烧录镜像到 SD 卡

## 配置 NAS
* 使用 SuperScan 扫描您的网络中新增节点，找到的新增 IP地址就是您的 NAS的访问IP
* http://192.168.1.x
    默认用户名: admin
    默认密码: openmediavault
* 登录 OpenMediaVault 后的首页，让我们开始 NAS之旅吧:
{{< gallery "/imgs/Omv_version411.jpg" >}}

## 系统维护
* ssh root@192.168.1.x
    默认用户名: root
    默认密码: fa

## 参考
* [NanoPi NEO2](http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO2/zh)
* [友善出品NAS网络存储服务器](https://item.taobao.com/item.htm?id=548007605979)
<!--more-->
