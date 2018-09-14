---
title: "使用 pip 升级 python 包"
date: 2018-09-14T18:29:35+08:00
url: "2018/09/14/pip-uninstall"
categories: ["deploy"]
tags: ["linux","python"]
banner: "imgs/pip.png"
---

使用pip安装了就版本的python包，如何升级到更新的版本

<!--more-->

## 安装 pip
```
$ wget https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py
```

## 尝试卸载 couchbase-0.11
```
sudo pip uninstall couchbase
```
提示如下错误
```
Cannot uninstall 'couchbase'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
```

## 尝试强行升级到 couchbase-2.4.0
```
sudo pip install --upgrade --force-reinstall couchbase==2.4.0
```
提示如下错误
```
Collecting couchbase==2.4.0
Installing collected packages: couchbase
  Found existing installation: couchbase 0.11
Cannot uninstall 'couchbase'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
```

## 解决办法
```
sudo pip install couchbase==2.4.0 --ignore-installed requests
```
<!--more-->
