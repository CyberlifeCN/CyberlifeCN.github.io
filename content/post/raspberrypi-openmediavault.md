---
title: "OpenMediaVault 4 for Raspberry Pi 2B, 3B and 3B+"
date: 2018-06-30T22:10:55+08:00
url: "2018/06/30/OpenMediaVault"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/openmediavault.png"
---

OpenMediaVault，是一个开源的基于Debian Linux的下一代网络附加存储(NAS)解决方案。

它包含众多服务,如SSH,(S)FTP,SMB / CIFS,DAAP媒体服务器,RSync,BitTorrent客户机等。 并具有通过插件可增强的模块化设计框架特性。

主要是设计用于在家庭环境或小的家庭办公室,但不仅限于这些场景。 这是一个简单和易于使用的开箱即用的解决方案,允许任何没有更深的知识的人来安装和管理的网络附加存储(NAS)。

Olivier Cochard-Labbe在2005年创建了FreeNAS项目，后Volker Theile加入该项目作为核心开发人员 [2]  ；2009年12月Olivier Cochard-Labbe发表声明停止为FreeNAS 0.7版本开发新功能后，Volker Theile创建了基于Debian Linux的OpenMediaVault项目，并采用GPLv3授权；iXsystems收购FreeNAS后重写了网页框架和构架在2011年发布了全新的FreeNAS 8版本 [2]  ；2011年其余开发者基于FreeNAS 0.7开发了分支并重新命名为NAS4Free。

<!--more-->

建议直接使用 [OMV树莓派官方版](https://sourceforge.net/projects/openmediavault/files/Raspberry%20Pi%20images/) 镜像，[etcher](https://etcher.io)写进SD卡。


### web interface:
  - username = admin
  - password = openmediavault

### console/ssh:
  - username = root
  - password = openmediavault

## 打开阻止root远程登录
找个显示器、键盘登录树莓派，操作
```
  # vi /etc/ssh/sshd_config
  PermitRootLogin yes
  # service sshd restart
```

## 启动nginx时报错
```
nginx: [emerg] socket() [::]:80 failed (97: Address family not supported by protocol)
```
### 解决办法：
```
vi /etc/nginx/sites-enabled/openmediavault-webgui

将
listen [::]:80 ipv6only=off;

注释掉：
#listen [::]:80 ipv6only=off;
```

## 更新Debian软件源
```
cp /etc/apt/sources.list /etc/apt/sources.list_bak #备份一下软件源
vi /etc/apt/sources.list

# 163
deb http://mirrors.163.com/debian/ stretch main
deb http://mirrors.163.com/debian/ stretch-updates main non-free contrib
deb-src http://mirrors.163.com/debian/ stretch-updates main non-free contrib
deb http://mirrors.163.com/debian-security/ stretch/updates main non-free contrib
deb http://httpredir.debian.org/debian stretch-backports main contrib non-free
```

## 挂载exFAT类型格式化后的USB设备
```
# debian apt-get update：public key 错误修复
apt-get install debian-keyring debian-archive-keyring
apt-get update
apt-get install exfat-utils
apt-get install exfat-fuse
```

## OpenMediaVault 包含以下重要特性：
### 核心
* Debian Linux(Squeeze)系统(i386 or x64)
* 基于Web方式的系统管理
* 通过Debian软件包简单的系统升级
* 用户管理
* 计划任务
* 多语言支持（0.4.18版支持繁体中文）
* 基于DNS-SD(Apple's protocol)的服务公告
* 插件系统

### 网络系统
* 链路聚合
* 网络唤醒
* 支持IPv6

### 卷管理
* 硬盘电源管理(APM/AAM)
* GPT分区
* EXT3/EXT4/XFS/JFS文件系统支持
* 软RAID JBOD/0/1/5/6/...
* 配额 (每个卷)
* 访问控制列表(ACL)
* 共享管理

### 监测
* 系统日志
* 检测程序(Watchdog)
* S.M.A.R.T.
* SNMP (v1/2c/3) (只读)
* 电子邮件通知
* 主动进程和系统状态监测

### 服务
* SSH
* FTP
* TFTP
* NFS (v3/v4)
* SMB/CIFS
* RSync

## 插件
### 官方插件
* openmediavault-owncloud
可以搭建自己的私有云，插件可以设置读取NAS系统中已有的文件目录，这样就可以通过owncloud读取现有文件结构而不用重新上传。
* openmediavault-nut
UPS监控软件，可以管理UPS，如设置UPS断电自动关机等。
* openmediavault-route
可以设置静态路由。
* openmediavault-usbbackup
可以在USB外置存储和NAS系统之间双向备份数据。
* openmediavault-clamav
开源的防病毒软件，可以一定程度保护系统安全。
* openmediavault-netatalk
可以设置支持Apple Talk 协议的的共享。
* openmediavault-lvm
逻辑卷管理软件，可以管理逻辑卷系统。
* openmediavault-forkeddaapd
可以设置支持iTunes的DAAP协议服务器。
* openmediavault-iscsitarget
可以设置ISCSI Target客户端或者服务器。
* openmediavault-ldap
轻量级的目录服务器。

### 第三方插件
* openmediavault-omvextrasorg
第三方插件源地址，要想顺利安装下面的插件，改插件要第一个安装。在WebGUI中提供了插件源的不同版本及服务器选项，可以按需开启相关源。
* openmediavault-autoshutdown
可以根据用途设置系统自动关机以，比如定时关机，局域网客户端侦测关机等。
* openmediavault-btsync
基于BitTorrent Sync 的客户端，可以做P2P同步或分享数据。
* openmediavault-downloader
下载客户端，可用协议有aria2、curl、youtube-dl。
* openmediavault-extplorer
基于Web的eXtplorer浏览器，可以在Web端管理系统中的文件。
* openmediavault-links
在WebGUI提供了OpenMediaVault 相关信息的网站地址，也可以自己添加其它地址。
* openmediavault-minidlna
提供轻量级的DLNA/UPnP-AV服务，可以搭建完全符合标准的DLNA服务器。通过MiniDLNA你可以共享NAS系统中的音乐、图片、视频，可以在支持DLNA协议的客户端读取相关共享数据。
* openmediavault-mysql
MySQL数据库插件，用途不言而喻，搭建Web相关程序使用。插件提供简单易用的MySQL Workbench工具来管理数据库，支持简体中文。
* openmediavault-plexmediaserver
跨平台的Plex多媒体服务中心，功能强大界面友好，通过它可以共享视频、图片、音频等多媒体信息，有客户端支持远程访问，可以付费获取更多功能，详情请见官网 [5]  。
* openmediavault-processlist
可以在WebGUI查看系统进程。
* openmediavault-rsnapshot
基于rsnapshot备份软件，可以设置增量备份，更多信息请查阅官网 [6]  。
* openmediavault-snapraid
非常适合家庭数据中心的快照冗余软件，相对与传统RAID有诸多特性，更多详情可以查看官网 [7]  。
* openmediavault-supportinfo
可以在WebGUI查看重要的支持信息，支持信息可用于系统维护。
* openmediavault-transmissionbt
Transmissionbt下载客户端，用途这里就不多介绍了。
* openmediavault-virtualbox
强大的virtualbox虚拟机，如果NAS主机支持Vt-x或、Vt-d虚拟化，可以安装该插件虚拟操作系统，插件提供phpVirtualBox来在Web端管理虚拟机，易用性很强。.
* openmediavault-webdav
可以设置WebDAV协议的来共享系统文件。
* openmediavault-website
使用Apache2，可以提供基于主机名或IP端口两种模式的虚拟主机，架设网站必备。

<!--more-->
