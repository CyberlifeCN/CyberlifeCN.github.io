---
title: "Tornado 如何自动生成 Rest API 文档"
date: 2017-12-13T14:49:43+08:00
categories: ["programming"]
tags: ["python", "tornado", "swagger", "api", "doc"]
---

前一段时间学习使用Golang，使用了beego创建了第一个apiserver，发现beego集成了Swagger。
Swagger 是一个规范和完整的框架，用于生成、描写叙述、调用和可视化 RESTful 风格的 Web 服务。整体目标是使client和文件系统作为服务器以相同的速度来更新。
我们使用python开发程序已经有两年多，一时无法切换到Golang，那python下是否有Swagger的插件呢？

<!--more-->

用 tornado swagger 在github上搜索，发现了tornado-swagger
### 下载
    $ git clone https://github.com/SerenaFeng/tornado-swagger.git

### 安装
    $ sudo python setup.py install

### 程序中引用tornado-swagger
    from tornado_swagger import swagger

### 在原代码中使用注释@swagger
    # /api/auth/codes
    class AuthCodeXHR(BaseHandler):
        @swagger.operation(nickname='post')
        def post(self):
            """
                @description: 获取用户认证使用的授权凭证 {code}

                @param body: {"appid":"bookpi",\
                    "app_secret":"HmnNYYXNKH"}
                @type body: L{json}
                @in body: body
                @required body: True

                @rtype: L{Resp}
                @notes: 后续所有 /api/auth 操作均需要使用通过此 API 获得的 code,\
                    Authorization="Bearer {code}" 放置在 http.request.headers 中\
                    系统将通过此凭证验证是合法授权的app在使用/api/auth操作 \
                    此凭证 30分钟内有效
            """

### 编写一个新文件swagger.py
```
# _*_ coding: utf-8_*_

import tornado.ioloop
from tornado.web import RequestHandler, HTTPError
from tornado_swagger import swagger

DEFAULT_REPRESENTATION = "application/json"
HTTP_BAD_REQUEST = 400
HTTP_FORBIDDEN = 403
HTTP_NOT_FOUND = 404

swagger.docs()

def make_app():
    return swagger.Application([
        # 获取用户认证使用的授权凭证{code}
        ('/api/auth/codes', getattr(api_auth, 'AuthCodeXHR')),
    ])


if __name__ == "__main__":
    app = make_app()
    app.listen(7117)
    tornado.ioloop.IOLoop.current().start()
```

### 运行
    $ python swagger.py

### 验证
    $ curl http://localhost:7117/swagger/spec

### 通过浏览器访问
http://localhost:7117/swagger/spec.html

### [Demo](http://docs.domicake.com/swagger/spec.html)
注意要点击【List Operations】菜单才能看到API列表

### 主要参考资料：
* [tornado-swagger] (https://github.com/SerenaFeng/tornado-swagger)

<!--more-->
