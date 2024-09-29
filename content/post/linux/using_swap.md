
---

title: "Using Swap"
description: ""
lead: ""
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
