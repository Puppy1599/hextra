---
title: 'WSL2 安装 Kali Linux'
date: '2025-05-01T09:22:46+08:00'
weight: 
draft: false
description: 'WSL2 安装 Kali Linux'
---

## 一、启用 Windows 功能

- Hyper-V
- Virtual Machine Platform
- 适用于 Linux 的 Windows 子系统

## 二、安装 Kali Linux

查看可用发行版列表：`wsl --list --online`

```shell
C:\Users\puppy>wsl --list --online
以下是可安装的有效分发的列表。
使用 'wsl.exe --install <Distro>' 安装。

NAME                            FRIENDLY NAME
AlmaLinux-8                     AlmaLinux OS 8
AlmaLinux-9                     AlmaLinux OS 9
AlmaLinux-Kitten-10             AlmaLinux OS Kitten 10
Debian                          Debian GNU/Linux
FedoraLinux-42                  Fedora Linux 42
SUSE-Linux-Enterprise-15-SP5    SUSE Linux Enterprise 15 SP5
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
Ubuntu                          Ubuntu
Ubuntu-24.04                    Ubuntu 24.04 LTS
archlinux                       Arch Linux
kali-linux                      Kali Linux Rolling
openSUSE-Tumbleweed             openSUSE Tumbleweed
openSUSE-Leap-15.6              openSUSE Leap 15.6
Ubuntu-18.04                    Ubuntu 18.04 LTS
Ubuntu-20.04                    Ubuntu 20.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_7                 Oracle Linux 8.7
OracleLinux_9_1                 Oracle Linux 9.1
```

安装 kali-linux：`wsl --install -d kali-linux`

```shell
C:\Users\puppy>wsl --install kali-linux
正在下载: Kali Linux Rolling
正在安装: Kali Linux Rolling
已成功安装分发。它可通过 “wsl.exe -d kali-linux” 启动
```

启动 kali-linux：`wsl.exe -d kali-linux` 或直接在开始菜单打开 `kali-linux`

创建用户：输入用户名和密码

```shell
Waiting for systemd to start...
running
Please create a default Kali WSL user. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: hailo
New password:
Retype new password:
passwd: password updated successfully
┏━(Message from Kali developers)
┃
┃ This is a minimal installation of Kali Linux, you likely
┃ want to install supplementary tools. Learn how:
┃ ⇒ https://www.kali.org/docs/troubleshooting/common-minimum-setup/
┃
┗━(Run: “touch ~/.hushlogin” to hide this message)
┌──(hailo㉿HAILO-LAPTOP)-[~]
└─$
```

## 三、安装 GUI 桌面

Win-KeX 为 Windows Subsystem for Linux (WSL 2) 中的 Kali Linux 提供了 GUI 桌面体验

### 1、更改软件仓库镜像源

在终端中执行命令，使用 vim 编辑器编辑 `sources.list` 文件

```shell
sudo vim /etc/apt/sources.list
```

使用 `#` 注释 Kali 官方的镜像源并添加清华源

```text
# deb http://http.Kali.org/kali kali-rolling main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main non-free contrib non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main non-free contrib non-free-firmware
```

### 2、更新软件包列表

```shell
sudo apt update
```

### 3、安装 Win-KeX

```shell
sudo apt install -y kali-win-kex
```

## 四、启动 Kali Linux Win-KeX

以无缝模式启动具有声音支持的 Win-KeX，其它模式请参考[官方文档](https://www.kali.org/docs/wsl/win-kex/)

```shell
wsl -d kali-linux kex --sl -s
```