---
title: 'Gitignore'
date: '2025-05-19T19:21:06+08:00'
weight: 
draft: false
description: 'Gitignore'
---

> 在一些项目中，我们不想让本地仓库的所有文件都上传到远程仓库中，而是有选择的上传，比如：一些依赖文件（node_modules下的依赖）、bin 目录下的文件、测试文件等。一方面将一些依赖、测试文件都上传到远程传输量很大，另一方面，一些文件对于你这边是可用的，在另一个人那可能就不可用了，比如：本地配置文件。



- 空行不匹配任何文件；

- 如果本地仓库文件已被跟踪，那么即使在 .gitignore 中设置了忽略，也不起作用。

- .gitignore 文件也会被上传的到远程仓库，所以，同一个仓库的人可以使用同一个.gitignore 文件。

```js
# See https://help.github.com/articles/ignoring-files/ for more about ignoring files.
# dependencies
# #开头的便是注释

# 忽略文件和目录
/node_modules
/.pnp
.pnp.js

# testing
/coverage

# production
/build

# misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local

# 使用通配符
npm-debug.log*
yarn-debug.log*
yarn-error.log*   

```