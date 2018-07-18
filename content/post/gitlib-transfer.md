---
title: "git 工程导入到新的 gitlab 服务器中"
date: 2018-07-18T12:49:43+08:00
url: "2018/07/18/git-transfer"
categories: ["deploy"]
tags: ["github"]
banner: "imgs/git-repository.jpg"
---

原有的 git 源代码要迁移到新到服务器上，操作如下:

<!--more-->
确保管理员所在机器的 ssh 公钥已经保存在 gitlab 网站上。

这样管理员可以在自己的机器上和 gitlab 系统交互。

1. git clone 到本地
2. 目标服务器先创建一个 git project
3. 本地 git 关联新 git   
```
git remote add new git@test.com:test/test.git
```
4. push 到新 git
```
git push -u new --all
git push -u new --tags
```

注意：需要先将远程的分支都“git checkout 分支名”一遍，然后再git push -u new --all,不然只有master

其实在gitlab上创建新的project后，会有相关操作：
```
Existing Git repository

cd existing_repo
git remote add origin git@gitlab.jianlc.tp:android/jlcbaselib.git
git push -u origin --all
git push -u origin --tags
```
<!--more-->
