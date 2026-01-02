---
title: 'i440fx vs q35'
slug: i440fx-vs-q35
description: |-
  Discuss the difference between i440fx and q35
date: 2025-02-08T11:15:25+08:00
lastmod: 2025-02-08T11:15:25+08:00
weight: 1
categories:
  - pve
tags:
  - pve
---

## Differences

这个问题似乎在社区中，并没有被广泛的讨论，大多数讨论都认为两个芯片组并无明显差
异[^4]。比较详细的讨论是 Rediit 中的这篇讨论[^5]

一个常被提到的区别是，q35 原生支持 PCIE，而 i440fx 并不完全支持。因此通常在进行
GPU 直通时，可能会优先使用 q35（Reddit 中有人提到 i440fx 会将 PCIE 设备模拟成
PCI，但仍然以 PCIE 速度运行，所以可能也并不会有明显区别[^1]）。

另外，在 reddit 中，有人提到 i440fx 支持热插拔，所以据说可以将 GPU 删除以允许
VM 保存状态而不是暂停/关闭？这反过来允许 VM 在运行时使用完成快照。[^2]

在这个回复中，有人提到 q35 的限制[^6]: Limited IO space can affect the number
of devices used by a single Q35 machine（有限的 IO 空间可能会影响单个 Q35 机
器使用的设备数量），这个问题我似乎在某篇直通文章中见过有人提到当 GPU 和 USB 设
备超过一定数量时，会导致虚拟机无法启动，但记不起细节了。

另一篇文章[^3]则提到：`很长一段时间以来，q35 不被建议用于 GPU 直通，因为某些部
分没有完全解决`，这似乎与主流文章（至少是国内大部分文章）描述不一致，但文章中并
没有详细说明这些问题。我一直在使用 q35 进行 GPU 直通，而目前并没有遇到过 q35 带
来的问题。

## Reference

[^4]: [q35 vs i440fx | Proxmox Support Forum](https://forum.proxmox.com/threads/q35-vs-i440fx.112147/)
[^5]: [Differences/benefits between i440fx and q35 chipsets? : r/VFIO](https://www.reddit.com/r/VFIO/comments/5ireij/differencesbenefits_between_i440fx_and_q35/)
[^1]: https://www.reddit.com/r/VFIO/comments/5ireij/comment/dbafh86/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button
[^2]: https://www.reddit.com/r/VFIO/comments/5ireij/comment/dbagbbd/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button
[^3]: [Virtualized Windows 10 – i440FX vs Q35](https://altechnative.net/virtualized-windows-10-i440fx-vs-q35/)
[^6]: https://www.reddit.com/r/VFIO/comments/5ireij/comment/dbb2e01/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button
