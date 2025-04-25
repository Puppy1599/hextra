---
title: 'GitHacker'
date: '2025-04-24T22:33:59+08:00'
weight: 
draft: false
description: 'GitHacker'
---

**官方仓库：**[GitHacker](https://github.com/WangYihang/GitHacker)

### 一、描述

这是一个多线程工具，用于利用 `.git` 文件夹泄露漏洞，它几乎能够完全下载目标 `.git` 文件夹，即使目标网站禁用了目录列出功能，该工具也能通过暴力破解常见的 `.git` 文件来工作。

通过 GitHacker，您可以查看开发者的提交历史，分支，...，暂存区，这有助于更好地理解目标代码库，甚至发现安全漏洞。

### 二、安装

```shell
python -m pip install -i https://pypi.org/simple/ GitHacker
```

### 三、用法

```text
usage: githacker [-h] (--url URL | --url-file URL_FILE) --output-folder OUTPUT_FOLDER [--brute]
                 [--enable-manually-check-dangerous-git-files] [--threads THREADS] [--delay DELAY] [--version]

GitHacker

options:    
  -h, --help            显示此帮助信息并退出
  --url URL             目标网站的 URL，该网站暴露了 .git 文件夹
  --url-file URL_FILE   包含目标网站 URL 列表的文件，这些网站暴露了 `.git` 文件夹
  --output-folder OUTPUT_FOLDER
                        本地文件夹，所有被利用的代码库将存储在该文件夹中，每个代码库将以 md5(url) 命名的文件夹存储
  --brute               启用暴力破解分支/标签名称
  --enable-manually-check-dangerous-git-files
                        启用手动检查可能导致远程代码执行（RCE）的危险 Git 文件（例如：.git/config，.git/hook/pre-commit）。如果启用此选项，GitHacker 将不会下载可能危险的文件。
  --threads THREADS     下载时使用的线程数
  --delay DELAY         HTTP 请求之间的延迟时间（单位：秒）
  --version             显示程序版本号并退出
```