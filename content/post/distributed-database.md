---
title: "分布式Key-Value数据库设计参考"
date: 2018-09-15T11:39:35+08:00
url: "2018/09/15/distributed-kv-database"
categories: ["数据库","programming","架构"]
tags: ["linux","go"]
banner: "imgs/ddb.jpg"
---

分布式数据存储是一个真正的技术难道, 不说各种理论, 最根本的问题就是数据怎么迁移.
想想, 原来你只有一个存储节点, 但数据多了之后, 硬盘存不下, 这时怎么把一部分数据迁移到另一个新的存储节点?

<!--more-->
## 这就是数据迁移问题. 这其实是2个问题:
1. 一份数据应该存储在哪个节点? 原有的节点, 还是新加入的节点?
2. 在什么时机, 用什么手段来迁移? 如何保证迁移的过程不影响服务?

有些同学一听到分布式数据存储, 就言必称"一致性哈希".

所谓的"一致性哈希"只解决了上面的问题1, 而且仅仅是解决问题1的众多方案中的一种, 也不是最好的一种.

而对于分布式数据存储来说, 解决了问题1, 只不过是解决了百分之一, 只是皮毛.

## 怎么解决数据迁移?

### 方案一: 引入唯一的 proxy

引入了唯一的 proxy 之后, 所以有请求必须通过 proxy, 那么就可以在 proxy 上做数据迁移, 迁移中的数据如果被请求到, 就先挂起请求, 快的话, 只需要挂起几百毫秒.

这种方案就是 twemproxy 采用的. 缺点也很明显, proxy 很容易成为瓶颈.

### 方案二: 停机维护

这种方案看起来比较土. 当我的存储空间不够的时候, 我就把网站停了, 然后人工操作, 写脚本导数据. 数据导完后, 再采用著名的"使用者自己分布式(客户端分布式)".

## 分布式系统产生的问题

数据的一致性，安全性、可扩展性、服务器的高可用、高承载能力等

## 分布式事务

### 概念

既然是分布式事务，就是要有两个数据源，而实现分布式事务，目前常用的手段就是利用“两阶段提交协议（Two-Phase Commit）”（简称2PC），2PC存在着两个重要的角色：事务协调器（TC），事务执行者

```
zookeeper可以实现分布式事务
```

### 缺点

2PC，可以看到节点之间的通信次数太多了，时间很长！时间变长了，从而导致，事务锁定的资源时间也变长了，造成资源等待时间变长！在高并发场景下，存在严重的性能问题！

### 最终一致性

分布式事务，写操作的时候，就使用乐观更新，使用版本号进行更新，能够保证最终一致性就行了

## 分布式缓存中间件

### Redis

redis是以key-value的形式存储数据，是一个非关系型的，分布式开源的（BSD协议），水平可扩展的缓存服务器。redis用途：缓存（StackOverFlow），数据库（微博），消息中间件（微博）

### Memcached

memcached 是由 Danga Interactive 开发并使用 BSD 许可的一种通用的分布式内存缓存系统。也是以key-value的形式存储数据。由于它的工作机制是在内存中开辟一块空间，然后建立一个HashTable，Memcached自管理这些HashTable。

### SSDB

SSDB是一个快速的用来存储十亿级别列表数据的开源NoSql数据库，可以替代Redis数据库，是Redis的100倍容量。LevelDB网络支持，使用C/C++开发。与Redis完美兼容。

### Redis、Memcached、SSDB缓存数据库简单比较

1. 都是key-value方式存储
2. redis支持多种数据类型，如String Hash Set List等,并且支持集群方案（3.x）
3. Redis单线程操作（少了内部的锁，性能好，可以保证数据的原子性，Redis可以有多个实例变相提供并发操作），Memcached多线程操作（耗资源）。Memcached支持事务的概念，Redis事务支持很弱（Redis只能保证一个Key的原子性操作，多个Key的事务操作是不支持的，可以使用Lua脚本解决）。
4. Redis与SSDB，SSDB是基于google性能极高的LevelDB作为存储引擎去架构的，而且可以与Redis完美整合，一般会使用相互结合的方案，因为SSDB性能高大上，但是高可用性不如Redis。（Redis和SSDB结合，Redis可以解决高并发的读，SSDB解决高并发的写。 ）---推荐方式
5. Redis持久化方式：RDB、AOF（推荐）

### QDB
是一个类似Redis的快速Key-Value数据库，它有以下优点：
1. 兼容Redis：如果你对 Redis很熟悉，你就能轻松使用QDB，它支持大多数的Redis命令和数据结构（String、Hash、List、Set、Sorted Set等）；
2. 将数据保存在磁盘：（超越内存大小限制）可以将热点数据在内存中保存，利用了后端存储；
3. 支持多种后端存储：你可以选择 RocksDB、LevelDB 或者 GoLevelDB（稍后，我们将用RocksDBs作为例子讲解）；
4. 和Redis双向同步：我们可以作为一个从节点从 Redis同步数据，也可以作为一个主节点复制数据到Redis。

## RebornDB：下一代分布式Key-Value数据库
RebornDB是一个基于代理的分布式Redis集群解决方案，有点像twemproxy。它有一个杀手锏：动态的切分数据集，即透明的重新切分数据而不影响目前正在运行的服务。

我们可以将RebornDB想象成一个黑盒，像一个单节点的Redis服务器一样用任何现有的Redis客户端去和它通信。下面的图片展示了RebornDB的架构。

{{< gallery "/imgs/RebornDB.jpg" >}}

RebornDB有以下组件： reborn-proxy, backend store, coordinator, reborn-config, and reborn-agent.

### 重新切分（Resharding）

RebornDB支持动态的重新切分数据。是怎么做到的呢？

RebornDB将整个数据集分成1024个slots，并且将不同的slot保存到不同的组。当我们新增加一个组的时候，我们会将一些slots从旧的组迁移到新组中。在重新切分过程中我们将它叫做迁移。在RebornDB中最小的迁移单元是slot。

让我们从下面的一个简单的例子开始：

{{< gallery "/imgs/RebornDB-1.jpg" >}}

我们有两个组，group1有两个slot 1和2，group2有三个slot 3、4、5.现在group2的工作量比较大，我们将会增加group3并将slot5迁移进去。

我们可以使用下面的命令来将slot5从group2迁移至group3。

```
reborn-config slot migrate 5 2 3
```

这个命令看起来很简单，但是我们需要在内部做很多工作来保证迁移的安全性。我们必须使用两阶段提交协议（2PC）来告诉reborn-proxy我们将把slot5从group2迁移至group3。待所有reborn-proxy确认并且答复之后，我们将开始迁移操作。

{{< gallery "/imgs/RebornDB-2.jpg" >}}

迁移的流程比较简单：从slot5中得到一个key，从group2迁移它的数据至group3，然后删除group2中的key，如此循环。最后group2中就没有slot5的数据而所有slot5的数据都在group3中。

key的迁移是原子性的，因此无论这个key之前是否在group2或者group3中，我们能够确定的是在执行迁移命令之后它是在group3中的。

如果在group2中没有数据是属于slot5的，我们将停止迁移，拓扑结构看起来是下面这个样子：

{{< gallery "/imgs/RebornDB-3.jpg" >}}

### 高可用性（High Availability）

RebornDB使用reborn-agent来提供HA解决方案。

reborn-agent每时每刻都在检查它启动的应用是否是活动的，如果reborn-agent发现一个应用挂掉了，它会重新启动这个应用。

## 参考
* [SSDB 分布式的一些想法](http://www.ideawu.net/blog/archives/838.html)
* [RebornDB：下一代分布式Key-Value数据库](https://www.csdn.net/article/2015-07-13/2825186)
* [reborndb](https://github.com/reborndb)

<!--more-->
