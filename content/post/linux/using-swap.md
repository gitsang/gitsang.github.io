---
title: Using Swap
slug: using-swap
description: Instructions on creating, formatting, and enabling a swap file in Linux, including configuration for automatic activation at boot.
date: "2021-04-13T11:38:00+08:00"
lastmod: "2025-05-12T10:47:59+08:00"
weight: 1
categories:
  - "linux"
tags:
  - "Linux"
  - "swap file"
  - "memory management"
  - "mkswap"
  - "swapon"
  - "fstab"
---

<!-- markdown-front-matter -->

```sh
# create swap file
dd if=/dev/zero of=/.swap bs=1048576 count=4096

# format swap
mkswap /.swap

# start swap
swapon /.swap

# check
free -h

# onboot
echo "/.swap swap swap defaults 0 0" >> /etc/fstab
```
