---
title: "漂亮的API文档发布主题"
date: 2019-04-25T11:39:35+08:00
url: "2019/04/25/docuapi"
categories: ["deploy"]
tags: ["linux","golang","ui"]
banner: "imgs/docuapi.png"
---

Beautiful multilingual API documentation theme for Hugo

<!--more-->


### 安装golang
```
sudo yum install go
```

### 编辑环境变量
```
$ vi ~/.bashrc
export GOROOT=/usr/lib/golang
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

### 安装 hugo
```
go get -u -v "github.com/gohugoio/hugo"
```

### 生成自己的站点：
```
hugo new site docuapi
```

### 下载主题样式
```
hugo new site docuapi
cd docuapi/theme
git clone git@github.com:bep/docuapi.git
cd ..
cp -R themes/docuapi/exampleSite/* .
$ vi config.toml
# Comment the themesDir option if you use this theme in production
#themesDir = "../.."
```

### 启动
```
# 本地测试启动脚本
hugo server --port=1314 --theme=docuapi --buildDrafts --watch
# 实际部署环境启动脚本
hugo server --watch --baseUrl=http://docuapi.cyber-life.cn/ --theme=docuapi --buildDrafts --port=1314 --appendPort=false
```

### 访问
```
http://localhost:1314/
```

### 配置hugo自启动

新建启动脚本文件 /etc/systemd/system/docuapi.service，内容如下：
```
[Unit]
Description=docuapi
[Service]
TimeoutStartSec=0
WorkingDirectory=/home/thomas/go/src/docuapi
ExecStart=/home/thomas/go/bin/hugo server --watch --baseUrl=http://docuapi.cyber-life.cn/ --theme=docuapi --buildDrafts --port=1314 --appendPort=false
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```

启动 hugo 服务
```
systemctl daemon-reload
systemctl enable docuapi.service
systemctl start docuapi.service
systemctl status docuapi.service
```


### 主要参考资料：
* [github项目] (https://github.com/bep/docuapi)
* [使用hugo搭建个人博客站点] (http://cyber-life.cn/2017/12/10/add-your-blog-on-github-pages/)

<!--more-->