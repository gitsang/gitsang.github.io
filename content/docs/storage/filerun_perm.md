---
title: "filerun 权限问题"
description: ""
lead: ""
date: 2021-02-25T12:28:45+00:00
lastmod: 2021-02-25T12:28:51+00:00
draft: false
images: []
menu: 
  docs:
    parent: "storage"
weight: 1
toc: true
---

## filerun 的操作用户

filerun 在 linux 上操作的用户是 www-data，因此，若是通过 filerun 创建文件，那该文件的所有者和所有组应该都为 www-data。

（www-data 实际上并不属于 filerun，只是因为 filerun 是通过 apache2 或 nginx 搭建的，而 apache2 和 nginx 的默认用户是 www-data，实际上几乎所有的网络服务的用户都是 www-data，（当然也有些例外）。）

当然我们可以不去关注这么多，只需要知道 filerun 创建的文件是 www-data 所有的就行。

## filerun 无法访问某个目录

## filerun 无法删除某个文件

## 用户组授权 www-data 访问

将文件无脑地给 www-data 赋权固然方便，但不幸的是大多数情况下，我们并不

