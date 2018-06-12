---
title: "git - webhook自动部署"
date: 2018-06-13T01:23:52+08:00
categories: ["deploy"]
tags: ["programming"]
banner: "imgs/git-workflow.png"
---

前段时间在自己的服务器上搭好了自己的blog，但是如果每次写完都要用一些文件传输的工具上传到自己的服务器就显得很低效是吧，也会降低自己写代码的动力。现在就借着自己的github，用一个webhook就可以自动将自己的github上的代码部署到web目录下了。那么这样我们只要push一下代码到master上就可以自动生效了，用浏览器重新访问下网站就可以看了勒( •̀ ω •́ )。

<!--more-->

{{< gallery "/imgs/git-operations.png" >}}

### 每次提交时的标准操作流程
```
git stash
git pull -p
git stash pop(may be conflict, please modify your code)
git add .
git commit -m "message"
git push
```

### 如果发现错误的将不想staging的文件add进入index之后，想回退取消，则可以使用命令
git reset HEAD^


### 主要参考资料：
* [git - 简易指南] (http://www.bootcss.com/p/git-guide/)
* [Git 使用规范流程] (http://www.ruanyifeng.com/blog/2015/08/git-use-process.html)

<!--more-->
