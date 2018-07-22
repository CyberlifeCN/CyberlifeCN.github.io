---
title: "Mac OS 下安装 Docker 环境"
date: 2018-07-23T02:15:55+08:00
url: "2018/07/23/macos-docker"
categories: ["deploy"]
tags: ["MacOS","docker"]
banner: "imgs/docker-macos.jpg"
---

Docker 是一个开源的应用容器引擎，可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

<!--more-->

## Docker的应用场景

* Web 应用的自动化打包和发布。
* 自动化测试和持续集成、发布。
* 在服务型环境中部署和调整数据库或其他的后台应用。
* 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。

## Docker 的优点

1. 简化程序：
Docker 让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，便可以实现虚拟化。Docker改变了虚拟化的方式，使开发者可以直接将自己的成果放入Docker中进行管理。方便快捷已经是 Docker的最大优势，过去需要用数天乃至数周的任务，在Docker容器的处理下，只需要数秒就能完成。

2. 避免选择恐惧症：
如果你有选择恐惧症，还是资深患者。Docker 帮你	打包你的纠结！比如 Docker 镜像；Docker 镜像中包含了运行环境和配置，所以 Docker 可以简化部署多种应用实例工作。比如 Web 应用、后台应用、数据库应用、大数据应用比如 Hadoop 集群、消息队列等等都可以打包成一个镜像部署。

3. 节省开支：
一方面，云计算时代到来，使开发者不必为了追求效果而配置高额的硬件，Docker 改变了高性能必然高价格的思维定势。Docker 与云的结合，让云空间得到更充分的利用。不仅解决了硬件管理的问题，也改变了虚拟化的方式。

## Docker由下面几样东西组成

* Docker Client : Docker提供给用户的客户端。Docker Client提供给用户一个终端，用户输入Docker提供的命令来管理本地或者远程的服务器。
* Docker Daemon : Docker服务的守护进程。每台服务器（物理机或虚机）上只要安装了Docker的环境，基本上就跑了一个后台程序Docker Daemon，Docker Daemon会接收Docker Client发过来的指令,并对服务器的进行具体操作。
* Docker Images : 俗称Docker的镜像，这个可难懂了。你暂时可以认为这个就像我们要给电脑装系统用的系统CD盘，里面有操作系统的程序，并且还有一些CD盘在系统的基础上安装了必要的软件，做成的一张 “只读” 的CD。
* Docker Registry : 这个可认为是Docker Images的仓库，就像git的仓库一样，用来管理Docker镜像的，提供了Docker镜像的上传、下载和浏览等功能，并且提供安全的账号管理可以管理只有自己可见的私人image。就像git的仓库一样，docker也提供了官方的Registry，叫做Dock Hub(http://hub.Docker.com)
* Docker Container : 俗称Docker的容器，这个是最关键的东西了。Docker Container是真正跑项目程序、消耗机器资源、提供服务的地方，Docker Container通过Docker Images启动，在Docker Images的基础上运行你需要的代码。 你可以认为Docker Container提供了系统硬件环境，然后使用了Docker Images这些制作好的系统盘，再加上你的项目代码，跑起来就可以提供服务了。 听到这里，可能你会觉得是不是有点像一个VM利用保存的备份或者快照跑起来环境一样，其实是挺像的，但是实际上是有本质的区别，后面我会细说。

{{< gallery "/imgs/docker-arch.png" >}}

## Docker Toolbox
Docker Toolbox目前支持Mac和Windows平台。使用它可以快速地在安装Docker工具集。

{{< gallery "/imgs/docker-toolbox.png" >}}

Toolbox可以安装你在开发中运行Docker时所需要的一切：Docker客户端、Compose（仅Mac需要）、Kitematic、Machine以及VirtualBox。Toolbox使用Machine和VirtualBox在虚拟机中创建了一个引擎来运行容器。在该虚拟机上，你可以使用Docker客户端、Compose以及Kitematic来运行容器。

{{< gallery "/imgs/docker-kitematic.png" >}}

## Docker 怎么玩
```
docker version
docker images
docker ps -a
docker search
docker pull image-name
docker run image-name
docker commit container-id
```

## Docker 与虚拟机的区别
我们知道VM技术可以将一台物理机器部署为多台虚拟机器，解决了很多物力资源的浪费以及方便的管理能力。那么VM是怎么做到的呢？关键词：Hypervisor，VM在物理机器的操作系统上建立了一个中间软件层Hypervisor，Hypervisor利用物理机器的资源，虚拟出多个新虚拟的硬件环境，这些硬件环境可以共享宿主机的资源。这些新的虚拟的硬件环境，安装操作系统和相应的软件后便形成了一台台的虚拟机器。
那么Docker有什么不同呢？Docker很聪明的利用linux的一些技术走了一条捷径：Docker选择了和虚拟化完全不同的思路，并不去虚拟化任何硬件，而是对硬件资源在不同的docker container之间做了 “隔离” 。隔离使每个docker container之间拥有了不同的环境（硬盘空间、网络、系统的工具包），并且又可以共享需要的硬件资源（cpu、内存、系统内核），达到了和虚拟机能提供的同样的功能。

{{< gallery "/imgs/docker-vm.jpg" >}}

## Docker使用的Linux核心的组件

* AUFS(chroot) – 用来建立不同的操作系统和隔离运行时的硬盘空间
* Namespace – 用来隔离Container的执行空间
* Cgroup – 分配不同的硬件资源
* SELinux – 用来保护linux的网络安全
* Netlink – 用来让不同的Container之间的进程保持通信
* Netfilter – 建立Container埠為基礎的網路防火牆封包過濾
* AppArmor – 保護Container的網路及執行安全
* Linux Bridge – 讓不同Container或不同主機上的Container能溝通

{{< gallery "/imgs/docker-linux.png" >}}

* [Docker 官网](http://www.docker.com)
* [Github Docker 源码](https://github.com/docker/docker)
* [docker 菜鸟教程](http://www.runoob.com/docker/docker-tutorial.html)
* [Docker Toolbox Mac版 V18.03.0 下载地址](http://www.pc6.com/mac/225744.html)
* [Docker kitematic 下载地址](https://github.com/docker/kitematic/releases)
<!--more-->
