---
title: "用Pytest+Allure生成漂亮的HTML图形化测试报告"
date: 2018-08-30T15:10:55+08:00
url: "2018/08/30/Pytest-Allure"
categories: ["programming"]
tags: ["python","测试"]
banner: "imgs/Pytest-Allure.jpeg"
---

Allure是一款非常轻量级并且非常灵活的开源测试报告生成框架。
它支持绝大多数测试框架， 例如TestNG、Pytest、JUint等。它简单易用，易于集成。

<!--more-->

## Pytest安装
Pytest是Python的单元测试框架，非常方便和易用。强烈推荐对于用Python进行测试工作的小伙伴使用这个测试框架，相比与Python自带的UnitTest好用太多太多。
```
pip install pytest
```

安装完成后，可以验证安装的版本：
```
py.test --version
```

<!--more-->
