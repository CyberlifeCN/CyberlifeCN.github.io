---
title: "ubuntu 16 挂载 exfat 格式的U盘"
date: 2018-07-10T10:25:35+08:00
url: "2018/06/19/ubuntu16-exfat"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/exFAT.png"
---

在ubuntu下，由于版权的原因，默认不支持exfat格式的u盘，不过可以很方便就能添加对exfat的支持

<!--more-->
### 直接运行下面的命令就可以了
```
sudo apt-get install exfat-utils
```

### 开机启动时自动 mount
```
sudo vi /etc/fstab
最后一行加入
/dev/sda1     /media/disk1     exfat   defaults    0 0
sudo mount -a
```

<!--more-->
