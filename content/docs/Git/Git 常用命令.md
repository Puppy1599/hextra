---
title: 'Git 常用命令'
date: '2025-05-19T18:49:54+08:00'
weight: 
draft: false
description: 'Git 常用命令'
---

##### 1.设置用户签名

> 签名的作用就是用来标识用户，以区分不同的开发人员。

```shell
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

##### 2.初始化本地库

- 我们希望一个文件夹被 `git` 管理的话，那么就要在一个文件夹下进行 **git 初始化**

- 找到一个希望被 `git` 管理的文件夹

- 在文件夹内单击鼠标右键，点开 `Git Bash Here`

- 输入指令

  ```shell
  # git 初始化的指令
  $ git init
  ```

- 然后文件夹内会多一个 `.git` 的文件夹（这个文件夹是一个隐藏文件夹）

- 这个时候，我的这个 `git_demo` 文件夹就被 `git` 管理了

    - `git` 不光管理这一个文件夹，包括所有的子文件夹和子文件都会被管理

- 注意： **只有当一个文件夹被 git 管理以后，我们才可以使用 git 的功能去做版本管理**

    - 也就是说，我们必须要把我们电脑中的某一个文件夹授权给 `git`
    - `git` 才能对这个文件夹里面的内容进行各种操作
    - 而 `git init` 就是在进行这个授权的操作



##### 3.Git工作区、暂存区和版本库

![image-20220907112424167](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image-20220907112424167.8ojx61ym41.webp)

**托管平台**

- 局域网(内网)
    - gitlab
- 公网（外网）
    - gitlab
    - github
    - gitee 码云

##### 4.git add

- 我们要放入暂存区，要使用 `git add` 指令

- 把单独一个文件放在暂存区

  ```shell
  # 把文件夹下的 index.txt 文本放在暂存区
  $ git add index.txt
  ```

- 把单独一个文件夹放在暂存区（暂存区不能存放空文件夹）

  ```shell
  # 把文件夹下的 ceshi文件夹 放在暂存区
  $ git add ceshi/
  ```

- 把所有文件都放在暂存区

  ```shell
  # 把文件夹下所有的内容都放在暂存区
  $ git add --all
  
  # git add --all 有一个简单的写法
  $ git add .
  ```

    - 全部存放的时候使用上面两个指令哪个都行

##### 5.git commit

```shell
# 把暂存区的内容放到历史区
$ git commit -m "我是第一个版本"
```

我们使用 `git log` 这个指令查看版本信息

```shell
# 查看当前历史区版本信息
$ git log
```

- 我们使用 `git reset --hard 版本编号` 进行历史回退

  ```shell
  # 回退到上一次提交的版本
  $ git reset --hard HEAD^
  
  # 回退到上上次提交的版本
  $ git reset --hard HEAD^^
  $ git reset --hard HEAD~2
  ```

![image-20220908074740564](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image-20220908074740564.3yeo7n6zqp.webp)

##### 6.git revert 与 git reset

![image-20220908075826032](https://Puppy1599.github.io/picx-images-hosting/Typora/networkSecurity/image-20220908075826032.83a9jr45te.webp)

- git reset 是回滚到对应的commit-id，相当于是删除了commit-id以后的所有的提交，并且不会产生新的commit-id记录，如果要推送到远程服务器的话，需要强制推送-f
- git revert 是反做撤销其中的commit-id，然后重新生成一个commit-id。本身不会对其他的提交commit-id产生影响，如果要推送到远程服务器的话，就是普通的操作git push就好了