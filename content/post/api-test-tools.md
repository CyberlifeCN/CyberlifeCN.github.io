---
title: "Restful API 测试工具"
date: 2018-06-20T10:07:35+08:00
url: "2018/06/20/api-test-tools"
categories: ["deploy"]
tags: ["linux"]
banner: "imgs/RestAssuredAPITesting.jpg"
---

As we move towards more Agile shift-left software development processes like continuous integration and delivery, the need to quickly give test feedback to our developers is increasing.

One downfall to UI tests is they are slow, making them a poor choice for letting developers know quickly if their code has broken the latest build or not. API tests, on the other hand, tend to be faster and run more reliably than GUI tests.

<!--more-->
# What is an API?
Application Programming Interfaces (API) is a specification that acts as an interface for software components.

While most functional testing involves testing a user interface like a web page or a dot net form, API testing involves bypassing a user interface and communicating directly with an application by making calls to its APIs.

API testing allows you to test headless technologies like JMS HTTP, databases and Web services.

API testing is sometimes called “headless” testing. Most headless testing consists of bypassing the UI and sending a request directly to an application’s backend or service and receiving a response while validating the response to ensure things are working as we expect them to.

This simple example is often referred to as a client/server relationship. A client makes a request by asking for a resource; the request then goes out and finds a server that can fill that request. The server locates the desired resource and sends a response back to the client.

# What API Testing Tools Can I Use to Automate API Testing?
Since Selenium is just for browser-based testing, you may be wondering which tool to use for Rest and Soap web service-based testing.

# RESTClient, a debugger for RESTful web services.
RESTClient has been completely rewritten for compatible with Firefox 57. Some functions are still missing (OAuth & OAuth 2.0 authentication).

{{< gallery "/imgs/RESTClient.png" >}}

RESTClient supports all HTTP methods RFC2616 (HTTP/1.1) and RFC2518 (WebDAV). You can construct custom HTTP request (custom method with resources URI and HTTP request Body) to directly test requests against a server.

<!--more-->
