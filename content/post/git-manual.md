---
title: "git - 实用手册"
date: 2018-06-12T22:58:43+08:00
url: "2018/06/12/git-manual"
categories: ["deploy"]
tags: ["git"]
banner: "imgs/git-workflow.png"
---

新入职几名员工，使用git时经常出错，发生冲突的现象时有发生，赶紧写一篇文档，固定操作流程，避免大家继续犯错。

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
