---
title: "git - webhook自动部署"
date: 2018-06-13T01:23:52+08:00
categories: ["deploy"]
tags: ["programming"]
banner: "imgs/git-workflow.png"
---

前段时间在自己的服务器上搭好了自己的blog，但是如果每次写完都要用一些文件传输的工具上传到自己的服务器就显得很低效是吧，也会降低自己写代码的动力。现在就借着自己的github，用一个webhook就可以自动将自己的github上的代码部署到web目录下了。那么这样我们只要push一下代码到master上就可以自动生效了，用浏览器重新访问下网站就可以看了勒( •̀ ω •́ )。

<!--more-->

{{< gallery "/imgs/git-webhook.png" >}}

## webhookit
一个极简的命令行版本的 git webhok，部署容易，非常简单就可以部署一个 webhook server。支持GitHub, GitLab, GitOsc, Gogs, Coding。Python 2 / 3 都支持。

### 1. 安装 webhookit
pip install webhookit

### 2. 初始化一个配置模版
webhookit_config > /home/thomas/webhook-configs/config4hustcc.py

### 3. 更新 config4hustcc.py 配置内容
vim config4hustcc.py

```
# -*- coding: utf-8 -*-

# Created on Mar-03-17 15:14:34
# @author: hustcc/webhookit

# This means:
# When get a webhook request from `repo_name` on branch `branch_name`,
# will exec SCRIPT on servers config in the array.
WEBHOOKIT_CONFIGURE = {
    # a web hook request can trigger multiple servers.
    'repo_name/branch_name': [{
        # if exec shell on local server, keep empty.
        'HOST': '',  # will exec shell on which server.
        'PORT': '',  # ssh port, default is 22.
        'USER': '',  # linux user name
        'PWD': '',  # user password or private key.

        # The webhook shell script path.
        'SCRIPT': '/home/hustcc/exec_hook_shell.sh'
    }, 
	...],
	...
}
```
Python 变量名 WEBHOOKIT_CONFIGURE 不要去修改。

每个 webhook 都用仓库的名字和分支名字 'repo_name/branch_name' 作为它的键值，每个 webhook 可以触发一组服务器，这些服务器的配置信息存储在一个数组中。

服务器可以是远程的服务器，也可以是本地机器，如果要触发本机的脚本运行，那么请保持 HOST, PORT, USER, PWD 这些配置为空，或者不存在这些键值。

### 4. 运行 http server
webhookit -c config4hustcc.py

### 5. 查看运行结果
然后在浏览器中打开 http://localhost:18340 就可以看到下面的一些信息了：

* webhook 执行的状态；
* webhook 的 URL 地址；
* webhook 的配置信息（隐藏私密信息）；


### 主要参考资料：
* [webhookit](https://github.com/hustcc/webhookit)
* [gitlab之webhook自动部署](https://www.jianshu.com/p/00bc0323e83f)

<!--more-->
