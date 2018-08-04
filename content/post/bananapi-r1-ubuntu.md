---
title: "香蕉派 BPI-R1 开源智能路由器 安装ubuntu"
date: 2018-08-04T20:54:35+08:00
url: "2018/08/04/bananapi-r1-ubuntu"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/BPI-R1.jpg"
---

香蕉派 BPI-R1是一款高性价比开源无线路由器，采用全志 A20 1.2G 双核 Cortex™-A7处理器； 无需加载任何设备，只需SD卡即可开机、运行系统并存储的高性价比智能无线路由器， 采用BCM53125 Switch芯片，满足有线网络传输10/100/1000Mbps的要求；无线支持 IEEE 802.11 b/g/n 2.4GHz，配置双外置天线加强无线传输信号，无线传输速率高达300Mbps；

<!--more-->

## 香蕉派 BPI-R1，不仅仅是路由器

* 一键实现开机与重启，操作简便快捷；
* 支持720p@30fps和1080p@30fps的视频输出，搭Mali-400MP2 GPU轻松实现1920*1200高分辨率输出，最高支持
100dB SNR音频输出构建家庭影音中心毫无压力；
* HDMI输出适用市面上主流高清输出设备；
* 自由选配SATA1.5/3.0接口笔记本硬盘扩充存储容量，为家庭云中心
* 远程下载打造自由存储空间；
* BPI-R1让计算机与路由器完美融合，专业的技术支持让您玩转极致全能功能

## ubuntu-mate-16.04-desktop 下载地址
* [2016-07-21-ubuntu-mate-16.04-desktop-armhf-raspberry-pi-bpi-m1-m1p-r1-sd-emmc.img.zip](https://drive.google.com/file/d/0B_YnvHgh2rwjY0VlUGdobzFtdk0/view?usp=sharing)
* 用户名: pi
* 密码: bananapi

## 香蕉派tf卡扩容
我的本身是16g内存卡，扩容之前是7g，没有充分利用资源。
```
1. cat /sys/block/mmcblk0/mmcblk0p2/start
2. 记下返回的数字 729088
3. sudo fdisk /dev/mmcblk0
d  回车
2  回车
n  回车
p  回车
2  回车
将刚才数字打上 729088 回车
再按一次回车
w 回车
此时应该显示

The partition table has been altered.

      Calling ioctl() to re-read partition table.
      Re-reading the partition table failed.: Device or resource busy
The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).

4. sudo reboot 重新启动
5. 重启之后运行  sudo resize2fs /dev/mmcblk0p2

至此扩容完毕  可以运行df -h 查看一下。
Filesystem      Size  Used Avail Use% Mounted on
udev            435M     0  435M   0% /dev
tmpfs            88M  3.2M   85M   4% /run
/dev/mmcblk0p2   15G  4.5G  9.3G  33% /
tmpfs           437M  232K  437M   1% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           437M     0  437M   0% /sys/fs/cgroup
/dev/mmcblk0p1  256M  218M   39M  85% /boot
tmpfs            88M   24K   88M   1% /run/user/1000
```

## 安装 VNC

1. 安装 VNC 服务器
```
sudo apt-get install tightvncserver
```
2. 启动 VNC 服务器并设置密码
```
vncserver
```
输入6位密码，已经要记得哟。默认端口是 5901.
3. 配置 .vnc/xstartup 脚本
```
sudo vi /home/pi/.vnc/xstartup
```

停止VNC服务
```
vncserver -kill :1
```

重新启动VNC服务
```
vncserver :1
```

## 参考资料
* [树莓派、香蕉派tf卡扩容](https://blog.csdn.net/TIANJIAZHAO/article/details/79417255)
* [Banana Pi BPI-R1](http://wiki.banana-pi.org/Banana_Pi_BPI-R1)
* [BananaPro/Pi:How to login to the system](http://wiki.lemaker.org/BananaPro/Pi:How_to_login_to_the_system)
<!--more-->
