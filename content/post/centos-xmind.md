---
title: "centos-xmind"
date: 2018-08-30T00:29:55+08:00
url: "2018/08/30/centos-xmind"
categories: ["deploy"]
tags: ["linux","oa"]
banner: "imgs/xmind8.jpg"
---

XMind作为使用最广泛的思维导图软件，拥有强大的功能、优秀的用户体验和操作简单的特点，正在为200万用户提供更高的生产力及创造力。

<!--more-->

## 下载地址
* [XMind 8](https://www.xmind.cn/download/xmind8/)

## 安装
```
unzip xmind-8-update8-linux.zip
cd xmind-8-update8-linux
cd XMind_amd64
chmod 755 XMind
./XMind
```

## 制作软链接，方便启动
```
vi ~/bin/xmind.sh
cd ~/sfw/xmind-8-update8-linux/XMind_amd64
./XMind
```

## 制作桌面启动
```
vi /usr/share/applications/xmind.desktop
Icon=/usr/share/pixmaps/XMind.png
Exec=/opt/XMind
```

<!--more-->
