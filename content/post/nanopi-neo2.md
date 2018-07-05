---
title: "NanoPi Neo2 金甲套装,配OLED显示屏"
date: 2018-07-02T20:03:55+08:00
url: "2018/07/02/nanopi-neo2"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/NanoHat-OLED.jpg"
---

周末下单购买了NanoPi Neo2 金甲套装,配OLED显示屏，周一到公司了，配置成功。

<!--more-->
## 官方镜像下载
http://download.friendlyarm.com/nanopineo2

选择百度网盘，下载 nanopi-neo2_friendlycore-xenial_4.14.0_20180626.img.zip

## 使用IP Scanner 软件查找此设备的IP地址
如：192.168.3.36
```
ssh pi@192.168.3.36
密码：pi

User Name: root
Password: fa
```
## 安装 NanoHat OLED 软件包
* 安装软件包前将软件包的源设置为国内：

```
wget http://wiki.firendlyarm.com/utils/aptsouce.sh
chmod 755 aptsouce.sh
sudo -H ./aptsouce.sh
sudo apt-get update
```

* 执行

```
git clone https://github.com/friendlyarm/NanoHatOLED.git
cd NanoHatOLED
sudo -H ./install.sh
```

* 安装完毕，重启

{{< gallery "/imgs/NanoHat-OLED2.jpg" >}}
{{< gallery "/imgs/NanoHat-OLED1.jpg" >}}
{{< gallery "/imgs/NanoHat-OLED3.jpg" >}}


## 资料
* [NanoPi NEO2](http://wiki.friendlyarm.com/wiki/index.php/NanoPi_NEO2)
<!--more-->
