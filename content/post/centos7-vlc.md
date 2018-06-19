---
title: "Centos7 下安装 VLC 视频播放器"
date: 2018-06-19T15:14:55+08:00
url: "2018/06/19/centos7-vlc"
categories: ["deploy"]
tags: ["linux","music"]
banner: "imgs/vlc.jpg"
---

打算在centos7.2上安装一个叫MPlayer的视频播放器，但是折腾好久，得到的结果只是可以播放，但是却没有声音。无奈之下另寻他路。最后选择安装VLC视频播放器。

<!--more-->

## 下载epel源。

在命令行下输入：
```
wget https://mirrors.tuna.tsinghua.edu.cn/epel//7/x86_64/e/epel-release-7-9.noarch.rpm
```
(此地址会每隔一段时间就会发生变化，当你发现有问题时，你先输入前半段网址 https://mirrors.tuna.tsinghua.edu.cn/epel/)

下载nux-dextop

命令行下输入：
```
wget http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
```
(此地址也可能会变化，若有变，操作方法，如上。)

## 安装yum源
```
yum -y localinstallepel-release-7-9.noarch.rpm
yum -y localinstallnux-dextop-release-0-5.el7.nux.noarch.rpm
```
## 安装VLC
```
yum -y install vlc
```
注意root安装VLC，但是root用户却无法播放。必须使用别的用户才能进行播放操作。

至此，安装完成。但是，我在播放时还是遇到问题-------没有声音。解决办法：更改右下角的声卡选项，改为指定主机声卡。现在播放就有了声音。

<!--more-->
