---
title: 'PHP 反序列化'
date: '2025-05-23T20:13:50+08:00'
weight: 
draft: false
description: 'PHP 反序列化'
---

## 一、靶场部署


**项目地址：[php_ser_Class](https://github.com/mcc0624/php_ser_Class)**

> [!NOTICE]
> 作者推荐使用 Docker 的方式部署环境，这里以 phpstudy 的方式部署

1、直接将项目文件复制到 phpstudy 的网站根目录

![PixPin_2025-05-04_11-23-57](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-04_11-23-57.5c16tsuidf.webp)

2、访问 php_ser_Class 靶场目录

![PixPin_2025-05-04_11-28-40](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-04_11-28-40.39le5r1khg.webp)

## 二、面向对向基本概念

* 面向过程

  面向过程是一种以**整体事件**为中心的编程思想，编程的时候把解决问题的步骤分析出来，然后用函数把这些步骤实现，在一步一步的具体步骤中再按顺序调用函数。

* 面向对象

  面向对象是一种以**对象**为中心的编程思想, 把要解决的问题分解成各个对象;对象是一个由信息及对信息进行处理的描述所组成的整体, 是对现实世界的抽象。

**对象的三个特征：对象的行为、对象的形态、对象的表示**

类的定义：类定义了一件事物的抽象特点，它将数据以及对这些数据的操作封装在一起。对象是具有类类型的变量，是对类的实例。

内部构成：成员属性 + 成员方法

* 成员属性：定义在类内部的变量。该变量的值对外是不可见的，但是可以通过成员函数访问，在类被实例化为对象后，该变量即可成为对象的属性。

* 成员方法：定义在类的内部，可用于访问对象的数据

继承：继承性是子类自动共享父类数据结构和方法的机制，是类之间的一种关系。在定义和实现一个类的时候，可以在一个已经存在的类的基础上进行，把一个己经存在的类所定义的内容作为自己的内容，并加入若干新的内容。

父类：一个类被其它类继承，可将该类称为父类、基类或超类。

子类：一个类继承其它类，可将该类称为子类或派生类。

## 三、类与对象

### 1、类的结构

**类：定义类名、成员属性、成员方法**

```php
<?php
class Class_Name{
// 成员属性声明
// 成员方法声明
}
```

### 2、类的内容

创建一个类：

```php
<?php
// 定义类 类名
class hero{
    // 声明成员属性
    var $name;
    var $sex;

    // 声明成员方法
    function jineng($var1){
        // 使用 $this 调用成员属性
        echo $this->name;
        echo $var1;
    }
}
```

### 3、实例化和赋值

```php
<?php
class hero{
    var $name;
    var $sex;

    function jineng($var1){
        echo $this->name;
        echo $var1;
    }
}

// 实例化类 hero 为 对象 cyj
$cyj = new hero();

// 参数赋值
$cyj->name = "chengyaojin";
$cyj->sex = "man";

// 调用函数
$cyj->jineng("zuofan");

// 打印对象 cyj
print_r($cyj);
```

运行结果：

```text

chengyaojin
zuofan
hero Object
(
    [name] => chengyaojin
    [sex] => man
)
```

### 4、类的修饰符介绍

在类中直接声明的变量你为成员属性（也可以称为成员变量）。

可以在类中声明多个变量，即对象中可以有多个成员属性，每个变量都存储了对象不同的属性信息。

访问权限修饰符：对属性的定义

* public：公共的，在类的内部、子类和类的外部都可以访问。

* protected：受保护的，在类的内部和子类中可以访问，但不能在类的外部访问。

* private：私有的，只能在类的内部访问，在类的外部和子类中都不能访问。

|  访问权限修饰符  | 类的内部 | 子类 | 类的外部 |
|:---------:|:----:|:--:|:----:|
|  public   |  √   | √  |  √   |
| protected |  √   | √  |  ×   |
|  private  |  √   | ×  |  ×   |

```php
<?php
class hero{
    public $name = "chengyaojin";
    private $sex = "man";
    protected $shengao = "165";

    function jineng($var1){
        echo $this->name;
        echo $var1;
    }
}

$cyj = new hero();
echo $cyj->name;
echo $cyj->sex;
echo $cyj->shengao;
```

运行结果：

```text
chengyaojin
Fatal error: Uncaught Error: Cannot access private property hero::$sex in D:\Workspace\php\new_star_ctf_2024\md5.php:15 Stack trace: #0 {main} thrown in D:\Workspace\php\new_star_ctf_2024\md5.php on line 15
```

```php
<?php
class hero{
    public $name = "chengyaojin";
    private $sex = "man";
    protected $shengao = "165";

    function jineng($var1){
        echo $this->name;
        echo $var1;
    }
}

class hero2 extends hero{
    function test(){
        echo $this->name;
        echo $this->sex;
        echo $this->shengao;
    }
}

$cyj = new hero();
$cyj2 = new hero2();
echo $cyj->name;
echo $cyj2->test();
```

运行结果：

```text
chengyaojin
chengyaojin
Warning: Undefined property: hero2::$sex in D:\Workspace\php\new_star_ctf_2024\md5.php on line 16
165
```

### 5、类与对象

* 类是对象的抽象，而对象是类的具体实例

* 类是想法，把类实例化（new），调用具体值后就变成了对象

### 6、类的成员方法

* 在类中定义的函数被称为成员方法

* 函数实现的是某个独立的功能

* 成员方法实现的是类中的一个行为，是类的一部分

* 可以在类中声明多个成员方法，成员方法的声明和函数声明完全一样，只不过在声明成员方法时可以在 function 关键字前加一些访问权限修饰符，如 public、protected、private（可以省略，默认为 public）

## 四、序列化基础知识

### 1、序列化的作用

**序列化（Serialization）** 是将对象的状态信息（属性）转换为可以存储或传输的形式的过程

**对象 ---序列化---> 字符串：** 将对象或数组转化为可存储/传输的字符串

### 2、表达方式

```php
<?php
$a = null;
echo serialize($a);
?>
```

所有格式第一位都是数据类型的英文字母的简写。

|  数据类型   |   原始数据   |    序列化字符串     |
|:-------:|:--------:|:-------------:|
|  NULL   |   null   |      N;       |
|   int   |   666    |    i:666;     |
| double  |   66.6   |    d:66.6;    |
| Boolean |   true   |     b:1;      |
| Boolean |  false   |     b:0;      |
| string  | "benben" | s:6:"benben"; |

数组的序列化：

```php
<?php
$a = array("benben", "dazhuang", "laoliu");
var_dump($a);
echo serialize($a);
```

运行结果：

```text
array(3) {
  [0]=>
  string(6) "benben"
  [1]=>
  string(8) "dazhuang"
  [2]=>
  string(6) "laoliu"
}
a:3:{i:0;s:6:"benben";i:1;s:8:"dazhuang";i:2;s:6:"laoliu";}
```

对象的序列化：

* 不能序列化类，只能序列化对象

* 只序列化成员变量，不序列化成员函数

```php
<?php
class test{
    public $pub = "benben";
    function jineng(){
        echo $this->pub;
    }
}

$a = new test();
echo serialize($a);
```

运行结果：

```text
O:4:"test":1:{s:3:"pub";s:6:"benben";}
```

私有属性（private）序列化时在变量名前添加 `%00Class_Name%00`

```php
<?php
class test{
    private $pub = "benben";
    function jineng(){
        echo $this->pub;
    }
}

$a = new test();
echo serialize($a);
echo urlencode(serialize($a));
```

运行结果：

```text
O:4:"test":1:{s:9:" test pub";s:6:"benben";}
O%3A4%3A%22test%22%3A1%3A%7Bs%3A9%3A%22%00test%00pub%22%3Bs%3A6%3A%22benben%22%3B%7D
```

受保护属性（protected）序列化时在变量名前添加 `%00*%00`

```php
<?php
class test{
    protected $pub = "benben";
    function jineng(){
        echo $this->pub;
    }
}

$a = new test();
echo serialize($a);
echo urlencode(serialize($a));
```

运行结果：

```text
O:4:"test":1:{s:6:" * pub";s:6:"benben";}
O%3A4%3A%22test%22%3A1%3A%7Bs%3A6%3A%22%00%2A%00pub%22%3Bs%3A6%3A%22benben%22%3B%7D
```

实例化后的对象作为成员属性

```php
<?php
class test{
    public $pub = "benben";
    function jineng(){
        echo $this->pub;
    }
}

class test2{
    public $ben;
    function __construct(){
        $this->ben = new test();
    }
}

$a = new test2();
echo serialize($a);
```

```text
<?php
class test{
    public $pub = "benben";
    function jineng(){
        echo $this->pub;
    }
}

class test2{
    public $ben;
}

$a = new test2();
$a->ben = new test();
echo serialize($a);
```

运行结果：

```text
O:5:"test2":1:{s:3:"ben";O:4:"test":1:{s:3:"pub";s:6:"benben";}}
```

## 五、反序列化知识

1. 反序列化之后的内容为一个对象；
2. 反序列化生成的对象的值，由反序列化里的值提供，与原有类与定义的值无关
3. 反序列化不触发类的成员方法，需要调用方法后才能触发

**反序列化的作用：** 将序列化后的参数还原成实例化的对象

对象 ---序列化---> 字符串

字符串 ---反序列化---> 对象

* 反序列化生成的对象里的值，由反序列化里的值提供，与原有类与定义的值无关
* 反序列化不改变类的成员方法，需要调用方法后才能触发

```php
<?php
class test{
    public $a = "benben";
    protected $b = 666;
    private $c = false;
    public function displayVar(){
        echo $this->a;
    }
}

$d = new test();
$d = serialize($d);
$a = urlencode($d);
echo $a;
$b = unserialize(urldecode($a));
var_dump($b);
```

运行结果：

```text
O%3A4%3A%22test%22%3A3%3A%7Bs%3A1%3A%22a%22%3Bs%3A6%3A%22benben%22%3Bs%3A4%3A%22%00%2A%00b%22%3Bi%3A666%3Bs%3A7%3A%22%00test%00c%22%3Bb%3A0%3B%7D
object(test)#1 (3) {
  ["a"]=>
  string(6) "benben"
  ["b":protected]=>
  int(666)
  ["c":"test":private]=>
  bool(false)
}
```

> [!NOTICE]
> `#1` 是这个对象在当前运行时环境中的唯一 ID 编号，是对象句柄 ID（object handle ID），它是 PHP 在输出调试信息时为了区分多个对象所生成的编号

## 六、反序列化漏洞利用例题

**反序列化漏洞的成因：**

* 反序列化过程中，unserialize() 接收的值（字符串）可控，通过更改这个值，得到所需的代码，即生成的对象的属性值

* 通过调用方法，触发代码执行

```php
<?php
class test{
    public $a = 'echo "this is test!!";';
    public function displayVar() {
        eval($this->a);
    }
}

$get = $_GET["benben"];
$b = unserialize($get);
$b->displayVar();
```

构造 payload：`O:4:"test":1:{s:1:"a";s:17:"system("whoami");";}`

```php
<?php
class test{
    public $a = 'system("whoami");';
}

$get = new test();

echo serialize($get);
```

运行结果：

```text
win-1iljbo80gej\administrator
```

## 七、魔术方法

**魔术方法：** 一个预定义好的，在特定情况下自动触发的行为方法

### 1、魔术方法的作用

魔术方法在特定条件下自动调用相关方法，最终导致触发代码

1. __construct()，类的构造函数
2. __destruct()，类的析构函数
3. __call()，在对象中调用一个不可以访问的方法
4. __callStatic()，用静态方式调用一个不可以访问的方法
5. __get()，访问一个不可以访问的类的成员属性时调用
6. __isset()，对不可访问属性调用 isset() 或 empty()
7. __set()，设置一个不可访问的类的成员属性
8. __unset()，对不可访问属性调用 unset()
9. __sleep()，执行 serialize() 时，会先调用这个函数
10. __wakeup()，执行 unserialize() 时，会先调用这个函数
11. __toString()，类被作为字符串
12. __invoke()，类被作为函数调用
13. __set_state()，调用 var_export() 导出类时，此静态方法被调用
14. __clone()，当对象调用 clone() 复制完成时
15. __autoload()，尝试加载未定义的类
16. __debugInfo()，打印所需调试信息

### 2、魔术方法相关机制

* 触发时机（动作不同，触发的魔术方法也不同）
* 功能
* 参数（一些特殊魔术方法需要传参）
* 返回值

### 3、__construct()

构造函数，在实例化一个对象的时候，首先会去自动执行的一个方法

**实例化对象时触发构造函数 __construct()**

```php
<?php
class User {
    public $username;
    public function __construct($username) {
        $this->username = $username;
        echo "触发了 1 次构造函数";
    }
}
$test = new User("benben");
$ser = serialize($test);
unserialize($ser);
```

运行结果：

```text
触发了 1 次构造函数
```

### 4、__destruct()

析构函数，在对象的所有引用被删除或当对象被显式销毁时执行的魔术方法

**实例化对象结束后，代码运行完会销毁，触发析构函数 __destruct()**

```php
<?php
class User {
    public function __destruct()
    {
        echo "触发了析构函数1次"."<br />" ;
    }
}
$test = new User("benben");
$ser = serialize($test);
unserialize($ser);
```

### 5、析构函数例题

```php
<?php
highlight_file(__FILE__);
error_reporting(0);
class User {
    var $cmd = "echo 'dazhuang666!!';" ;
    public function __destruct()
    {
        eval ($this->cmd);
    }
}
$ser = $_GET["benben"];
unserialize($ser);
```

构造 payload：`O:4:"User":1:{s:3:"cmd";s:17:"system('whoami');";}`

```php
<?php
class User {
    var $cmd = "system('whoami');";
}

$ser = new User();

echo serialize($ser);
```

运行结果：

```text
win-1iljbo80gej\administrator
```