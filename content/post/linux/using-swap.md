---
title: 'Using Swap'
slug: using-swap
description: |-
  Step by step to enable swap.
date: 2021-04-13T11:38:00+08:00
lastmod: 2021-04-13T11:38:00+08:00
weight: 1
categories:
  - linux
tags:
  - linux
  - swap
---

```
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
