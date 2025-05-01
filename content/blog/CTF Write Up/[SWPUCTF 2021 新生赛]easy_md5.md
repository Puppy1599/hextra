---
title: '[SWPUCTF 2021 新生赛]easy_md5'
date: '2025-05-01T09:00:50+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]easy_md5'
---

**绕过原理：[md5 与 sha1 绕过](../../php/md5-与-sha1-绕过)**

```php
<?php 
 highlight_file(__FILE__);
 include 'flag2.php';
 
if (isset($_GET['name']) && isset($_POST['password'])){
    $name = $_GET['name'];
    $password = $_POST['password'];
    if ($name != $password && md5($name) == md5($password)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
 
}
else {
    echo 'wrong!';
}
?>
```
**方法：数组比较**

![PixPin_2025-05-01_19-56-18](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-01_19-56-18.4job8acsk1.webp)

获得 flag：`NSSCTF{f112765f-2a34-48ee-b7c6-f18f7cc42671}`