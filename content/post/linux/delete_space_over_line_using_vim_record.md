---
title: Delete Space Overline Using Vim Record
slug: delete-space-over-line-using-vim-record
description: This document explains how to use Vim's recording feature to automate cursor movement and editing tasks, particularly focusing on manipulating spaces at line ends and moving to the last non-space character. It provides solutions to common issues faced with cursor navigation.
date: "2020-05-07T20:45:49+08:00"
lastmod: "2025-05-12T10:45:17+08:00"
weight: 100
categories:
  - "linux"
tags:
  - "Vim"
  - "recording macros"
  - "last non-space character"
  - "cursor movement"
  - "automation"
---

<!-- markdown-front-matter -->

```vim
qd$a<space><space><esc>g_ldwjq
gg10000@d
```

## 1. 说明

1. 使用 vim 自带 record 功能
   - `q<?>` 启动录制，保存在 `<?>` 键
   - `q` 结束录制
   - `@<?>` 调用 `<?>` 键录制结果
   - `num@<?>` 调用 num 次 `<?>` 键录制结果
2. 在末尾 `dw` 删除空格
3. `$a<space>` 添加空格防止末尾无空格情况删除掉最后一个字符
4. `$a<space><space>` 添加两个空格防止空行情况下出现错误
5. `g_` 移动到最后一个非空字符，有些人会使用 `$be` `$ge` 等方式移动到最后一个非空字符，但大部分方式是有问题的，详见 [vim如何移动到光标所在行最后一个非空字符，而不是末尾？](https://www.zhihu.com/question/26221661)
6. 最后按 `j` 自动下一行
