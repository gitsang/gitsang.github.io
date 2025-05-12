---
title: Yum Command Quick Search
slug: yum-command-quick-search
description: This guide provides a list of essential commands for managing software packages using Yum, including commands for listing, searching, and removing packages.
date: "2020-01-14T14:53:28+08:00"
lastmod: "2025-05-12T10:50:03+08:00"
weight: 100
categories:
  - "linux"
tags:
  - "Yum"
  - "package management"
  - "Linux commands"
  - "software installation"
  - "system administration"
---

<!-- markdown-front-matter -->

## 1. yum 针对软件包操作常用命令

1. 查看安装了哪些软件包

```
yum list installed
```

2. 查找软件包

```
yum search
```

3. 列出所有可安装的软件包

```
yum list
```

4. 列出所有可更新的软件包

```
yum list updates
```

5. 列出所有已安装的软件包

```
yum list installed
```

6. 列出所有已安装但不在 Yum Repository 内的软件包

```
yum list extras
```

7. 列出所指定的软件包

```
yum list
```

8. 获取软件包信息

```
yum info
```

9. 列出所有可更新的软件包信息

```
yum info updates
```

10. 列出所有已安装的软件包信息

```
yum info installed
```

11. 列出所有已安装但不在 Yum Repository 内的软件包信息

```
yum info extras
```

12. 列出软件包提供哪些文件

```
yum provides
```

13. 卸载软件包

```
yum remove
```
