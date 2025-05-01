---
title: 'Md5 与 Sha1 绕过'
date: '2025-05-01T12:04:46+08:00'
weight: 
draft: false
description: 'Md5 与 Sha1 绕过'
---

## 1、md5 宽松比较绕过

**方法：0e 比较**

以 `0e` 开头的字符串在 PHP 中会被视为浮点数 `0`（科学计数法），如果 `md5` 编码后的哈希值是以 `0e` 开头的，就会被 PHP 解析为 `0`，当两个不同的值经过 `md5` 编码后都以 `0e` 开头而解析为 `0` 时，`==` 就会认为相等

```php
<?php

highlight_file(__FILE__);
 
$flag = 'flag{this is a flag}';
 
if (isset($_GET['a']) && isset($_GET['b'])){
    $a = $_GET['a'];
    $b = $_GET['b'];
    if ($a != $b && md5($a) == md5($b)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
}

?>
```

`md5` 常见的以 `0e` 开头的值：

|    字符串    |              md5 值               |
|:---------:|:--------------------------------:|
| 240610708 | 0e462097431906509019562988736854 |
| 314282422 | 0e990995504821699494520356953734 |
| 571579406 | 0e972379832854295224118025748221 |
| 903251147 | 0e174510503823932942361353209384 |
|  QLTHNDT  | 0e405967825401955372549139051580 |
|  QNKCDZO  | 0e830400451993494058024219903391 |
|  EEIZDOI  | 0e782601363539291779881938479162 |
|  TUFEPMC  | 0e839407194569345277863905212547 |

## 2、md5 严格比较绕过

> [!ERROR]
> 在宽松比较（==）时，PHP 会自动进行类型转换，而严格比较（===）不会进行任何类型转换，即 `0e` 开头的字符串不会被以科学计数法解析为 `0`，PHP 会直接比较字符串是否相同，因此无法通过 **0e 比较** 的方式绕过

**方法：数组比较（PHP < 8.0.0）**

在 PHP 中，`md5()` 和 `sha1()` 等哈希函数是专门设计来处理字符串的，传入数组时会报错并返回 `NULL`，当两个不同的数组经过 `md5` 编码后会报错但都会返回 `NULL`，`===` 就会认为相等

```text
Warning: md5() expects parameter 1 to be string, array given in C:\phpStudy\WWW\php\md5\md5_2.php on line 10
```

```php
<?php

highlight_file(__FILE__);
 
$flag = 'flag{this is a flag}';
 
if (isset($_GET['a']) && isset($_GET['b'])){
    $a = $_GET['a'];
    $b = $_GET['b'];
    if ($a != $b && md5($a) === md5($b)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
}

?>
```

## 3、md5 字符串比较绕过

> [!ERROR]
> 当只能传入字符串或 PHP >= 8.0.0 时，无法通过 **0e 比较** 和 **数组比较** 的方式绕过

**方法：哈希碰撞**

生成两个 `md5` 相同但内容完全不一样的字符串

```php
<?php

highlight_file(__FILE__);
 
$flag = 'flag{this is a flag}';
 
if (isset($_GET['a']) && isset($_GET['b'])){
    $a = $_GET['a'];
    $b = $_GET['b'];
    if ((string)$a != (string)$b && md5($a) === md5($b)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
}

?>
```

![PixPin_2025-05-01_19-26-05](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-01_19-26-05.6m43wb8klt.webp)

1、使用 `fastcoll` 生成两个 `md5` 相同但内容完全不一样的文件

```shell
C:\Users\puppy\Desktop\fastcoll>fastcoll_v1.0.0.5.exe -p input.txt -o output1.txt output2.txt
MD5 collision generator v1.5
by Marc Stevens (http://www.win.tue.nl/hashclash/)

Using output filenames: 'output1.txt' and 'output2.txt'
Using prefixfile: 'input.txt'
Using initial value: afb4f79efa0c73c3ff9cc897951da3fe

Generating first block: .....
Generating second block: S11...............
Running time: 1.239 s
```

2、以十六进制查看两个文件的内容

```shell
┌──(hailo㉿HAILO-LAPTOP)-[~/md5collision]
└─$ xxd output1.txt
00000000: 6861 696c 6f00 0000 0000 0000 0000 0000  hailo...........
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000040: 1aae 2ab5 f640 bc61 9d96 15e7 6463 9e2e  ..*..@.a....dc..
00000050: 6eb8 493f 1bc4 a5e7 2a3a 7acd 138a b7e2  n.I?....*:z.....
00000060: 70e5 f2c9 ccab ef35 c73b 6979 df5b 552a  p......5.;iy.[U*
00000070: a8bc 9c5d 63fc ed67 ef6f b38b 2d7d a14a  ...]c..g.o..-}.J
00000080: bb02 a7aa 8e2c 9e27 814d 73bc c33e d457  .....,.'.Ms..>.W
00000090: a04a a2f5 11ba 6022 d5b7 f7cc 5029 5d22  .J....`"....P)]"
000000a0: b586 dff6 3c29 9f36 87a3 a4ef e615 76e6  ....<).6......v.
000000b0: bcae 7117 662b b617 314e e86c f425 3e4a  ..q.f+..1N.l.%>J

┌──(hailo㉿HAILO-LAPTOP)-[~/md5collision]
└─$ xxd output2.txt
00000000: 6861 696c 6f00 0000 0000 0000 0000 0000  hailo...........
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000040: 1aae 2ab5 f640 bc61 9d96 15e7 6463 9e2e  ..*..@.a....dc..
00000050: 6eb8 49bf 1bc4 a5e7 2a3a 7acd 138a b7e2  n.I.....*:z.....
00000060: 70e5 f2c9 ccab ef35 c73b 6979 dfdb 552a  p......5.;iy..U*
00000070: a8bc 9c5d 63fc ed67 ef6f b30b 2d7d a14a  ...]c..g.o..-}.J
00000080: bb02 a7aa 8e2c 9e27 814d 73bc c33e d457  .....,.'.Ms..>.W
00000090: a04a a275 11ba 6022 d5b7 f7cc 5029 5d22  .J.u..`"....P)]"
000000a0: b586 dff6 3c29 9f36 87a3 a4ef e695 75e6  ....<).6......u.
000000b0: bcae 7117 662b b617 314e e8ec f425 3e4a  ..q.f+..1N...%>J
```

3、对两个文件内容进行 `url` 编码

```shell
PS C:\Users\puppy\Desktop> python file_md5_url_encode.py "C:\Users\puppy\Desktop\fastcoll\output1.txt"
文件的 MD5 值是: 7b31d56dd86166359ed55e1c3e4809ff
文件的 URL 编码内容是：
hailo%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%17%D9%D41%96%E1%A5%7F%10W%ABt%23%A6~%05%94%C6%15OP%19%FAz%3C%1E%406%E8%3B%3D%FD%E5%F1%F9%BE%80%E4A%A6k%FC%F1%F4%CE%AD+%CE%1BK%CC%7D%87%BB%85%CF%E6AN%5B%EB%D6%F5Z1m9X%86%CFa%2B%B0%03%7C%FF%FD%C8e%F4El6%40%27%CD%99%AAt-%04LO%60%CDA-%C2%C8%A9%02%CC%AB6%EB%96%EB%D0%1B%7CJ%E1%A2%8D%9D%DF%D4~%F2%1F%84j%FF-%AF8q%40
PS C:\Users\puppy\Desktop> python file_md5_url_encode.py "C:\Users\puppy\Desktop\fastcoll\output2.txt"
文件的 MD5 值是: 7b31d56dd86166359ed55e1c3e4809ff
文件的 URL 编码内容是：
hailo%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%17%D9%D41%96%E1%A5%7F%10W%ABt%23%A6~%05%94%C6%15%CFP%19%FAz%3C%1E%406%E8%3B%3D%FD%E5%F1%F9%BE%80%E4A%A6k%FC%F1%F4%CE-%21%CE%1BK%CC%7D%87%BB%85%CF%E6AN%DB%EB%D6%F5Z1m9X%86%CFa%2B%B0%03%7C%FF%FD%C8e%F4El6%C0%27%CD%99%AAt-%04LO%60%CDA-%C2%C8%A9%02%CC%AB6%EB%96%EB%D0%1B%FCI%E1%A2%8D%9D%DF%D4~%F2%1F%84j%FF%AD%AF8q%40
```

```text {filename="str1"}
hailo%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%17%D9%D41%96%E1%A5%7F%10W%ABt%23%A6~%05%94%C6%15OP%19%FAz%3C%1E%406%E8%3B%3D%FD%E5%F1%F9%BE%80%E4A%A6k%FC%F1%F4%CE%AD+%CE%1BK%CC%7D%87%BB%85%CF%E6AN%5B%EB%D6%F5Z1m9X%86%CFa%2B%B0%03%7C%FF%FD%C8e%F4El6%40%27%CD%99%AAt-%04LO%60%CDA-%C2%C8%A9%02%CC%AB6%EB%96%EB%D0%1B%7CJ%E1%A2%8D%9D%DF%D4~%F2%1F%84j%FF-%AF8q%40
```

```text {filename="str2"}
hailo%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%17%D9%D41%96%E1%A5%7F%10W%ABt%23%A6~%05%94%C6%15%CFP%19%FAz%3C%1E%406%E8%3B%3D%FD%E5%F1%F9%BE%80%E4A%A6k%FC%F1%F4%CE-%21%CE%1BK%CC%7D%87%BB%85%CF%E6AN%DB%EB%D6%F5Z1m9X%86%CFa%2B%B0%03%7C%FF%FD%C8e%F4El6%C0%27%CD%99%AAt-%04LO%60%CDA-%C2%C8%A9%02%CC%AB6%EB%96%EB%D0%1B%FCI%E1%A2%8D%9D%DF%D4~%F2%1F%84j%FF%AD%AF8q%40
```

## 4、sha1 数组比较绕过

**方法：数组比较**

在 PHP 中，`md5()` 和 `sha1()` 等哈希函数是专门设计来处理字符串的，传入数组时会报错并返回 `NULL`，当两个不同的数组经过 `md5` 或 `sha1` 编码后会报错但都会返回 `NULL`，`===` 就会认为相等

```php
<?php

highlight_file(__FILE__);
 
$flag = 'flag{this is a flag}';
 
if (isset($_GET['a']) && isset($_GET['b'])){
    $a = $_GET['a'];
    $b = $_GET['b'];
    if ($a != $b && md5($a) === sha1($b)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
}

?>
```

## 5、总结

|     类型      |       方法        |
|:-----------:|:---------------:|
| md5 宽松比较绕过  | 0e 比较、数组比较、哈希碰撞 |
| md5 严格比较绕过  |    数组比较、哈希碰撞    |
| md5 字符串比较绕过 |      哈希碰撞       |
| sha1 数组比较绕过 |      数组比较       |

