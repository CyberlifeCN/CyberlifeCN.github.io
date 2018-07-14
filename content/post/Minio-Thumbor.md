---
title: "基于 Minio 和 Thumbor 搭建独立图片服务"
date: 2018-07-14T15:10:55+08:00
url: "2018/07/14/Minio-Thumbor"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/minio.jpg"
---

多数应用系统都会用到图片存储，从系统架构角度来说，像图片存储这样的服务应该尽量从核心业务中剥离出来。很多人会选择在线云存储服务，比如七牛云存储之类的。但是很多企业项目因为各种需求，还是会要求图片服务部署在内部。所以我们还是可能会需要一个可独立部署的图片服务。

自己开发实现一套图片存储服务系统，会花不少功夫，但如果有现成的方案何乐而不为呢？我在Github上发现Minio和Thumbor这两个项目可以帮我们达成需求。

<!--more-->
# Minio
Minio Cloud Storage是一个分布式对象存储系统。它是一个基于Go的开源项目，并且实现了Amazon S3的API。也就是说Minio相当于一个开源的Amazon S3。

使用docker安装可能是最省事的方式，两行命令即可安装下载并且运行起来。注意，这种启动模式仅限于测试环境下，一旦命令终止，数据将会消失。生产环境下的安装部署请参考文档。

下载、安装
```
wget https://dl.minio.io/server/minio/release/linux-amd64/minio
chmod +x minio
./minio server /mnt/cloudfs
```
启动之后，命令行中就会显示一堆系统信息。
```
Endpoint:  http://172.17.0.2:9000  http://127.0.0.1:9000
AccessKey: PT7TIDXEV7KH7S5R91JH
SecretKey: 17hdYH0mF2Ljd3ahZ7JGyi5l/ZPjunLlldUMY/A0
Region:    us-east-1
SQS ARNs:  <none>
```
之后就可以在浏览器中访问 http://127.0.0.1:9000 并且使用AccessKey和SecretKey登录。

其核心功能很简单，创建bucket，上传文件。其所有的文件链接都是含有过期时间的私有链接。然后我们就可以使用其提供的SDK使用了，就是这么简单。

minio专注于文件存储，并没有什么图片处理功能，但是我们可以使用thumbor这个服务来做图片处理。

# Thumbor
{{< gallery "/imgs/thumbor.png" >}}
Thumbor是一个非常强大的图片处理服务，可以实现图片裁剪、缩放、滤镜，甚至是人脸识别。

它是一个基于Python的开源项目，在python环境下可以通过pip安装。
```
pip install thumbor
```
然后使用下面命令即可运行。
```
thumbor-config > ./thumbor.conf # 生成配置文件
thumbor --port=8888 --conf=thumbor.conf
```
该服务运行在本地的8888端口，之后可以就可以直接通过url调用服务了。

比如这张图片 https://www.apple.com/cn/home/images/gallery/iphone_alt_small_2x.jpg

就可以使用 http://localhost:8888/unsafe/300x200/https://www.apple.com/cn/home/images/gallery/iphone_alt_small_2x.jpg 这个url使其缩放到300x200大小。

# Thumbor AWS
Thumbor处理图片很强大，但是只提供了很弱的图片存储功能。其图片上传修改接口没有做验证，所以默认情况下没有开启。但是Thumbor有一些社区支持，第三方开发者做了一些扩展。其中Thumbor AWS 这个扩展可以把Thumbor后端跟Amazon S3整合起来。在url上提交图片文件的key，Thumbor的后端会从Amazon S3中取出文件做处理。而之前Minio兼容Amazon S3的Api。所以意味着只需要做一点改动，就可以整合这两个服务。最终就可以达成，利用Minio上传存储图片，利用Thumbor取出图片做处理。

我们利用pip来安装这个扩展。
```
pip install tc_aws
```
Thumbor AWS这个扩展利用Boto3连接Amazon S3的SDK，根据Boto3文档中的配置，我们需要创建 ~/.aws/credentials 这个文件，并且填写刚才Minio提供的AccessKey和SecretKey，授权这个服务访问Minio。
```
[default]
aws_access_key_id = YOUR_ACCESS_KEY
aws_secret_access_key = YOUR_SECRET_KEY
```
之后在刚才利用thumbor_conf生成的thumbor.conf中修改一些配置。
```
TC_AWS_REGION='us-east-1' #填写minio提供的Region
TC_AWS_ENDPOINT='http://127.0.0.1:9000' #将默认的Amazon S3地址换成minio运行地址
LOADER = 'tc_aws.loaders.s3_loader' #将Thumbor的loader换成tc_aws.loaders.s3_loader
```
重新启动Thumbor，然后就可以在浏览器中通过Thumbor访问Minio中的图片了。

比如，在Minio的docker这个bucket中上传了一张aaa.jpg，然后我们就可以通过 http://localhost:8888/unsafe/docker/aaa.jpg 这个地址访问到这张图片的原图。当然，我们可以通过 http://localhost:8888/unsafe/300x200/docker/aaa.jpg 这种方式对这张图片进行300x200的缩放。

这样我们就大功告成了。

## Minio配置自启动

新建启动脚本文件 /etc/systemd/system/minio.service，内容如下：
```
[Unit]
Description=minio
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/bin/minio server /mnt/cloudfs
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 minio 服务
```
systemctl enable minio.service
systemctl start minio.service
systemctl status minio.service
systemctl daemon-reload
```

## Thumbor配置自启动

新建启动脚本文件 /etc/systemd/system/thumbor.service，内容如下：
```
[Unit]
Description=thumbor
[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/thumbor --port=8888 --conf=/home/thomas/thumbor.conf
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 thumbor 服务
```
systemctl enable thumbor.service
systemctl start thumbor.service
systemctl status thumbor.service
systemctl daemon-reload
```

## 反向代理frp内网主机配置
```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_386.tar.gz
tar -xvf frp_0.20.0_linux_386.tar.gz
cd frp_0.20.0_linux_386
```
修改 frpc.ini 文件，假设 frps 所在服务器的公网 IP 为 x.x.x.x，域名为yourdomain.com；
```
# frpc.ini
[common]
server_addr = x.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000

[web]
type = http
local_ip = 127.0.0.1
local_port = 8888
custom_domains = image.yourdomain.com
```
启动
```
./frpc -c ./frpc.ini
```

## 内网主机配置自启动

新建启动脚本文件 /etc/systemd/system/frpc.service，内容如下：
```
[Unit]
Description=frpc
[Service]
TimeoutStartSec=0
ExecStart=/home/thomas/frp_0.20.0_linux_386/frpc -c /home/thomas/frp_0.20.0_linux_386/frpc.ini
Restart=on-failure
RestartSec=5s
[Install]
WantedBy=multi-user.target
```
启动 frp 服务
```
systemctl enable frpc.service
systemctl start frpc.service
systemctl status frpc.service
systemctl daemon-reload
```

# 总结
各种服务都有其专精的一面，幸好Amazon S3在云服务API层面上形成了某种事实上的标准，所以这点能够帮助我们将一些服务整合在一起。

实现效果上来看，最终达成了图片存储和提取缩放需求，对于一些项目也足够使用。不过图片通过Thumbor处理后，其私有链接的属性也没了。这在一些要求高的企业服务项目中也是蛮尴尬的。
<!--more-->
