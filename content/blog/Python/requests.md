---
title: 'Requests'
date: '2025-04-23T15:04:24+08:00'
weight: 
draft: false
description: "Requests"
---

> [!NOTE]
> **官方文档：**[Requests](https://requests.readthedocs.io/en/latest/)

## Main Interface

可以通过以下方法访问请求的所有功能，它们都会返回一个 Response 对象

`requests.request(method, url, **kwargs)`：构造并发送请求

参数：
- method：新请求对象的方法（GET、OPTIONS、HEAD、POST、PUT、PATCH 或 DELETE）
- url：新请求对象的 URL
- params：(可选）字典，在请求的查询字符串中要发送的元组或字节列表
- data：(可选）要在请求正文中发送的字典、元组列表、字节或类文件对象
- json：(可选）在请求正文中发送的 JSON 可序列化 Python 对象
- headers：(可选）与请求一起发送的 HTTP 请求头信息字典
- cookies： (可选）与请求一起发送的 Dict 或 CookieJar 对象
- files：(可选）用于多部分编码上传的 `'name': file-like-objects`（或 `{'name': file-tuple}`）字典。`file-tuple` 可以是 2 元组 `('filename'、fileobj)`、3 元组 `('filename'、fileobj、'content_type')` 或 4 元组 `('filename'、fileobj、'content_type'、custom_headers)`，其中`content_type`是定义给定文件内容类型的字符串，`custom_headers` 是包含要为文件添加的附加标头的类字典对象
- timeout(float or tuple)： (可选）等待服务器发送数据多少秒后放弃，用浮点数或（连接超时、读取超时）元组表示。

## Request Sessions

`class requests.Session`（请求会话， 提供 cookie 持久性、连接池和配置）

**1、基础用法**

```python
import requests
session = requests.Session()
session.get('https://www.example.com')
```

**2、常用方法**

`get(url, **kwargs)`：发送 GET 请求，返回 Response 对象

参数：
- url：新请求对象的 URL
- **kwargs：`request` 接收的可选参数

`post(url, data=None, json=None, **kwargs)`：发送 POST 请求，返回 Response 对象

参数：
- url：新请求对象的 URL
- data：(可选）要在请求正文中发送的字典、元组列表、字节或类文件对象
- json：(可选）在请求正文中发送的 json
- **kwargs：`request` 接收的可选参数

`patch(url, data=None, **kwargs)`：发送 PATCH 请求，返回 Response 对象

参数：
- url：新请求对象的 URL
- data：(可选）要在请求正文中发送的字典、元组列表、字节或类文件对象
- **kwargs：`request` 接收的可选参数

