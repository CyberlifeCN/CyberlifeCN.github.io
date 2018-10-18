---
title: "最新压缩率之王xz"
date: 2018-10-18T16:49:35+08:00
url: "2018/10/18/xz"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/xz.png"
---
XZ Utils 是为 POSIX 平台开发具有高压缩率的工具。
与 POSIX 系统上传统的压缩工具 gzip、bzip2 相比，它能够提供更高的压缩率，生成的文件更小，而且解压数据的速度也很快。
对于典型文件，XZ Utils 能够生成比使用 gzip 小 30%，比使用 bzip 小 15% 的压缩文件。

<!--more-->
## xz压缩文件方法或命令
```
xz -z 要压缩的文件
```
如果要保留被压缩的文件加上参数 -k ，如果要设置压缩率加入参数 -0 到 -9调节压缩率。如果不设置，默认压缩等级是6


## xz解压文件方法或命令
```
xz -d 要解压的文件
```
同样使用 -k 参数来保留被解压缩的文件。


## 创建或解压tar.xz文件的方法

习惯了 tar czvf 或 tar xzvf 的人可能碰到 tar.xz也会想用单一命令搞定解压或压缩。
其实不行 tar里面没有征对xz格式的参数比如 z是针对 gzip，j是针对 bzip2。


## 创建tar.xz文件
只要先 tar cvf xxx.tar xxx/ 这样创建xxx.tar文件先，
然后使用 xz -z xxx.tar 来将 xxx.tar压缩成为 xxx.tar.xz
<!--more-->
