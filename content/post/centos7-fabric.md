---
title: "在 CentOS7 下安装 fabric"
date: 2018-08-31T17:45:35+08:00
url: "2018/08/31/centos7-fabric"
categories: ["programming"]
tags: ["linux","golang"]
banner: "imgs/Huawei_Cloud_Fabric.jpg"
---

参加华为的《21天转型区块链实战营》活动，今日任务是安装fabric的demo，遇到种种困难，解决如下：

<!--more-->

## 安装 golang
```
yum install golang
```
CentOS7.2 下默认安装的是 go_1.9.4

## 配置go环境变量
```
vi .bash_profile
export GOROOT=/usr/lib/golang/
export GOPATH=$HOME/go
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOROOT/bin:$GOBIN
```

## 编译demo
```
cd
mkdir -p go/src
mv 03.homework/ledger-reader/src/ledger go/src
cd go/src/ledger
go get
下载依赖包较慢，等10分钟左右
go build
```
出现错误:
```
# github.com/hyperledger/fabric/vendor/github.com/docker/docker/pkg/archive
vendor/github.com/docker/docker/pkg/archive/archive.go:364:5: hdr.Format undefined (type *tar.Header has no field or method Format)
vendor/github.com/docker/docker/pkg/archive/archive.go:364:15: undefined: tar.FormatPAX
vendor/github.com/docker/docker/pkg/archive/archive.go:1166:7: hdr.Format undefined (type *tar.Header has no field or method Format)
vendor/github.com/docker/docker/pkg/archive/archive.go:1166:17: undefined: tar.FormatPAX
```
然后：
```
go get github.com/docker/docker/pkg/archive
```
后，仍然报相同错误。fabric要求go版本大于等于1.10

## 在CentOS7 上从源码安装Go 1.10
```
cd /usr/lib/golang/
rm -rf *
wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
tar zxvf go1.10.linux-amd64.tar.gz
cd go
mv * /usr/lib/golang/
```

## 重新编译demo
```
cd
mkdir -p go/src
mv 03.homework/ledger-reader/src/ledger go/src
cd go/src/ledger
go get
下载依赖包较慢，等10分钟左右
go build
```

## 运行demo
```
./ledger
```
显示：
```
Usage of ./ledger:
  -channelId string
    	enter a channel id here
  -httptest.serve string
    	if non-empty, httptest.NewServer serves on this address and blocks
  -name string
    	enter your name
  -test.bench regexp
    	run only benchmarks matching regexp
```

加入参数执行
```
./ledger --name thomaszh9 --channelId testchannel
```
执行结果：
```
Welcome  thomaszh9
2018-08-31 05:39:54.774 EDT [bccsp] GetDefault -> WARN 001 Before using BCCSP, please call InitFactories(). Falling back to bootBCCSP.
2018-08-31 05:39:54.774 EDT [bccsp] GetDefault -> WARN 002 Before using BCCSP, please call InitFactories(). Falling back to bootBCCSP.
Channel Id:  testchannel
Current block number:  0
Block timestamp:  seconds:1535708394
Previous block hash is:  []
Block data hash is:  [191 135 241 74 100 238 6 83 86 96 12 59 8 86 165 1 163 96 37 143 8 211 59 124 92 198 45 167 245 37 149 221]
Transaction ID:  5b00aa9777eba8ad034a27ba94826f8d36eb30ff44d3a7d9db876d47281f4e43
Transaction type:  CONFIG
```
完美！

## 参考资料
* [fabric v1.2.0 运行e2e例子](https://blog.csdn.net/btqszl/article/details/80966455)
* [在CentOS 6.9上从源码安装Go 1.10](https://blog.csdn.net/tao_627/article/details/79585133)

<!--more-->
