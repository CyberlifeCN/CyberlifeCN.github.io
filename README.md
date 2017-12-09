[![image](http://b2oks-cover.b0.upaiyun.com/default/cyberlife-logo.jpg)](http://cyber-life.cn)

# CyberlifeCN.github.io
Host on GitHub

## 怎样将域名绑定到 github pages 博客上
1. 创建仓库 CyberlifeCN.github.io
注意仓库名 github.io 的前缀必须是 GitHub 上的用户名
2. 添加CNAME文件
在仓库根目录下添加一个CNAM文件，没有后缀名，里面内容为你的域名(如:cyber-life.cn),不需要添加http/www等前缀。
3. ping cyberlifecn.github.io 记录IP地址

        $ ping cyberlifecn.github.io
        PING sni.github.map.fastly.net (151.101.41.147): 56 data bytes
        64 bytes from 151.101.41.147: icmp_seq=0 ttl=41 time=290.620 ms
        64 bytes from 151.101.41.147: icmp_seq=1 ttl=41 time=305.150 ms
        64 bytes from 151.101.41.147: icmp_seq=2 ttl=41 time=323.938 ms
        64 bytes from 151.101.41.147: icmp_seq=3 ttl=41 time=242.839 ms
        ^C
        --- sni.github.map.fastly.net ping statistics ---
        4 packets transmitted, 4 packets received, 0.0% packet loss
        round-trip min/avg/max/stddev = 232.187/279.957/323.938/32.578 ms
4. 向你的 DNS 配置中添加 2 条记录

        @          A             151.101.41.147
        www      CNAME           cyberlifecn.github.io

## 使用hugo搭建个人博客站点
### 安装golang
    # yum install go

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
    $ cd themes
    $ git clone 'https://github.com/digitalcraftsman/hugo-icarus-theme.git'
    
    $ cp -R themes/hugo-type-theme/exampleSite/* .
    $ vi config.toml 
    # Comment the themesDir option if you use this theme in production 
    #themesDir = "../.." 

### 启动
    $ hugo server --theme=hugo-icarus-theme --buildDrafts --watch

### 访问 
    http://localhost:1313/
