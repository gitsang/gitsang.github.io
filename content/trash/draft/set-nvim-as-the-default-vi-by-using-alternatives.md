---
draft: true
title: '使用 Alternatives 将 nvim 配置为默认的 vi'
slug: set-nvim-as-the-default-vi-by-using-alternatives
description: |-
  另一种更优雅的变更命令指向的方式
date: 2024-10-15T14:13:22+08:00
lastmod: 2024-10-15T14:13:22+08:00
weight: 1
categories:
  - linux
tags:
  - alternatives
  - vi
  - vim
  - nvim
  - neovim
---

我最早曾经直接将 vim 命名为 vi 或 nvim 命名为 vi 来替换 vi 命令打开的程序。

```
sudo update-alternatives --install /usr/bin/vi vi "/usr/bin/nvim" 100
```

```
# update-alternatives --config vi
There are 3 choices for the alternative vi (providing /usr/bin/vi).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /usr/bin/nvim        100       auto mode
  1            /usr/bin/nvim        100       manual mode
  2            /usr/bin/vim.basic   30        manual mode
  3            /usr/bin/vim.tiny    15        manual mode

Press <enter> to keep the current choice[*], or type selection number:
```
