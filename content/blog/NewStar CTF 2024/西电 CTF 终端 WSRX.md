---
title: '西电 CTF 终端 WSRX'
date: '2025-04-19T22:43:05+08:00'
weight: 1
draft: false
description: "西电 CTF 终端 WSRX"
---

### 一、WSRX 的下载与安装

**下载地址：**[WebSocketReflectorX](https://github.com/XDSEC/WebSocketReflectorX)

### 二、添加到环境变量

```text
D:\Application\WebSocketReflectorX
```

### 三、编写 bat 脚本

以 `wsrx_ctf.bat` 为例，放在 `D:\Application\WebSocketReflectorX` 目录：

```shell
@echo off
chcp 65001
set /p wsrx=请输入 WSRX 地址：
echo link: 127.0.0.1:3307
wsrx.exe connect %wsrx% --host 127.0.0.1 --port 3307
```