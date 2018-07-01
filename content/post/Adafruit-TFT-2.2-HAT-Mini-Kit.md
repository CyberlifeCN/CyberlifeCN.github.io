---
title: "Adafruit TFT 2.2 HAT Mini Kit"
date: 2018-07-01T14:13:55+08:00
url: "2018/07/01/TFT-2.2-HAT"
categories: ["deploy"]
tags: ["linux","树莓派"]
banner: "imgs/TFT-2.2-HAT.jpeg"
---

在淘宝上买到《树莓派3B+ 铝合金CNC盒子 含2.2 TFT屏 2.0版 金属便携套装》已经有3个月了，
一直没有时间折腾它，今天终于空出点时间，找了些资料。

<!--more-->

## 安装配置 UGeek Screen Setup Tools
```
wget https://sourceforge.net/projects/u-geek/files/2.2TFT/screen_setup.sh
chmod +x ./screen_setup.sh
sudo ./screen_setup.sh
          ┌────────────────────┤ UGEEK WORKSHOP ├────────────────────┐
          │ Select the appropriate options:                          │
          │                                                          │
          │                  1 Output <BOTH>.                        │
          │                  2 Resolution <640*480>.                 │
          │                  3 Rotate <90°>.                         │
          │                  4 Blanking <No>.                        │
          │                  5 Apply new settings.                   │
          │                  6 Exit.                                 │
          │                                                          │
          │                                                          │
          │              <Ok>                  <Cancel>              │
          │                                                          │
          └──────────────────────────────────────────────────────────┘

Option 1: Select ouput device(HDMI | TFT Screen | Both)
Option 2: Select resolution(Auto | 800x600 | 640x480 | 320x240)
Option 3: Select rotate(0 | 90 | 180 | 270)
Option 4: Select screen blanking(Yes | No)
Option 5: Apply new settings.
Option 6: Exit
```

## 树莓派开机-图形界面-自启动全屏Chrome浏览器
```
cd .config/autostart
vi LXinput-setup.desktop
添加2行
Exec=xhost +
Exec=chromium-browser --kiosk "http://127.0.0.1/" --disable-desktop-notifications --no-first-run --kiosk-printing
```

## 制作一个 Web server 显示IP地址
```
mkdir git
cd git
git clone https://github.com/CyberlifeCN/simple-site.git
```

## 安装pip
```
$ wget https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py
```

## 安装 tornado-4.3
```
sudo pip install --upgrade pip
sudo pip install tornado==4.3
```

## 配置自启动
新建启动脚本文件/etc/systemd/system/simplesite.service，内容如下：
```
[Unit]
Description=simplesite
[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/python /home/pi/git/simplesite/simple-site.py
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 simplesite 服务
```
systemctl enable simplesite.service
systemctl start simplesite.service
systemctl status simplesite.service
systemctl daemon-reload
```

### 资料
* [Adafruit-PiTFT-Helper] (https://github.com/adafruit/Adafruit-PiTFT-Helper)
* [Adafruit-PiTFT-2.2-Inch-HAT-PCB] (https://github.com/adafruit/Adafruit-PiTFT-2.2-Inch-HAT-PCB)
* [UGeek Screen Setup Tools] (https://sourceforge.net/projects/u-geek/files/2.2TFT/)
* [Adafruit 2.2" PiTFT HAT - 320x240 Display] (https://learn.adafruit.com/adafruit-2-2-pitft-hat-320-240-primary-display-for-raspberry-pi/overview)
* [rpi-fbcp] (https://github.com/tasanakorn/rpi-fbcp)

<!--more-->
