---
title: "局域网内通讯工具 Dukto"
date: 2018-06-21T09:59:55+08:00
url: "2018/06/21/dukto"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/dukto.png"
---

为什么需要局域网通讯工具呢？
很简单，比如小微企业内部需要交流、传递文件等，使用QQ等互联网通讯软件，既不安全，速度也慢，所以需要局域网通讯工具。除了具备文字聊天外，还应可以发送文件标注部门等。当然，中大型企业一般使用腾讯通rtx 之类的内部通讯工具。但是那个既要付费，也需要部署服务器，成本较高。对于小微企业，飞鸽传书之类的局域网通讯工具足以满足需求。

<!--more-->
Windows 下有飞鸽传书，Linux 下则有 IPtux 和 Dukto。IPtux 是 Linux 下目前最好的局域网通讯工具，没有之一。
iptux 是一个国人开发的开源 Linux 版飞鸽传书，基于 GTK+2，兼容“飞鸽传书”协议的 LAN 通信、文档传递软件。
但是，我们没有找到Centos 版本的IPtux，于是只好下载了Dukto测试，发现也非常好用。

Dukto 是一款实用的开源跨平台LAN文件传输工具，Dukto支持 Mac、Win、Linux、iOS、Android、Symbian、Blackberry 各大平台，Dukto 运行稳定，传输文件速度快；

Dukto 具有一个时髦的 METRO UI，而且操作方便，无需配置，登陆后会在主界面发现同一网络内其他 Device，支持文本，剪切板，文件/文件夹的共享。
{{< gallery "/imgs/dukto1.png" >}}

如今 Dukto 已开发到了第六代，他们也推出了重写的 iOS 版本客户端供大家使用。双击 Buddies 列表下的设备名称即可进入文件传输节目（下图最右侧截图），你可以在这里发送文本、剪切板文本，任意格式的文件，甚至是文件夹。
{{< gallery "/imgs/dukto2.png" >}}

* [Mac版下载](http://www.pc6.com/mac/248218.html)
* [Linux版下载](https://software.opensuse.org/download.html?project=home:colomboem&package=dukto)
* [Windows版下载](https://sourceforge.net/projects/dukto/)
<!--more-->
