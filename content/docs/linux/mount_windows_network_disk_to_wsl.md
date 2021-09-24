
---

title: "Mount Windows Network Disk to WSL"
description: ""
lead: "把 Windows 上的网络磁盘挂载到 WSL"
date: 2021-09-24T14:21:20+08:00
lastmod: 2021-09-24T14:21:23+08:00
draft: false
images: []
menu:
  docs:
    parent: "linux"
weight: 100
toc: true

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

