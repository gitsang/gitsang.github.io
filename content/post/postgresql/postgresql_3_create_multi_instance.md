---
title: "[3] PostgreSQL 创建多个实例"
description: ""
lead: ""
date: 2020-05-14T14:53:28+08:00
lastmod: 2020-05-14T14:53:28+08:00
draft: false
images: []
menu: 
  docs:
    parent: "postgresql"
weight: 100
toc: true
---

## 创建多个 postgres 实例

postgres 可以通过指定不同的 data 目录以启动多个实例。[^1]

首先登录 postgres 用户

```
sudo -su postgres
```

然后运行此命令初始化 data 目录

```
/usr/lib/postgresql/9.5/bin/pg_ctl init -D /var/lib/postgresql/9.5/main_2
```

会在 main_2 目录里生成 `pg_hba.conf` 和 `postgres.conf`

需要在 `postgres.conf` 指定端口和 hba_path，（可以 copy 之前的 conf 文件 `/etc/postgresql/9.5/main/postgresql.conf`）

将 conf 文件修改为

```
data_directory = '/var/lib/postgresql/9.5/main_2'
hba_file = '/var/lib/postgresql/9.5/main_2/pg_hba.conf'
ident_file = '/var/lib/postgresql/9.5/main_2/pg_ident.conf'
external_pid_file = '/var/run/postgresql/9.5-main-2.pid'
port = 5433
```

运行命令，启动 postgres

```
/usr/lib/postgresql/9.5/bin/pg_ctl -D /var/lib/postgresql/9.5/main_2 -l /var/log/postgresql/pg_5433.log start
```

## 参考

[^1]: [Postgresql笔记（一）安装/配置/启动/运行多个实例](https://www.jianshu.com/p/b4dde99982e0)
