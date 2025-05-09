> 非 LVM 分区实现动态扩容，适用于系统分区扩容，无需格式化磁盘，无需重新挂载磁盘

### 0.1 扩容步骤

以 `/dev/sda2` 扩容为例，假设 `/dev/sda` 空间足够（或已通过虚拟化管理平台增加容量）

使用 `fdisk -l` 命令可看到 `/dev/sda` 磁盘总容量 200GiB，`/dev/sda2` 分区容量 100GiB

```
Disk /dev/sda: 200 GiB, 214748364800 bytes, 419430400 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 1459CE9A-A8E1-4934-8EC6-17C7BA97E9E0

Device     Start       End   Sectors  Size Type
/dev/sda1   2048      4095      2048    1M BIOS boot
/dev/sda2   4096 419430366 209711087  100G Linux filesystem
```

现将 `/dev/sda2` 分区扩容到 200GiB

#### 0.1.1 重新分区

```
fdisk /dev/sda
```

- 输入 `p` 查看分区情况，确认 `/dev/sda2` 所在的位置和大小。
- 输入 `d` 删除分区 `/dev/sda2`。
- 输入 `n` 创建一个新的分区。
- 选择主分区或扩展分区（根据你的需要）。
- 按照提示输入分区编号（如果有多个分区）。
- 按照提示输入新的起始扇区（通常是默认值）。
- 按照提示输入新的结束扇区，确保分区大小为200G。
- **如果出现提示是否保留文件索引，选择 `保留`**
- 输入 `w` 保存更改并退出。

再次输入 `fdisk -l` 应该能看到 `/dev/sda2` 已扩容成功

```
Disk /dev/sda: 200 GiB, 214748364800 bytes, 419430400 sectors
Disk model: QEMU HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 1459CE9A-A8E1-4934-8EC6-17C7BA97E9E0

Device     Start       End   Sectors  Size Type
/dev/sda1   2048      4095      2048    1M BIOS boot
/dev/sda2   4096 419430366 419426271  200G Linux filesystem
```

#### 0.1.2 扩展文件系统

输入 `df -h` 命令查看文件系统大小

```
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           6.3G  1.6M  6.3G   1% /run
/dev/sda2        99G   81G   18G  81% /
```

可以看到 `/dev/sda2` 文件系统空间仍未改变

对于 `ext4` 系统，可以使用 `resize2fs /dev/sda2` 命令扩展文件系统

再次输入 `df -h` 命令，可以看到 `/dev/sda2` 文件系统空间完成扩容

```
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           6.3G  1.6M  6.3G   1% /run
/dev/sda2       197G   81G  108G  43% /
tmpfs            32G     0   32G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           6.3G  4.0K  6.3G   1% /run/user/1000
```
