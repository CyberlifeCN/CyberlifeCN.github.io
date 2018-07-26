+++
title = "Linux Operation"
date = "2018-06-13T21:24:51+08:00"
url = "2018/06/13/linux-operation"
description = "linux 运维中常用命令总结"
images = []
menu = ""
tags = ["linux"]
categories = ["deploy"]
banner = "imgs/linux-5_banner.jpg"
+++

linux 初始化时经常使用，总结一下！

<!--more-->

### 1.创建账号
```
# useradd thomas
# passwd thomas
```

### 2.上传、下载文件
```
$ scp local_filename thomas@hostname:.
$ scp thomas@hostname:remote_filename .
```

### 3.无密码登录
    在远程主机生成密匙
    $ ssh-keygen -t rsa

    用ssh-copy-id 把公钥复制到远程主机上
    $ ssh-copy-id -i ~/.ssh/id_rsa.pub thomas@192.168.0.3

### 4.安装java
```
# yum -y list java*
# yum -y install java-1.8.0-openjdk*
```

### 5.ln
```
# rm /usr/bin/java
# ln /System/Library/Frameworks/JavaVM.framework/Versions/1.6/Commands/java /usr/bin/java
# ln /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java /usr/bin/java
```

### 6.mac os切换java的版本
    $ vi ~/.bash_profile
    export JAVA_6_HOME=/System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
    export JAVA_8_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_60.jdk/Contents/Home
    export JAVA_HOME=$JAVA_6_HOME
    alias jdk6="export JAVA_HOME=$JAVA_6_HOME"
    alias jdk8="export JAVA_HOME=$JAVA_8_HOME"
    $ source ~/.bash_profile
    $ java -version

### 7.启动memcached
    $ memcached -d -m 64 -l siriusb -p 11211

### 8.mac os启动mysql
```
# /usr/local/mysql/bin/mysqld_safe &
```

### 9.查看当前活动句柄数
```
# while true; do netstat -a | wc -l; done
```

### 10.查看某个进程开了几个句柄
```
# lsof -p pid |wc -l
```

### 11.gradle
    $ gradle --help
    $ gradle clean
    $ gradle ec
    $ gradle -b build-cli.gradle uploadArchives
    $ gradle -b build.gradle uploadArchives
    $ gradle --refresh-dependencies dependencies
    $ gradle --refresh-dependencies eclipse

### 12.80端口映射为8080端口
```
＃ echo 1 > /proc/sys/net/ipv4/ip_forward
＃ iptables -I PREROUTING -t nat -p tcp --dport 80 -j REDIRECT --to-ports 8080
＃ service iptables save
＃ iptables-save > /etc/sysconfig/iptables
```

### 13.上传远程文件，自动判断是否有更新，无更新，不上传
```
$ rsync -avn — exclude “log/“ ~/neuron-pkg/aplan-web-workspace/aplan-web/ thomas@siriusa:tornado-4.3/aplan-web
```

### 14.阻止root远程登录
```
# vi /etc/ssh/sshd_config
PermitRootLogin no
# service sshd restart
```

### 15.修改主机名
```
# hostname yourhostname
# vi /etc/sysconfig/network
# vi /etc/hosts
# hostnamectl --static set-hostname yourhostname
```

### 16.如何配置 sudo 命令在运行时而不输入密码
```
# vi /etc/sudoers
root    ALL=(ALL)       ALL
thomas  ALL=(ALL)       NOPASSWD: ALL
```

### 17.中文环境如何改成英文
```
$ vi /etc/bashrc # 修改环境文件，加入一行
export LC_ALL=en_US.UTF-8
```  
<!--more-->
