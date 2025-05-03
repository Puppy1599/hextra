---
title: '[SWPUCTF 2021 新生赛]easyupload1'
date: '2025-05-02T21:18:24+08:00'
weight: 
draft: false
description: '[SWPUCTF 2021 新生赛]easyupload1'
---

上传 `fish1.php` 失败，但可以抓到包，判断为 **服务端检查**

![PixPin_2025-04-29_20-28-41](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-28-41.sz5k7xwjn.webp)上传非图片内容、图片后缀的文件 `fish.jpg` 成功，判断为 **检查后缀**

```text
./upload/fish.jpg succesfully uploaded!
```

上传任意后缀文件 `fish.fish` 失败，判断为 **白名单**

```text
想啥呢！
```

尝试 **MIME 绕过**，上传 `fish1.php` 成功

![PixPin_2025-04-29_20-34-45](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-34-45.26loo9grmb.webp)

```text
./upload/fish1.php succesfully uploaded!
```

使用蚁剑进行连接

![PixPin_2025-04-29_20-37-49](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-37-49.8adgqpnhk5.webp)

`find / -iname flag` 没有找到文件，新建 `phpinfo.php` 找到了 `flag`

![PixPin_2025-04-29_20-49-43](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-49-43.lvxot31d7.webp)

![PixPin_2025-04-29_20-50-56](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-04-29_20-50-56.6f0vy3rrwi.webp)

