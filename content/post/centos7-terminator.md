---
title: "在 CentOS7 安装 Terminator"
date: 2018-07-17T23:49:35+08:00
url: "2018/07/17/centos7-terminator"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/terminator.png"
---

使用Terminator可以在一个窗口中显示多个GNOME终端窗口，并且可以按照用户的要求对窗口进行任意分割。可以比肩 Mac 下的 iTerm2。

<!--more-->
对Linux系统进行管理时，我们经常需要通过终端窗口输入各种操作命令。在GNOME集成桌面环境下，GNOME终端(gnome-terminal)是我们经常使用的终端程序，每次运行该程序都将打开一个单独的终端窗口。当我们进行命令行操作时，有时需要打开多个终端窗口，并且希望这些窗口能同时平铺显示，那该怎么办呢?其实，借助一款小巧而实用的软件——Terminator就可以轻松实现这一功能。

# 安装命令
```
sudo yum install epel-release
sudo yum install terminator
```

安装好Terminator软件后，我们可以在系统桌面的菜单“应用程序”→“附件”下找到“Terminator”项，单击该项可运行Terminator。另外，我们也可以直接在X-Window终端窗口中输入命令“terminator”来运行它。

Terminator运行后，在当前桌面显示一个终端窗口，乍一看，与GNOME的终端窗口很相似，只不过标题栏显示为“Terminator”，且没有菜单栏，

<!--more-->
