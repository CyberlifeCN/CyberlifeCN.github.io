---
title: "Mac OS 安装 Tidis 集群"
date: 2019-01-18T09:55:55+08:00
url: "2019/01/18/macos-tidis"
categories: ["deploy"]
tags: ["MacOS","数据库"]
banner: "imgs/pingcap.jpeg"
---

Tidis是分布式数据库，支持redis协议，多种数据结构支持，编写语言为golang。

<!--more-->
[Mac OS 下安装 Docker 环境](http://blog.cyber-life.cn/2018/07/23/macos-docker/)

## 安装 PD
```
# Set correct HostIP here. 
export HostIP="192.168.1.56"

docker run -d -p 2379:2379 -p 2380:2380 --name pd pingcap/pd:latest \
		  --cluster-id=1 \
		  --name="pd" \
		  --client-urls="http://0.0.0.0:2379" \
		  --advertise-client-urls="http://${HostIP}:2379" \
		  --peer-urls="http://0.0.0.0:2380" \
		  --advertise-peer-urls="http://${HostIP}:2380"
```

验证
```
curl ${HostIP}:2379/v2/members
```

## 安装 TiKV
```
# Set correct HostIP here. 
export HostIP="192.168.1.56"
export DockerFileSharing="/Users/thomas/dockerfs"

docker run -d -p 20160:20160 --name tikv1 \
	-v ${DockerFileSharing}/etc/localtime:/etc/localtime:ro \
	-v ${DockerFileSharing}/data:/data \
	pingcap/tikv:latest \
	--addr="0.0.0.0:20160" \
	--advertise-addr="${HostIP}:20160" \
	--data-dir="/data/tikv1" \
	--pd="${HostIP}:2379"
```

验证
```
curl 192.168.1.56:2379/pd/api/v1/stores
```

## 安装 tidis
```
export DockerFileSharing="/Users/thomas/dockerfs"

docker run  -d --name tidis -p 5379:5379 \
	-v ${DockerFileSharing}:/data  aronfan/tidis \
	-conf="/data/config.toml"
```

vi config.toml
```
# sample configuration file for tidis
desc = "sample configuration for tidis"

[tidis]
listen = “:5379"
max_connection = 5000

#expiration checker
string_checker_loop = 10
string_checker_interval = 100000
list_checker_loop = 10
list_checker_interval = 100
hash_checker_loop = 10
hash_checker_interval = 100
set_checker_loop = 10
set_checker_interval = 100
zset_checker_loop = 10
zset_checker_interval = 100

# true is check ttl and delete if expired before read
# if you have few key to expire, please set it to false
ttl_checker_lazy = false
loglevel = "debug"

#transaction retry count when commit failed in case of conflict
txn_retry = 2
auth = "tides"

#namespace will be used as the key prefix, in case one tidis used by multiple apps
#TODO
namespace = ""

[backend]
#tikv placement driver addresses
pds = "192.168.1.56:2379"
```

验证
```
redis-cli -p 5379 -a tidis
rpush /Users/thomas/dockerfs config.toml data etc
lrange /Users/thomas/dockerfs 0 -1
lset /Users/thomas/dockerfs 1 data2
llen /Users/thomas/dockerfs
```

## 参考
* [What is Tidis?](https://xiking.win/tidis/)
* [TiDB 文档](https://pingcap.com/docs-cn/)

<!--more-->
