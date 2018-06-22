---
title: "利用FRP搭建内网穿透实现反向代理"
date: 2018-06-22T14:17:55+08:00
url: "2018/06/22/frp"
categories: ["deploy"]
tags: ["linux","翻墙"]
banner: "imgs/ngrok-frp.jpg"
---

买了一台阿里云的VPS，有了公网IP就想着内网穿透的事情。一台内网服务器A，一台外网服务器B，我希望通过外网连接SSH来连接到A，也就是在家里连公司内网的办公虚拟机

<!--more-->
内网穿透说起来也算不上什么高深的技术，随着近些年微信公众号、支付宝生活号、小程序等新形态应用的持续火热，处于内网开发环境外网无法访问一直困扰着不少开发者。如何将内网服务暴露给外网访问？早些年的国产代表作有花生壳（收费），Github上开源产品有Ngrok、Frp等，我主要在Linux上使用Frp做反向代理。

## 公网主机配置
```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_386.tar.gz
tar -xvf frp_0.20.0_linux_386.tar.gz
cd frp_0.20.0_linux_386
./frps -c ./frps.ini
```
注意：别忘记将公网主机的防火墙端口 6000 和 7000 打开

## 公网主机配置自启动

新建启动脚本文件/etc/systemd/system/frp.service，内容如下：
```
[Unit]
Description=frp
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/frp_0.20.0_linux_386/frps -c /home/thomas/frp_0.20.0_linux_386/frps.ini
[Install]
WantedBy=multi-user.target
```
启动 frp 服务
```
systemctl enable frp.service
systemctl start frp.service
systemctl status frp.service
```

## 内网主机配置
```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_386.tar.gz
tar -xvf frp_0.20.0_linux_386.tar.gz
cd frp_0.20.0_linux_386
```
修改 frpc.ini 文件，假设 frps 所在服务器的公网 IP 为 x.x.x.x；
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```
启动
```
./frpc -c ./frpc.ini
```

## 内网主机配置自启动

新建启动脚本文件/etc/systemd/system/frp.service，内容如下：
```
[Unit]
Description=frp
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/frp_0.20.0_linux_386/frpc -c /home/thomas/frp_0.20.0_linux_386/frpc.ini
[Install]
WantedBy=multi-user.target
```
启动 frp 服务
```
systemctl enable frp.service
systemctl start frp.service
systemctl status frp.service
```

## 外网主机使用
通过 ssh 访问内网机器，假设用户名为 test
```
ssh -oPort=6000 test@x.x.x.x
```


* [frp](https://github.com/fatedier/frp)
* [ngrok](https://github.com/inconshreveable/ngrok)
<!--more-->
