---
title: "在 CentOS7 中安装 Nginx"
date: 2018-09-26T09:51:35+08:00
url: "2018/09/26/centos7-nginx"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/nginx.png"
---

<!--more-->
## Nginx安装
### 添加RPM包进行安装
```
#添加Nginx包
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

#安装
sudo yum -y install nginx
```

### 启动Nginx并设置为开机启动
```
#启动服务
sudo systemctl start nginx
#（如果启动失败，可能是Apache等服务占用了80端口，关掉相应服务/修改端口即可）

#设置nginx开机启动
sudo systemctl enable nginx
```

### 开放端口
```
#开放80端口（nginx默认监听80端口）
firewall-cmd --add-port=80/tcp --permanent

#重载防火墙规则
firewall-cmd --reload
```

### 访问测试
浏览器输入 服务器IP，出现以下信息说明安装成功
{{< gallery "/imgs/nginx-welcome.png" >}}

## Nginx常用配置

### 配置文件说明
```
1、全局配置文件：/etc/nginx/nginx.conf
2、默认配置文件：/etc/nginx/conf.d/default.conf
```

### 新增配置目录
```
#1、新增配置文件夹
sudo mkdir /etc/nginx/server
#2、修改默认配置（加载该文件夹下的配置）
sudo vi /etc/nginx/nginx.conf
#3、在http属性下增加：
include /etc/nginx/server/*.conf;
```

### 反向代理配置
```
#1、新建/修改配置文件
sudo vi /etc/nginx/server/default.conf

#2、配置示例
server {
    listen       80;        #监听80端口
    server_name  ken.io.local; #监听的域名
    location / {            #转发或处理
        proxy_pass https://ken.io;
    }
    error_page   500 502 503 504  /50x.html;#错误页
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

### 负载均衡配置
```
upstream serverswitch {
    server 127.0.0.1:80;
    server 127.0.0.1:81;
}
server {
    listen       80;        #监听80端口
    server_name   ken.io.local; #监听的域名
    location / {            #转发或处理
        proxy_pass https://serverswitch;
    }
    error_page   500 502 503 504  /50x.html;#错误页
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

<!--more-->
