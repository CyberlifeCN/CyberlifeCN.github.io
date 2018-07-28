---
title: "在 CentOS7 中安装 VNC Server"
date: 2018-07-28T14:47:35+08:00
url: "2018/06/19/centos7-vncserver"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/Install-VNC-in-CentOS-7.png"
---

<!--more-->

## 安装 TigerVNC Server
```
sudo yum install -y tigervnc-server
```

## 配置 VNC Service
```
sudo cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:.service
```

将其中<USER>替换为你想要的用户名
```
# The vncserver service unit file
#
# Quick HowTo:
# 1. Copy this file to /etc/systemd/system/vncserver@:<display>.service
# 2. Edit <USER> and vncserver parameters appropriately
#   ("runuser -l <USER> -c /usr/bin/vncserver %i -arg1 -arg2")
# 3. Run `systemctl daemon-reload`
# 4. Run `systemctl enable vncserver@:<display>.service`
#

. . .

[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i -geometry 1280x720"
PIDFile=/home/<USER>/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

[Install]
WantedBy=multi-user.target
```

## 设置VNC密码
```
vncserver
```

提示你输入密码，如下
```
You will require a password to access your desktops.
Password:
Verify:
xauth:  file /home/oracle/.Xauthority does not exist

New 'localhost.localdomain:1 (oracle)' desktop is localhost.localdomain:1

Creating default startup script /home/oracle/.vnc/xstartup
Starting applications specified in /home/oracle/.vnc/xstartup
Log file is /home/oracle/.vnc/localhost.localdomain:1.log
```

果想修改密码，可以使用vncpasswd。现在已经有一个vnc服务在运行了，但我们需要使用刚刚配置的服务来启动，所以我们需要先杀死刚刚的vnc服务，使用下面命令。
```
vncserver -kill :1
```

## 启动服务
```
sudo systemctl daemon-reload
sudo systemctl enable vncserver@:1.service
sudo systemctl restart vncserver@:1.service
sudo systemctl status vncserver@:1.service
```

## 修改防火墙
```
sudo firewall-cmd --permanent --zone=public --add-port=5901-5905/tcp
sudo firewall-cmd --reload
firewall-cmd --list-all-zones
```

<!--more-->
