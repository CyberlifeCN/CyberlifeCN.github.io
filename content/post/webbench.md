---
title: "Centos安装webbench"
date: 2018-09-19T13:29:35+08:00
url: "2018/09/19/centos-webbench"
categories: ["test"]
tags: ["linux"]
banner: "imgs/webbench.jpeg"
---

Webbench是有名的网站压力测试工具，在2004年发布webbench-1.5版本后，一直没有更新，但依旧好用。

<!--more-->
webbench最多可以模拟3万个并发连接去测试网站的负载能力，个人感觉要比Apache自带的ab压力测试工具好，安装使用也特别方便。

## 编译安装

```
wget http://blog.s135.com/soft/linux/webbench/webbench-1.5.tar.gz
tar zxvf webbench-1.5.tar.gz
cd webbench-1.5
make && make install
```

## 使用

```
webbench -c 500 -t 30 http://127.0.0.1/test.jpg/
```
参数说明：-c表示并发数，-t表示时间(秒)

## 测试结果示例
```
Webbench - Simple Web Benchmark 1.5
Copyright (c) Radim Kolar 1997-2004, GPL Open Source Software.

Benchmarking: GET http://127.0.0.1/test.jpg/
500 clients, running 30 sec.

Speed=3230 pages/min, 11614212 bytes/sec.
Requests: 1615 susceed, 0 failed.
```

## centos 下安装会遇到问题：

### make 时报错

 在安装编译webbench的时候，

出现如下错误信息
```
ctags *.c/bin/sh:
ctags: command not foundmake: [tags] Error 127 (ignored)
```
意思是说缺少ctags命令,我没有装了,

运行以下命令即可
```
yum install ctags
```

### make install 时报错
```
[root@ip-10-146-21-171 webbench-1.5]# make install
install -s webbench /usr/local/bin
install -m 644 webbench.1 /usr/local/man/man1
install: cannot create regular file `/usr/local/man/man1': No such file or directory
make: *** [install] Error 1
```
直接执行  
```
mkdir -p /usr/local/man/man1
```
<!--more-->
