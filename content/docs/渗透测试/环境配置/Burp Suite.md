---
title: 'Burp Suite'
date: '2025-04-11T19:06:08+08:00'
weight: 3
draft: false
description: ""
---

### 一、Java 环境的配置

> [!NOTE]
> Burp Suite 是一个基于 Java 开发的应用程序，因此需要配置 Java 环境来执行其代码。

下载链接：[Oracle Java](https://www.oracle.com/cn/java/technologies/downloads/)

- 下载 Windows 版本的 JDK
    
  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image.9nzz4z8iki.webp)

- 将 JDK 的压缩包解压到任意指定的目录（以 D:\Program Files\Java 为例）

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744372720740.4joafpmxa8.webp)

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744372788610.3gol4tseha.webp)

- 配置环境变量

  - 新建系统变量 `JAVA_HOME` （以 D:\Program Files\Java\jdk-24 为例） 

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744373044169.77dqq2mugh.webp)

  - 编辑系统环境变量 Path，添加 `%JAVA_HOME%\bin`

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744373101150.45vagilgq.webp)

- 验证 Java 环境
  - `java -version` 检查 Java 版本
  - `javac -version` 检查 Java 编译器版本

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744376999793.6m433u99tk.webp)

### 二、Burp Suite 的安装

下载链接：[Burp Suite](https://portswigger.net/burp/releases)

- 下载 JAR 版本的 Burp Suite

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744378175830.51ec4dx3mn.webp)

- 下载 BurpLoaderKeygen

  > [!WARNING]
  > 由于涉及到版权问题，该软件请在 Github 或 Bing 等网站自行搜索下载，仅供学习和交流使用，商业使用请购买正版软件！

- 将下载的 Burp Suite 和 BurpLoaderKeygen 放在任意指定的目录（以 D:\Program Files\burpsuite_pro 为例）

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744384976457.39ld9n27sq.webp)

- 启动 BurpLoaderKeygen 加载器（在 D:\Program Files\burpsuite_pro 目录）

  ```powershell
  java -jar BurpLoaderKeygen.jar
  ```

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744472686180.5mnzs91e6k.webp)

- 配置 BurpLoaderKeygen 加载器
  - 勾选 `Auto Run`，下次打开 BurpLoaderKeygen 时会自动运行 Burp Suite
  - 任意修改 `License Text`（可选，以 licensed to Hailo 为例）
  - 复制 License 的内容
  - 点击 Run 运行 Burp Suite

  ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385165274.5xatjzv94r.webp)

- 激活 Burp Suite

  - 点击 Next 跳过 License Agreement

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744382589175.b9365fhad.webp)

  - 将之前复制 License 的内容粘贴到 License Key

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385245782.5j4dt4my9p.webp)

  - 选择手动激活 Manual activation

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385266254.3k872shfy6.webp)

  - 复制 request

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385287177.8z6pl7wlbz.webp)

  - 粘贴到 BurpLoaderKeygen 的 Activation Request 并复制 Activation Response

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385313072.9gwr9sxyww.webp)

  - 粘贴到 Burp Suite 的 response

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744389817132.102cq6qp1a.webp)

  - 成功激活

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385332124.8adg1792bl.webp)

- 优化 Burp Suite 的启动 

  - 新建 `burpsuite.bat` 文件并放到 Burp Suite 的同级目录下

    ```shell
    java -jar "D:\Program Files\burpsuite_pro\BurpLoaderKeygen.jar"
    ```
    
    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744386453453.2yyjghmzna.webp)

  - 将 `D:\Program Files\burpsuite_pro` 添加到系统环境变量

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744386152557.1vyu5lr5rp.webp)

  - 在 cmd 中使用 `burpsuite` 命令运行 Burp Suite

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744386519216.60ufhpobuf.webp)

    ![](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/QQ_1744385371172.73u4slk5pw.webp)