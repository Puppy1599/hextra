---
title: '走入 Git'
date: '2025-05-19T18:48:31+08:00'
weight: 
draft: false
description: '走入 Git'
---

##### 1.Git介绍

> Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。
>
> Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。



- 版本控制

  版本控制（Revision control）是一种在开发的过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录，备份以便恢复以前的版本的软件工程技术。

- 团队协作

  从单兵作战转换为团队开发。

##### 2.Git对比SVN

![image-20220907110331660](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image-20220907110331660.1vyvjl7yrf.webp)

1. SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而工作的时候，用的都是自己的电脑，所以首先要从中央服务器得到最新的版本，然后工作，完成工作后，需要把自己的代码推送到中央服务器。集中式版本控制系统是必须联网才能工作。

2. Git是分布式版本控制系统，没有中央服务器，每个人的电脑就是一个完整的版本库，工作的时候不需要联网了，因为版本都在自己电脑上，可以离线工作。

##### 3.Git安装

> https://git-scm.com/

![image-20220907110802124](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image-20220907110802124.2rvd2zm6li.webp)

- 下载好以后，直接双击进行安装就行

- 一直下一步就可以，安装再默认路径就行

- 安装完毕后检测一下安装是否成功

    1. 方法一： 打开 `cmd` 窗口，输入指令检查

       ```shell
       # 检测 git 是否安装
       $ git --version
       ```

        - 出现版本号，说明安装成功

          ![PixPin_2025-05-19_18-01-40](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-19_18-01-40.9gwsrqknmd.webp)

    2. 方法二： 随便找个地方单机 **鼠标右键**，出现下图内容，说明安装成功

       ![PixPin_2025-05-19_18-03-09](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/PixPin_2025-05-19_18-03-09.491i4qtczq.webp)

- 安装完毕，接下来我们就可以开始使用了