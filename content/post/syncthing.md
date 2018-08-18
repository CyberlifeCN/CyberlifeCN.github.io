---
title: "使用Syncthing同步你的文件"
date: 2018-08-18T14:41:50+08:00
url: "2018/08/18/syncthing"
description: "linux 运维中常用命令总结"
tags: ["linux", "Syncthing"]
categories: ["deploy"]
banner: "imgs/syncthing.png"
---

在公司写了一天的文档，就差一点儿了，回家继续写吧。这个时候很多人都会使用U盘将文档拷贝一份，或者使用电子邮件发送给自己。这时如果可以直接将文件同步回你家里的电脑，那么一切都会变得十分方便。

<!--more-->

<!-- TOC -->

- [1. 安装](#1-安装)
- [2. 防火墙配置](#2-防火墙配置)
- [3. 为当前用户启动syncthing服务](#3-为当前用户启动syncthing服务)
- [4. 连接WebUI](#4-连接webui)
- [5. 全局设置](#5-全局设置)
    - [5.1. 常规设置](#51-常规设置)
    - [5.2. UI设置](#52-ui设置)
    - [5.3. 连接设置](#53-连接设置)
- [6. 添加远端设备](#6-添加远端设备)
    - [6.1. 常规](#61-常规)
    - [6.2. 共享](#62-共享)
    - [6.3. 高级](#63-高级)
- [7. 添加共享目录](#7-添加共享目录)
    - [7.1. 常规](#71-常规)
    - [7.2. 版本控制](#72-版本控制)
    - [7.3. 忽略模式](#73-忽略模式)
    - [7.4. 高级](#74-高级)
- [8. RestAPI](#8-restapi)

<!-- /TOC -->

Syncthing是使用Go语言开发的跨平台文件同步工具。开源、免费、简单易用。支持Linux、Windows、Mac、FreeBSD、Solaris等操作系统。你可以在[官网](https://syncthing.net/)找到对应各个平台的下载链接。

# Syncthing @ Centos7

## 1. 安装
```bash
wget https://github-production-release-asset-2e65be.s3.amazonaws.com/14712850/c43fd4be-8f8f-11e8-990e-904dc6f3c490?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20180817%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180817T083727Z&X-Amz-Expires=300&X-Amz-Signature=aef12420a4aa814ef4e2c5ae3e80ced595a224db55785d31fc62ccfd1f883c71&X-Amz-SignedHeaders=host&actor_id=10145852&response-content-disposition=attachment%3B%20filename%3Dsyncthing-linux-amd64-v0.14.49.tar.gz&response-content-type=application%2Foctet-stream
tar zxvf syncthing-linux-amd64-v0.14.49.tar.gz
cd syncthing-linux-amd64-v0.14.49
sudo install syncthing /usr/bin
sudo cp etc/linux-systemd/system/* /lib/systemd/system
sudo cp etc/linux-systemd/user/* /lib/systemd/user
sudo systemctl daemon-reload
```

详细流程可参考[官方文档Geting-Started](https://docs.syncthing.net/intro/getting-started.html#)

## 2. 防火墙配置
```bash
sudo firewall-cmd --permanent --add-port=22000/tcp
sudo firewall-cmd --permanent --add-port=21027/udp
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

## 3. 为当前用户启动syncthing服务
```bash
sudo systemctl enable syncthing@${USER}.service
sudo systemctl start syncthing@${USER}.service
sudo systemctl status syncthing@${USER}.service
```

服务启动后，syncthing会在用户的家目录加创建一个名为Sync的默认共享目录。

## 4. 连接WebUI
通过浏览器访问 http://127.0.0.1:8384 可以连接到syncthing的管理界面。

{{< gallery "/imgs/syncthing-ui.png" >}}

在界面上可以选择语言，支持简体中文。

管理界面十分简单，具体操作可参考官方文档[https://docs.syncthing.net/intro/gui.html]

## 5. 全局设置

### 5.1. 常规设置
{{< gallery "/imgs/syncthing-setting-1.png" >}}

- 设备名称
    默认使用主机名
- 最低可用磁盘空间
    设置主路径所在分区允许的最低可用磁盘空间，若剩余空间小于该百分比，则中止同步
- API Key
    显示当前的RestAPI的Key，可随机生成新Key
- 

### 5.2. UI设置
{{< gallery "/imgs/syncthing-setting-2.png" >}}

- GUI 监听地址
    若设置为`0.0.0.0:8384`，则可以由其他设备访问
- 账户密码设置
    设置界面的准入密码，防止被盗用
- 主题选择
    现在支持Default、Dark、Black三种主题
- 匿名使用报告
    是否向syncthing官方发送统计信息
- 自动升级
    默认仅升级到stable版本
- 默认文件夹路径
    默认情况下，共享文件夹时会创建在用户家目录下，可修改此配置更换目录位置。

### 5.3. 连接设置
{{< gallery "/imgs/syncthing-setting-3.png" >}}

- 协议监听地址
    协议监听地址默认为`default`，即监听在本机的`22000/TCP`端口并使用官方中继服务`dynamic+https://relays.syncthing.net/endpoint`，也可以配置为不使用中继服务，或使用专有中继服务。详情可参考[https://docs.syncthing.net/users/config.html#listen-addresses]
- 上传下载速度限制
    默认为0，不做限制。
- 启用NAT遍历
    **待调研**
- 本地发现
    广播发现syncthing设备
- 全球发现
    使用公网上的全球发现服务进行设备发现
- 开启中继
    当两个设备无法直接通讯时，例如在两个不同的内网，则需要启用中继服务
- 全球发现服务器
    可以设置为私有的发现服务器

## 6. 添加远端设备

点击右下角的`Add Remote Device`可添加待同步的远端设备。

### 6.1. 常规
{{< gallery "/imgs/syncthing-add-device-1.png" >}}

- 设备ID
    在添加远端设备时，必须指定设备ID。在启动之后，syncthing会为本机分配一个全球唯一的ID，可以在UI的右上角菜单中`Action`->`Show ID`查看。若启动了本地发现服务（默认启动），则可以在这里看到已经局域网中已经存在的syncthing设备。
- 设备名
    在没有设置的情况下，syncthing使用主机名作为设备名。在连接远端设备时，可以为其设已一个本地的别名。

### 6.2. 共享
{{< gallery "/imgs/syncthing-add-device-2.png" >}}

- 中介(introducer)开关
    将此新设备上拥有的“远程设备”都自动添加到您这边的“远程设备”列表中（如果它们跟您存在相同的文件夹的话）。
- 自动接受开关
    自动地创建或共享这个远端设备在默认路径通告的文件夹。
- 将指定文件夹共享给设备
    选择并将本地已有的共享文件夹共享给此设备

### 6.3. 高级

{{< gallery "/imgs/syncthing-add-device-3.png" >}}

- 地址列表
    syncthing使用“全球发现服务”和“局域网广播”发现其他的syncthing节点，此时远端设备的IP地址是通过发现服务动态获取的。也可以在“高级”选项卡中手动指定设备端的IP地址，从而不使用发现服务。
- 压缩
    选择要压缩的数据类型，可选项有：不压缩、仅元数据、全部
- 设备速率限制
    可设置上传及下载的速度限制

## 7. 添加共享目录

点击左侧的下方的`添加文件夹`按钮以添加共享目录。

### 7.1. 常规
{{< gallery "/imgs/syncthing-add-folder-1.png" >}}

- 文件夹标签
    即目录名称，可选的文件夹说明性标签。在不同设备上可以不一致。
- 文件夹 ID
    随机生成的ID号，用于区别各个目录
- 文件夹路径
    默认在默认目录下创建，可参考[5.2. UI设置](#52-ui设置)
- 共享给
    将目录共享给选择的设备

### 7.2. 版本控制
{{< gallery "/imgs/syncthing-add-folder-2.png" >}}

支持不启用、回收站式、简易版本控制、阶段版本控制、外部版本控制。

详情可参考[https://docs.syncthing.net/users/versioning.html]

### 7.3. 忽略模式
{{< gallery "/imgs/syncthing-add-folder-3.png" >}}

可以配置不需要同步的文件名patten，在两个设备上均可配置，取并集。

### 7.4. 高级
{{< gallery "/imgs/syncthing-add-folder-4.png" >}}

- 监视更改
    是否启动文件系统的通知功能来监视文件变化。注意即使开启了此功能，也不会在文件变化后立即同步，而是等待一个较短的超时时间，接近一分钟。
- 完整扫描
    设置完整扫描间隔，以免同步发生缺失，默认间隔一小时。对于大型的基本不修改内容的目录，可设置得更大，例如一天。
- 文件夹类型
    支持`发送与接受`，或`仅发送`
- 文件拉取顺序
    支持`字母顺序`、`小文件优先`、`大文件优先`、`新文件优先`、`旧文件优先`
- 最低可用磁盘空间
- 忽略文件权限变化

## 8. RestAPI
为方便其他软件/系统集成syncthing的文件同步功能并通知文件同步状态变化，syncthing提供了一系列的RestAPI，包含一个基于Rest的EventAPI。

为确认API调用者拥有合法的调用权限，在调用时需要将HTTP Header的头部设置X-API-KEY字段为syncthing的apikey的值。该值可以在管理界面的设置中生成并查看，也可以使用下述bash命令获取：

```bash
grep apikey ~/.config/syncthing/config.xml | sed "s/ *<\/*apikey> *//g"
```

具体的API文档请参考[https://docs.syncthing.net/dev/rest.html]

# Syncthing @ Windows

在Windows下，Syncthing提供了带有图形界面的工具[SyncTrayzor](https://github.com/canton7/SyncTrayzor/releases/tag/v1.1.21)，你可以在官网上找到最新版本的下载链接。

软件启动后会弹出一个嵌入浏览器的窗口，其界面风格与使用方式与Linux相同。

<!--more-->
