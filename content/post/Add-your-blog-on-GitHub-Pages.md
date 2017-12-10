---
title: "使用hugo搭建个人博客站点"
date: 2017-12-10T19:59:43+08:00
draft: true
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

### 主要参考资料：
* [使用hugo搭建个人博客站点] (http://www.gohugo.org/post/coderzh-hugo/)

<!--more-->
