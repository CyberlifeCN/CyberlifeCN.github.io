---
title: "利用 FRP 搭建内网穿透实现反向代理"
date: 2018-06-22T14:17:55+08:00
url: "2018/06/22/frp"
categories: ["deploy"]
tags: ["linux","翻墙"]
banner: "imgs/ngrok-frp.jpg"
---

买了一台阿里云的VPS，有了公网IP就想着内网穿透的事情。一台内网服务器A，一台外网服务器B，我希望通过外网连接SSH来连接到A，也就是在家里连公司内网的办公虚拟机

<!--more-->
内网穿透说起来也算不上什么高深的技术，随着近些年微信公众号、支付宝生活号、小程序等新形态应用的持续火热，处于内网开发环境外网无法访问一直困扰着不少开发者。如何将内网服务暴露给外网访问？早些年的国产代表作有花生壳（收费），Github上开源产品有Ngrok、Frp等，我主要在Linux上使用Frp做反向代理。

# frp 全名 Fast Reverse Proxy

## 公网主机配置
```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_386.tar.gz
tar -xvf frp_0.20.0_linux_386.tar.gz
cd frp_0.20.0_linux_386
```
修改 frps.ini 文件
```
[common]
bind_port = 7000
vhost_http_port = 80
vhost_https_port = 443
```
启动
```
./frps -c ./frps.ini
```
注意：别忘记将公网主机的防火墙端口 6000 和 7000 打开

## 公网主机配置自启动

新建启动脚本文件 /etc/systemd/system/frps.service，内容如下：
```
[Unit]
Description=frps
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/frp_0.20.0_linux_386/frps -c /home/thomas/frp_0.20.0_linux_386/frps.ini
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 frps 服务
```
systemctl enable frps.service
systemctl start frps.service
systemctl status frps.service
systemctl daemon-reload
```

## 内网主机配置
```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_386.tar.gz
tar -xvf frp_0.20.0_linux_386.tar.gz
cd frp_0.20.0_linux_386
```
修改 frpc.ini 文件，假设 frps 所在服务器的公网 IP 为 x.x.x.x，域名为yourdomain.com；
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

[web]
type = http
local_ip = 127.0.0.1
local_port = 80
custom_domains = o2.yourdomain.com

[webs]
type = https
local_ip = 127.0.0.1
local_port = 443
custom_domains = o2.cloudancing.cn
```
启动
```
./frpc -c ./frpc.ini
```

## 内网主机配置自启动

新建启动脚本文件 /etc/systemd/system/frpc.service，内容如下：
```
[Unit]
Description=frpc
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/frp_0.20.0_linux_386/frpc -c /home/thomas/frp_0.20.0_linux_386/frpc.ini
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 frp 服务
```
systemctl enable frpc.service
systemctl start frpc.service
systemctl status frpc.service
systemctl daemon-reload
```

## 提供 VNC 服务的内网主机配置
```
# frpc.ini
[vnc-server]
type = stcp
sk=RCPBxbuUWAdtMUXXZ3HvaHHX
local_ip = 127.0.0.1
local_port = 5901
```

## 使用 VNC 客户端内网主机配置
```
# frpc.ini
[vnc-client]
type = stcp
sk=RCPBxbuUWAdtMUXXZ3HvaHHX
role = visitor
server_name = vnc-server
bind_addr = 127.0.0.1
bind_port = 6007
```

使用 NVC 客户端时配置链接 127.0.0.1:6007

## 访问外网主机使用
通过 ssh 访问内网机器，假设用户名为 test
```
ssh -oPort=6000 test@x.x.x.x
```
通过 scp 发送文件到内网机器，假设用户名为 test
```
scp -P 6000 local_file test@x.x.x.x:.
```

## 常见问题
```
[E] [control.go:236] authorization timeout
```
提示授权失败，原因是两台机器之间的时钟相差太多（大于15分钟），解决办法
```
sudo ntpdate cn.pool.ntp.org
```

* [frp](https://github.com/fatedier/frp)
* [ngrok](https://github.com/inconshreveable/ngrok)
<!--more-->
