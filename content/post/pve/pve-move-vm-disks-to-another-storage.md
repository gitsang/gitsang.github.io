---
title: 'PVE move vm disks to another storage'
slug: pve-move-vm-disks-to-another-storage
description: |-
  An step by step guide to move vm disks to another storage
date: 2025-02-07T15:04:52+08:00
lastmod: 2025-02-07T15:04:52+08:00
weight: 1
categories:
  - pve
tags:
  - pve
---

## Step by step

### 1. Move vm disks file to another storage

Just move file or in UI use `Hardware -> Disk -> Disk Action -> Move Storage`

### 2. Configure vm config file

Config `/etc/pve/nodes/{node_name}/qemu-server/{vm_number}.conf` and change
all old disk path to new disk path. (Especially the snapshot config)
