---
title: "Restful API 测试工具"
date: 2018-06-20T10:07:35+08:00
url: "2018/06/20/api-test-tools"
categories: ["deploy"]
tags: ["linux","api"]
banner: "imgs/RestAssuredAPITesting.jpg"
---

随着我们转向更灵活的敏捷软件开发过程，如持续集成和交付，快速向开发人员提供测试反馈的需求正在增加。

UI测试的一个缺点是它们很慢，这使它们成为让开发人员快速知道他们的代码是否破坏了最新的构建的糟糕选择。另一方面，API测试往往比GUI测试更快、运行更可靠。

<!--more-->
# 什么是 API?
应用程序编程接口(API)是作为软件组件接口的规范。

虽然大多数功能测试都涉及到测试用户界面(如web页面或dot net表单)，但API测试涉及绕过用户界面，通过调用应用程序的API直接与应用程序通信。

API测试允许您测试诸如JMS HTTP、数据库和Web services之类的接口。

API测试有时被称为“无头”测试。大多数无头测试包括绕过UI，直接向应用程序的后端或服务发送请求，并在验证响应的同时接收响应，以确保事情按照我们的预期运行。

这个简单的例子通常被称为客户端/服务器关系。客户机通过请求资源发出请求;然后请求走出去并找到一个可以填充该请求的服务器。服务器定位所需的资源并向客户端发送响应。

# 什么测试工具能做自动化的 API 测试工具?
由于Selenium只是用于基于浏览器的测试，所以您可能想知道用于Rest和Soap web服务测试的工具是什么。

# RESTClient, a debugger for RESTful web services.
RESTClient已经完全重写，以与Firefox 57兼容。一些函数仍然缺失(OAuth & OAuth 2.0身份验证)。

{{< gallery "/imgs/RESTClient.png" >}}

RESTClient支持所有HTTP方法RFC2616 (HTTP/1.1)和RFC2518 (WebDAV)。您可以构造自定义HTTP请求(具有资源URI和HTTP请求体的自定义方法)，以直接针对服务器测试请求。

<!--more-->
