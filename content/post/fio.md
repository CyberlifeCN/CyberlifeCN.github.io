---
title: "IO测试工具之fio"
date: 2018-09-18T17:29:35+08:00
url: "2018/09/18/fio"
categories: ["test"]
tags: ["linux"]
banner: "imgs/fio.jpg"
---

目前主流的第三方IO测试工具有fio、iometer，这两种种工具各有千秋。

fio在Linux系统下使用比较方便，iometer在window系统下使用比较方便。

<!--more-->
如下是在Linux系统上采用fio工具来对SAN存储进行的IO测试。

1、安装fio

在fio官网下载fio-2.1.10.tar文件，解压后./configure、make、make install之后就可以使用fio了。

2、fio参数解释

可以使用fio -help查看每个参数，具体的参数左右可以在官网查看how to文档，如下为几个常见的参数描述

```
filename=/dev/emcpowerb　支持文件系统或者裸设备，-filename=/dev/sda2或-filename=/dev/sdb
direct=1                 测试过程绕过机器自带的buffer，使测试结果更真实
rw=randwread             测试随机读的I/O
rw=randwrite             测试随机写的I/O
rw=randrw                测试随机混合写和读的I/O
rw=read                  测试顺序读的I/O
rw=write                 测试顺序写的I/O
rw=rw                    测试顺序混合写和读的I/O
bs=4k                    单次io的块文件大小为4k
bsrange=512-2048         同上，提定数据块的大小范围
size=5g                  本次的测试文件大小为5g，以每次4k的io进行测试
numjobs=30               本次的测试线程为30
runtime=1000             测试时间为1000秒，如果不写则一直将5g文件分4k每次写完为止
ioengine=psync           io引擎使用pync方式，如果要使用libaio引擎，需要yum install libaio-devel包
rwmixwrite=30            在混合读写的模式下，写占30%
group_reporting          关于显示结果的，汇总每个进程的信息
此外
lockmem=1g               只使用1g内存进行测试
zero_buffers             用0初始化系统buffer
nrfiles=8                每个进程生成文件的数量
```

3、fio测试场景及生成报告详解

测试场景：
```
100%随机，100%读， 4K
fio -filename=/dev/emcpowerb -direct=1 -iodepth 1 -thread -rw=randread -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=rand_100read_4k

100%随机，100%写， 4K
fio -filename=/dev/emcpowerb -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=rand_100write_4k

100%顺序，100%读 ，4K
fio -filename=/dev/emcpowerb -direct=1 -iodepth 1 -thread -rw=read -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=sqe_100read_4k

100%顺序，100%写 ，4K
fio -filename=/dev/emcpowerb -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=sqe_100write_4k

100%随机，70%读，30%写 4K
fio -filename=/dev/emcpowerb -direct=1 -iodepth 1 -thread -rw=randrw -rwmixread=70 -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=randrw_70read_4k
```

结果报告查看：
```
[root@rac01-node02]# fio -filename=/dev/sdc4 -direct=1 -iodepth 1 -thread -rw=randrw -rwmixread=70 -ioengine=psync -bs=4k -size=1000G -numjobs=50 -runtime=180 -group_reporting -name=randrw_70read_4k_local
randrw_70read_4k_local: (g=0): rw=randrw, bs=4K-4K/4K-4K/4K-4K, ioengine=psync, iodepth=1
...
fio-2.1.10
Starting 50 threads
Jobs: 21 (f=21): [____m____m_m___m____mmm__mmm__mm_m_mmm_m__m__m_m_m] [3.4% done] [7004KB/2768KB/0KB /s] [1751/692/0 iops] [eta 01h:27m:00s]
randrw_70read_4k_local: (groupid=0, jobs=50): err= 0: pid=13710: Wed May 31 10:23:31 2017
  read : io=1394.2MB, bw=7926.4KB/s, iops=1981, runt=180113msec
    clat (usec): min=39, max=567873, avg=24323.79, stdev=25645.98
     lat (usec): min=39, max=567874, avg=24324.23, stdev=25645.98
    clat percentiles (msec):
     |  1.00th=[    3],  5.00th=[    5], 10.00th=[    6], 20.00th=[    7],
     | 30.00th=[    9], 40.00th=[   12], 50.00th=[   16], 60.00th=[   21],
     | 70.00th=[   27], 80.00th=[   38], 90.00th=[   56], 95.00th=[   75],
     | 99.00th=[  124], 99.50th=[  147], 99.90th=[  208], 99.95th=[  235],
     | 99.99th=[  314]
    bw (KB  /s): min=   15, max=  537, per=2.00%, avg=158.68, stdev=38.08
  write: io=615280KB, bw=3416.8KB/s, iops=854, runt=180113msec
    clat (usec): min=167, max=162537, avg=2054.79, stdev=7665.24
     lat (usec): min=167, max=162537, avg=2055.38, stdev=7665.23
    clat percentiles (usec):
     |  1.00th=[  201],  5.00th=[  227], 10.00th=[  249], 20.00th=[  378],
     | 30.00th=[  548], 40.00th=[  692], 50.00th=[  844], 60.00th=[  996],
     | 70.00th=[ 1160], 80.00th=[ 1304], 90.00th=[ 1720], 95.00th=[ 3856],
     | 99.00th=[40192], 99.50th=[58624], 99.90th=[98816], 99.95th=[123392],
     | 99.99th=[148480]
    bw (KB  /s): min=    6, max=  251, per=2.00%, avg=68.16, stdev=29.18
    lat (usec) : 50=0.01%, 100=0.03%, 250=3.15%, 500=5.00%, 750=5.09%
    lat (usec) : 1000=4.87%
    lat (msec) : 2=9.64%, 4=4.06%, 10=21.42%, 20=18.08%, 50=19.91%
    lat (msec) : 100=7.24%, 250=1.47%, 500=0.03%, 750=0.01%
  cpu          : usr=0.07%, sys=0.21%, ctx=522490, majf=0, minf=7
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued    : total=r=356911/w=153820/d=0, short=r=0/w=0/d=0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
   READ: io=1394.2MB, aggrb=7926KB/s, minb=7926KB/s, maxb=7926KB/s, mint=180113msec, maxt=180113msec
  WRITE: io=615280KB, aggrb=3416KB/s, minb=3416KB/s, maxb=3416KB/s, mint=180113msec, maxt=180113msec

Disk stats (read/write):
  sdc: ios=356874/153927, merge=0/10, ticks=8668598/310288, in_queue=8978582, util=99.99%
```

```
io=执行了多少M的IO
bw=平均IO带宽
iops=IOPS
runt=线程运行时间
slat=提交延迟
clat=完成延迟
lat=响应时间
bw=带宽
cpu=利用率
IO depths=io队列
IO submit=单个IO提交要提交的IO数
IO complete=Like the above submit number, but for completions instead.
IO issued=The number of read/write requests issued, and how many of them were short.
IO latencies=IO完延迟的分布

io=总共执行了多少size的IO
aggrb=group总带宽
minb=最小.平均带宽.
maxb=最大平均带宽.
mint=group中线程的最短运行时间.
maxt=group中线程的最长运行时间.

ios=所有group总共执行的IO数.
merge=总共发生的IO合并数.
ticks=Number of ticks we kept the disk busy.
io_queue=花费在队列上的总共时间.
util=磁盘利用率
```

4、扩展之IO队列深度

在某个时刻,有N个inflight的IO请求,包括在队列中的IO请求、磁盘正在处理的IO请求。N就是队列深度。
加大硬盘队列深度就是让硬盘不断工作，减少硬盘的空闲时间。
加大队列深度 -> 提高利用率 -> 获得IOPS和MBPS峰值 ->注意响应时间在可接受的范围内，
增加队列深度的办法有很多，使用异步IO，同时发起多个IO请求，相当于队列中有多个IO请求，多线程发起同步IO请求，相当于队列中有多个IO请求。
增大应用IO大小，到达底层之后，会变成多个IO请求，相当于队列中有多个IO请求 队列深度增加了。
队列深度增加了，IO在队列的等待时间也会增加，导致IO响应时间变大，这需要权衡。

为何要对磁盘I/O进行并行处理呢？主要目的是提升应用程序的性能。这一点对于多物理磁盘组成的虚拟磁盘（或LUN）显得尤为重要。
如果一次提交一个I/O，虽然响应时间较短，但系统的吞吐量很小。
相比较而言，一次提交多个I/O既缩短了磁头移动距离（通过电梯算法），同时也能够提升IOPS。
假如一部电梯一次只能搭乘一人，那么每个人一但乘上电梯，就能快速达到目的地（响应时间），但需要耗费较长的等待时间（队列长度）。
因此一次向磁盘系统提交多个I/O能够平衡吞吐量和整体响应时间。

Linux系统查看默认队列深度：

```
[root@qsdb ~]# lsscsi -l
[0:0:0:0]    disk    DGC      VRAID            0533  /dev/sda
  state=running queue_depth=30 scsi_level=5 type=0 device_blocked=0 timeout=30
[0:0:1:0]    disk    DGC      VRAID            0533  /dev/sdb
  state=running queue_depth=30 scsi_level=5 type=0 device_blocked=0 timeout=30
[2:0:0:0]    disk    DGC      VRAID            0533  /dev/sdd
  state=running queue_depth=30 scsi_level=5 type=0 device_blocked=0 timeout=30
[2:0:1:0]    disk    DGC      VRAID            0533  /dev/sde
  state=running queue_depth=30 scsi_level=5 type=0 device_blocked=0 timeout=30
[4:2:0:0]    disk    IBM      ServeRAID M5210  4.27  /dev/sdc
  state=running queue_depth=256 scsi_level=6 type=0 device_blocked=0 timeout=90
[9:0:0:0]    cd/dvd  Lenovo   SATA ODD 81Y3677 IB00  /dev/sr0
  state=running queue_depth=1 scsi_level=6 type=5 device_blocked=0 timeout=30
```

使用dd命令设置bs=2M进行测试：
```
dd if=/dev/zero of=/dev/sdd bs=2M count=1000 oflag=direct
```
记录了1000+0 的读入 记录了1000+0 的写出 2097152000字节(2.1 GB)已复制，10.6663 秒，197 MB/秒
```
Device: rrqm/s   wrqm/s   r/s   w/s   rsec/s   wsec/s   avgrq-sz   avgqu-sz   await   svctm   %util
sdd      0.00     0.00   0.00  380.60  0.00    389734.40  1024.00  2.39       6.28    2.56    97.42
```
可以看到2MB的IO到达底层之后，会变成多个512KB的IO，平均队列长度为2.39，这个硬盘的利用率是97%，MBPS达到了197MB/s。
(为什么会变成512KB的IO，你可以去使用Google去查一下内核参数 max_sectors_kb的意义和使用方法 )也就是说增加队列深度，是可以测试出硬盘的峰值的。

5、Linux系统中查看IO命令iostat详解
```
[root@rac01-node01 /]# iostat -xd 3
Linux 3.8.13-16.2.1.el6uek.x86_64 (rac01-node01)     05/27/2017     _x86_64_    (40 CPU)
Device:         rrqm/s   wrqm/s     r/s     w/s   rsec/s   wsec/s avgrq-sz avgqu-sz   await  svctm  %util
sda               0.05     0.75    2.50    0.50    76.59    69.83    48.96     0.00    1.17   0.47   0.14
scd0              0.00     0.00    0.02    0.00     0.11     0.00     5.25     0.00   21.37  20.94   0.05
dm-0              0.00     0.00    2.40    1.24    75.88    69.83    40.00     0.01    1.38   0.38   0.14
dm-1              0.00     0.00    0.02    0.00     0.14     0.00     8.00     0.00    0.65   0.39   0.00
sdc               0.00     0.00    0.01    0.00     0.11     0.00    10.20     0.00    0.28   0.28   0.00
sdb               0.00     0.00    0.01    0.00     0.11     0.00    10.20     0.00    0.15   0.15   0.00
sdd               0.00     0.00    0.01    0.00     0.11     0.00    10.20     0.00    0.25   0.25   0.00
sde               0.00     0.00    0.01    0.00     0.11     0.00    10.20     0.00    0.14   0.14   0.00
```
输出参数描述：
```
rrqms：每秒这个设备相关的读取请求有多少被Merge了（当系统调用需要读取数据的时候，VFS将请求发到各个FS，如果FS发现不同的读取请求读取的是相同Block的数据，FS会将这个请求合并Merge）
wrqm/s：每秒这个设备相关的写入请求有多少被Merge了。
rsec/s：The number of sectors read from the device per second.
wsec/s：The number of sectors written to the device per second.
rKB/s：The number of kilobytes read from the device per second.
wKB/s：The number of kilobytes written to the device per second.
avgrq-sz：平均请求扇区的大小,The average size (in sectors) of the requests that were issued to the device.
avgqu-sz：是平均请求队列的长度。毫无疑问，队列长度越短越好,The average queue length of the requests that were issued to the device.   
await：每一个IO请求的处理的平均时间（单位是微秒毫秒）。这里可以理解为IO的响应时间，一般地系统IO响应时间应该低于5ms，如果大于10ms就比较大了。
这个时间包括了队列时间和服务时间，也就是说，一般情况下，await大于svctm，它们的差值越小，则说明队列时间越短，反之差值越大，队列时间越长，说明系统出了问题。
svctm：表示平均每次设备I/O操作的服务时间（以毫秒为单位）。如果svctm的值与await很接近，表示几乎没有I/O等待，磁盘性能很好。
如果await的值远高于svctm的值，则表示I/O队列等待太长，系统上运行的应用程序将变慢。
%util： 在统计时间内所有处理IO时间，除以总共统计时间。例如，如果统计间隔1秒，该设备有0.8秒在处理IO，而0.2秒闲置，那么该设备的%util = 0.8/1 = 80%，
所以该参数暗示了设备的繁忙程度，一般地，如果该参数是100%表示磁盘设备已经接近满负荷运行了（当然如果是多磁盘，即使%util是100%，因为磁盘的并发能力，所以磁盘使用未必就到了瓶颈）。
```

<!--more-->
