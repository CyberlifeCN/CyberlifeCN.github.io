---
title: "IO压力测试工具 -- IOMETER 使用说明"
date: 2018-09-18T17:51:35+08:00
url: "2018/09/18/iometer"
categories: ["test"]
tags: ["linux"]
banner: "imgs/iometer.jpg"
---

Iometer是一个单机或者集群的I/O子系统的测量和描述工具。它最初是由英特尔公司在1998年2月17日的英特尔开发者论坛（IDF）宣布，自那时以来，在行业内广泛的蔓延，成为了标准。与此同时，英特尔已经停止Iometer的开发工作并把开放源码交给了OSDL。 2001年11月，Iometer在SourceForge.net注册，开发项目从2003年月2月起又重新启动，当然项目维护者变成了独立的一个内部工作组。

<!--more-->
IOMeter可用于测试系统的存储性能。在linux上，IOMeter包括两部分：IOMeter主程序和Dynamo执行代理。可以在Windows上安装运行IOMeter主程序，在Linux上安装运行IOMeter执行代理，主程序就会把读写配置传递给执行代理来执行。本文将在Windows 7上安装IOMeter主程序，在CentOS 6.6上编译安装Dynamo执行代理，然后对CentOS服务器进行存储性能测试。

一、安装IOMeter主程序（Windows端）

1. 下载IOMeter主程序

下载链接为：
http://colocrossing.dl.sourceforge.net/project/iometer/iometer-devel/1.1.0-rc1/iometer-1.1.0-rc1-win64.x86_64-bin.zip

2. 安装主程序

将下载得到的压缩包解压至Windows 7主机的D:盘根目录即可，主程序的路径为：D:\iometer\IOMETER.exe

二、安装Dynamo执行代理（Linux端）

1. 下载源码包

在Shell中执行以下命令：
```
cd /root/Downloads
wget http://tcpdiag.dl.sourceforge.net/project/iometer/iometer-devel/1.1.0-rc1/iometer-1.1.0-rc1-src.tar.bz2
tar -xvjf iometer-1.1.0-rc1-src.tar.bz2
```

2. 编译安装执行代理

在Shell中执行以下命令：
```
touch /usr/include/stropts.h
cd iometer-1.1.0-rc1/src
make -f Makefile-Linux.x86_64 all
cp -a /root/Downloads/iometer-1.1.0-rc1/src/dynamo /usr/local/bin/
```

三、存储性能测试

1. 运行IOMeter主程序

在Windows 7主机上运行D:\iometer\IOMETER.exe。

2. 运行Dynamo执行代理

在Linux主机上执行如下命令：
```
dynamo -i 192.168.124.71 -m 192.168.124.70
```
其中，-i参数后面是Windows主机的IP地址，-m参数后面是Linux主机的地址。

注意，一定要先运行IOMeter主程序，然后再运行Dynamo执行代理，否则可能会产生错误。

3. 使用IOMeter

执行完上面两步操作之后，可以看到RHEL.PerfTest，也就是Linux主机的主机名被添加到manager中。Topology显示了所有manager（dynamo副本）的层次化结构以及所有激活的worker（每个dynamo副本的线程数，跟CPU核心有关）。

Disk Targets面板如下图所示：
{{< gallery "/imgs/iometer-1.png" >}}

* Targets：显示了dynamo所在主机的不同分区，在windows上就是C、D，在Linux上就是 sda、sdb等；

* Maxium Disk Size：设置每个worker使用的扇区数，默认每扇区 512 字节；

* Starting Disk Sector：设置每个worker使用的起始扇区；

* of Outstanding I/Os：模拟测试多个应用向 IO 请求读写，默认是 1。通常不用这个参数，除非是用在 NAS/SAN 上面。此参数和”Test Setup”面板上的Cycling Options 有关。

Access Specification面板如下图所示：
{{< gallery "/imgs/iometer-2.png" >}}

在右侧Global Access Specifications列出了常用的 IO参数，用户可以直接选中添加到左侧使用，也可以双击或点击 Edit自定义参数，弹出界面如下：
{{< gallery "/imgs/iometer-3.png" >}}

* Transfer Request Size：设置传输块大小，这个参数值越大则 IOPS就越小。

* Percent Random/Sequential Distribution：设置读写数据的随机性，即顺序 I/O 和随机 I/O 的占用比例。要得到较高的 IO 性能当然要选100%顺序。

* Percent Read/Write Distribution：设置读写方式，即读和写在 IO中的占用比例。硬盘的读性能要比写性能好，所以 100%读可以得到较高的性能数据。

Test Setup面板如下图所示：
{{< gallery "/imgs/iometer-4.png" >}}

* Run Time：设置测试运行时间

* Ramp Up Time：一些高级的 RAID 系统通常会有一块 RAM 当做 IOcache，为避免开始得到的数据来自于这些cache，系统会将这个参数设置的时间不列入最后的性能计算，以便得到真正的 IO 值。

* Results Display面板如下图所示：

* Results Since：分为两种，Start of Test表示查看iometer在多种参数下跑过的平均值和总和；Last Update表示只看这一次的结果不和前次比较。

* Update Frequency(seconds)：设置测试时数据显示刷新频率，通常设为1基本为实时查看，最右边为无穷大表示测试完成后再查看数据。

* Total I/Os per Second：每秒IO数的平均值，它和 transfer request size有关，反比关系。

* Total MBs per Second：每秒传输的数据量，提高性能可以将transfer request size增大并且为100%顺序读。

* Average I/O Response Time：平均IO响应时间。

* Maxium I/O Response Time：最大IO响应时间。

测试完成后结果会保存在csv文件以便处理。

4. 测试模式分析

根据不同的测试目的，可以设置不同的测试参数进行测试，这些测试参数可以在Access Specification面板中进行设置。

* 最大IO处理能力测试
transfer request size为512bytes；
percent random/sequential distribution为100%顺序；
percent read/write distribution为100%读。

* 最大带宽能力测试
transfer request size为64 Kbytes；
percent random/sequential distribution为100%顺序；
percent read/write distribution为100%读。

* 文件服务器
transfer request size为64 Kbytes；
percent random/sequential distribution为100%随机；
percent read/write distribution为80%读 20%写。

* Web 服务器
transfer request size为512 Kbytes；
percent random/sequential distribution为100%随机；
percent read/write distribution为100%读。

* 在线交易OLTP
transfer request size为8 Kbytes；
percent random/sequential distribution为100%随机；
percent read/write distribution为67%读%33%写。

当所有参数都设置完成之后，点击按钮即可启动测试，结果在Results Display面板显示。

5. 常见错误

常见的Iometer错误就是当网络环境没有使用DNS造成hostname和IP无法解析的时候就会看到下面的错误提示：
```
===> ERROR: Getting host name for "localhost.localdomain" failed.
[PortTCP::Create() in IOPortTCP.cpp line 238]
errno = 11
*** Could not create a TCP/IP Port. exiting.....
```
解决方法是编辑hosts文件，添加记录如127.0.0.1 RHEL.PerfTest。Linux主机的hosts文件为/etc/hosts。

<!--more-->
