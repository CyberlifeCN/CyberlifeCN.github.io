---
title: "用Pytest+Allure生成漂亮的HTML图形化测试报告"
date: 2018-08-30T15:10:55+08:00
url: "2018/08/30/Pytest-Allure"
categories: ["programming"]
tags: ["python","测试"]
banner: "imgs/allure.png"
---

Allure是一款非常轻量级并且非常灵活的开源测试报告生成框架。
它支持绝大多数测试框架， 例如TestNG、Pytest、JUint等。它简单易用，易于集成。

<!--more-->

# python单元测试框架pytest
pytest是python的一种单元测试框架，与python自带的unittest测试框架类似，但是比unittest框架使用起来更简洁，效率更高。根据pytest的官方网站介绍，它具有如下特点：

* 非常容易上手，入门简单，文档丰富，文档中有很多实例可以参考
* 能够支持简单的单元测试和复杂的功能测试
* 支持参数化
* 执行测试过程中可以将某些测试跳过，或者对某些预期失败的case标记成失败
* 支持重复执行失败的case
* 支持运行由nose, unittest编写的测试case
* 具有很多第三方插件，并且可以自定义扩展
* 方便的和持续集成工具集成

### Pytest安装
与安装其他的python软件无异，直接使用pip安装。
```
pip install pytest
```

安装完成后，可以验证安装的版本：
```
py.test --version
```

### 一个实例
我们可以通过下面的实例，看看使用py.test进行测试是多么简单。

    # content of test_sample.py

    def func(x):
        return x+1

    def test_func():
        assert func(3) == 5

执行测试的时候，我们只需要在测试文件test_sample所在的目录下，运行py.test即可。pytest会在当前的目录下，寻找以test开头的文件（即测试文件），找到测试文件之后，进入到测试文件中寻找test_开头的测试函数并执行。
```
$ py.test
=========================== test session starts ============================
platform linux -- Python 3.4.1 -- py-1.4.27 -- pytest-2.7.1
rootdir: /tmp/doc-exec-101, inifile:
collected 1 items
test_sample.py F
================================= FAILURES =================================
_______________________________ test_answer ________________________________
def test_answer():
> assert func(3) == 5
E assert 4 == 5
E + where 4 = func(3)
test_sample.py:5: AssertionError
========================= 1 failed in 0.01 seconds =========================
```
通过上面的测试输出，我们可以看到该测试过程中，一个收集到了一个测试函数，测试结果是失败的（标记为F），并且在FAILURES部分输出了详细的错误信息，帮助我们分析测试原因，我们可以看到"assert func(3) == 5"这条语句出错了，错误的原因是func(3)=4，然后我们断言func(3) 等于 5。

### 如何编写pytest测试样例
通过上面2个实例，我们发现编写pytest测试样例非常简单，只需要按照下面的规则：

* 测试文件以test_开头（以_test结尾也可以）
* 测试类以Test开头，并且不能带有 __init__ 方法
* 测试函数以test_开头
* 断言使用基本的assert即可

### 如何执行pytest测试样例
执行测试样例的方法很多种，上面第一个实例是直接执行py.test，第二个实例是传递了测试文件给py.test。其实py.test有好多种方法执行测试：

    py.test               # run all tests below current dir
    py.test test_mod.py   # run tests in module
    py.test somepath      # run all tests below somepath
    py.test -k stringexpr # only run tests with names that match the
                          # the "string expression", e.g. "MyClass and not method"
                          # will select TestMyClass.test_something
                          # but not TestMyClass.test_method_simple
    py.test test_mod.py::test_func # only run tests that match the "node ID",
    			       # e.g "test_mod.py::test_func" will select
                                   # only test_func in test_mod.py
### 测试报告
pytest可以方便的生成测试报告，即可以生成HTML的测试报告，也可以生成XML格式的测试报告用来与持续集成工具集成。
生成HTML格式报告：
```
py.test --resultlog=path
```

# Allure测试报告

### 安装Allure Pytest Adaptor
Allure Pytest Adaptor是Pytest的一个插件，通过它我们可以生成Allure所需要的用于生成测试报告的数据。安装pytest-allure-adaptor插件方法：
```
$ pip install pytest-allure-adaptor
```

### 使用Allure Pytest Adaptor改造基于Pytest的测试用例
pytest-allure-adaptor官网中详细介绍了pytest-allure-adaptor所具有的功能。

为了使用Allure生成报告，需要在conftest.py和测试脚本中加入Allure特性。
首先，conftest.py中可以通过allure.environment方法将测试环境的信息输出到报告中，比如将测试时用的host和测试用的browser添加到测试报告中：
```
#!/usr/bin/env python
# coding=utf-8

import pytest
import allure
import yaml

@pytest.fixture(scope="session", autouse=True)
def env(request):
    """
    Parse env config info
    """
    root_dir = request.config.rootdir
    config_path = '{0}/config/env_config.yml'.format(root_dir)
    with open(config_path) as f:
        env_config = yaml.load(f) # 读取配置文件

    allure.environment(host=env_config['host']['domain']) # 测试报告中展示host
    allure.environment(browser=env_config['host']['browser']) # 测试报告中展示browser

    return env_config
```

接着，在测试脚本中，添加allure特性，直接看下面的脚本，我通过在脚本中添加注释的方式给大家解释allure特性的用途。
```
#!/usr/bin/env python
# coding=utf-8

import pytest
import allure


@allure.feature('购物车功能')  # feature定义功能
class TestShoppingTrolley(object):
    @allure.story('加入购物车')  # story定义用户场景
    def test_add_shopping_trolley(self):
        login('刘春明', '密码')  # 调用“步骤函数”
        with allure.step("浏览商品"):  # 将一个测试用例分成几个步骤，将步骤打印到测试报告中，步骤2
            allure.attach('商品1', '刘春明')  # attach可以打印一些附加信息
            allure.attach('商品2', 'liuchunming')
        with allure.step("点击商品"):  # 将一个测试用例分成几个步骤，将步骤打印到测试报告中，步骤3
            pass
        with allure.step("校验结果"):
            allure.attach('期望结果', '添加购物车成功')
            allure.attach('实际结果', '添加购物车失败')
            assert 'success' == 'failed'

    @allure.story('修改购物车')
    def test_edit_shopping_trolley(self):
        pass

    @pytest.mark.skipif(reason='本次不执行')
    @allure.story('删除购物车')
    def test_delete_shopping_trolley(self):
        pass


@allure.step('用户登录')  # 还可以将一个函数作为一个步骤，调用此函数时，报告中输出一个步骤，步骤名字通常是函数名，我把这样的函数叫“步骤函数”
def login(user, pwd):
    print(user, pwd)
```

上面使用了Allure的几个特性：

*    @allure.feature # 用于定义被测试的功能，被测产品的需求点
*    @allure.story # 用于定义被测功能的用户场景，即子功能点
*    with allure.step # 用于将一个测试用例，分成几个步骤在报告中输出
*    allure.attach # 用于向测试报告中输入一些附加的信息，通常是一些测试数据信息
*    @pytest.allure.step # 用于将一些通用的函数作为测试步骤输出到报告，调用此函数的地方会向报告中输出步骤

### 生成Allure测试报告
测试脚本中添加了Allure特性之后，在执行测试的时候需要先生成Allure报告所需要的测试结果数据。在py.test执行测试的时候，指定–alluredir选项及测试数据保存的目录即可：
```
$ py.test test/ --alluredir ./result/
```

./result/中保存了本次测试的结果数据。另外，还可以执行指定features或者stories执行一部分测试用例，比如执行‘购物车功能’下的‘加入购物车’子功能的测试用例：
```
$ py.test test/ --allure_features='购物车功能' --allure_stories='加入购物车'
```

### 生成测试报告
allure generate ./result/ -o ./report/ --clean

### 查看测试报告
用浏览器打开 ./report/index.html

### 安装Allure
```
wget https://bintray.com/qameta/generic/download_file?file_path=io%2Fqameta%2Fallure%2Fallure%2F2.7.0%2Fallure-2.7.0.tgz
tar -xvf Fallure-2.7.0.tgz
ln -s /home/thomas/sfw/allure-2.7.0/bin/allure ~/bin/allure
```

### 测试报告解读
打开生成的测试报告后，浏览器被自动调起，展示测试报告。下面我们分别看看测试报告的几个页面。

1.    首页
{{< gallery "/imgs/allure-01.png" >}}
首页中展示了本次测试的测试用例数量，成功用例、失败用例、跳过用例的比例，测试环境信息，SUITES，FEATURES BY STORIES等基本信息，当与Jenkins做了持续置成后，TREND区域还将显示，历次测试的通过情况。

首页的左边栏，还从不同的维度展示测试报告的其他信息，大家可以自己点进去看看。

2.    Behaviors
接下来，我们点击一下FEATURES BY STORIES，将进入Behaviors页面，这个页面按照FEATURES和 STORIES展示测试用例的执行结果：
{{< gallery "/imgs/allure-02.png" >}}
从这个页面可以看到“购物车功能”这个FEATURES包含的三个STORIES的测试用例执行情况。

3.    Suites
Allure测试报告将每一个测试脚本，作为一个Suite。在首页点击Suites区域下面的任何一条Suite，都将进入Suites页面。
{{< gallery "/imgs/allure-03.png" >}}
这个页面，将脚本的目录结果展示本次所有的测试用例执行情况。

4.    测试用例页面
在Suites页面上点击任何一条测试用例，Suites页面的右侧将展示这条用例的详细执行情况。
{{< gallery "/imgs/allure-04.png" >}}
从这个页面可以看到测试用例执行的每一个步骤，以及每个步骤的执行结果。测试用例为什么失败，这里一目了然。


<!--more-->
