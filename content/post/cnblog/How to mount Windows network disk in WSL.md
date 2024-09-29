---
title: 'How to mount Windows network disk in WSL'
description: ''
slug: how-to-mount-windows-network-disk-in-wsl
date: 2021-03-19T11:03:00+08:00
lastmod: 2021-03-19T11:03:00+08:00
weight: 1
categories:
  - wsl
tags:
  - wsl
  - windows
  - samba
---

## Backgroud

Mount samba directly in wsl like linux is difficult

```
Password for root@//filesystem.domain/root:
mount error: cifs filesystem not supported by the system
mount error(19): No such device
Refer to the mount.cifs(8) manual page (e.g. man mount.cifs)
```

## Solution

But is easily mount net disk in windows file manager. So if your windows share is already mapped to a drive in the Windows host, it can be even simpler.

Suppose you already mounted the share on Z:. In that case the following will work:[^1]

```
sudo mkdir /mnt/z
sudo mount -t drvfs 'Z:' /mnt/z
```

## Reference

[^1]: [Mounting a windows share in Windows Subsystem for Linux - Stack Overflow](https://stackoverflow.com/questions/45244306/mounting-a-windows-share-in-windows-subsystem-for-linux/46968030)
