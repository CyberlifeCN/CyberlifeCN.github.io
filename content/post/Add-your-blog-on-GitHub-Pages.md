---
title: "使用hugo搭建个人博客站点"
date: 2017-12-10T19:59:43+08:00
url: "2017/12/10/add-your-blog-on-github-pages"
categories: ["deploy"]
tags: ["golang", "github"]
banner: "imgs/hugo-theme.png"
---

Hugo是一个强大的静态博客生成器，由spf13使用Golang开发。在性能和生成文章的速度上都不错。目前的版本是0.31.1，依然没有迈出1.0的大版本号。开始用Hugo进行博客写作了。


<!--more-->

### 安装golang
    $ sudo yum install go

### 编辑环境变量
    $ vi ~/.bashrc
    export GOROOT=/usr/lib/golang
    export GOPATH=$HOME/go
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin

### 安装 hugo
    $ go get -u -v "github.com/gohugoio/hugo"

### 生成自己的站点：
    $ hugo new site mysite

### 下载主题样式
```
$ cd themes
$ git clone https://github.com/digitalcraftsman/hugo-icarus-theme.git
$ cp -R themes/hugo-icarus-theme/exampleSite/* .
$ vi config.toml
# Comment the themesDir option if you use this theme in production
#themesDir = "../.."
```

### 启动
    # 本地测试启动脚本
    $ hugo server --theme=hugo-icarus-theme --buildDrafts --watch
    # 实际部署环境启动脚本
    $ hugo server --watch --baseUrl=http://cyber-life.cn/ --theme=hugo-icarus-theme --buildDrafts --port=1313 --appendPort=false

### 访问 
    http://localhost:1313/

### 配置hugo自启动

新建启动脚本文件 /etc/systemd/system/hugo.service，内容如下：
```
[Unit]
Description=hugo
[Service]
TimeoutStartSec=0
WorkingDirectory=/home/thomas/go/src/CyberlifeCN.github.io
ExecStart=/home/thomas/go/bin/hugo server --baseUrl=http://cyber-life.cn --theme=hugo-icarus-theme --buildDrafts --port=1313 --appendPort=false --disableLiveReload=true
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 hugo 服务
```
systemctl daemon-reload
systemctl enable hugo.service
systemctl start hugo.service
systemctl status hugo.service
```

### 主要参考资料：
* [使用hugo搭建个人博客站点] (http://www.gohugo.org/post/coderzh-hugo/)

<!--more-->
