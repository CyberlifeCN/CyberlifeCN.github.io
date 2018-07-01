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

注释掉
#Exec=sh -c 'xset m 20/10 10 r rate 500 30 b on; xinput --set-prop "pointer:USB Optical Mouse" "libinput Accel Speed" 0.000000'

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

## 编写 GPIO 程序控制屏幕显示
vi backlight.py
```
#!/usr/bin/env python
# coding=utf-8
# author:ksc

import RPi.GPIO as GPIO
import time
import os,sys
import signal

GPIO.setmode(GPIO.BCM)

#define GPIO pin
btn_x=5
btn_s=22
btn_o=23
pin_tft=27

# Up, Down, left, right, fire
GPIO.setup(24, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #Trigon Button for GPIO24
GPIO.setup(btn_x, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #X Button for GPIO5
GPIO.setup(btn_o, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #Circle Button for GPIO23
GPIO.setup(btn_s, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #Square Button for GPIO22
GPIO.setup(4, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #R Button for GPIO4
GPIO.setup(5, GPIO.IN, pull_up_down=GPIO.PUD_UP)  #L Button for GPIO7
GPIO.setup(pin_tft, GPIO.OUT, initial=GPIO.HIGH)

def cleanup():
    '''释放资源，不然下次运行是可能会收到警告
    '''
    print('clean up')
    GPIO.cleanup()


def handleSIGTERM(signum, frame):
    #cleanup()
    sys.exit()#raise an exception of type SystemExit


def onLightOff(channel):
    print('pressed light off')
    GPIO.output(pin_tft, GPIO.LOW) # off


def onLightOn(channel):
    print('pressed light on')
    GPIO.output(pin_tft, GPIO.HIGH) # on


GPIO.add_event_detect(btn_x, GPIO.FALLING, callback=onLightOff, bouncetime=500)
GPIO.add_event_detect(btn_s, GPIO.FALLING, callback=onLightOn, bouncetime=500)


#signal.signal(signal.SIGTERM, handleSIGTERM)
try:
    while True:
        GPIO.output(pin_tft, GPIO.LOW)# blink led
        time.sleep(1)
except KeyboardInterrupt:
    print('User press Ctrl+c ,exit;')
finally:
    cleanup()
```

## 配置自启动
新建启动脚本文件/etc/systemd/system/backlight.service，内容如下：
```
[Unit]
Description=backlight
[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/python /home/pi/bin/backlight.py
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 backlight 服务
```
systemctl enable backlight.service
systemctl start backlight.service
systemctl status backlight.service
systemctl daemon-reload
```

### 资料
* [Adafruit-PiTFT-Helper] (https://github.com/adafruit/Adafruit-PiTFT-Helper)
* [Adafruit-PiTFT-2.2-Inch-HAT-PCB] (https://github.com/adafruit/Adafruit-PiTFT-2.2-Inch-HAT-PCB)
* [UGeek Screen Setup Tools] (https://sourceforge.net/projects/u-geek/files/2.2TFT/)
* [Adafruit 2.2" PiTFT HAT - 320x240 Display] (https://learn.adafruit.com/adafruit-2-2-pitft-hat-320-240-primary-display-for-raspberry-pi/overview)
* [rpi-fbcp] (https://github.com/tasanakorn/rpi-fbcp)

<!--more-->
