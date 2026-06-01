---
title: "[2] 建立 PostgreSQL 连接及常见错误解决方法"
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

## 连接 pg

通过以下命令可访问远程机器上的 pg

```
# psql -U postgres -h 10.200.110.110 -p 5432
```

## 连接被拒绝的解决方案

### 连接被拒 1

大多数情况下 pg 会禁止远程接入，通过以上命令，或其他方式接入 pg 可能被拒绝访问，出现如下错误[^1][^2][^5]

```
psql: error: could not connect to server: FATAL:  Ident authentication failed for user "postgres"
```

或其他类似的错误

```
Connection to 10.200.110.110:5432 refused. Check that the hostname and port are correct and that the postmaster is accepting TCP/IP connections.
```

### 解决方法

编辑安装 pg 机器上的 `/var/lib/pgsql/data/postgresql.conf` 文件，修改

```
#listen_addresses = 'localhost'
```

成

```
listen_addresses = '*'
```

修改完成后通过命令重启 pg 即可

```
[postgres@host] $ pg_ctl restart
```

### 连接被拒绝 2

上述方法可能依然无法解决连接被拒绝的问题，是由于在客户端访问PostgreSQL数据库时，PostgreSQL会读取文件pg_hba.conf判断是否信任该主机，故所有需要连接PostgreSQL Server的主机都应当在pg_hba.conf中添加对其信任，即使是Server主机也不例外

```
psql: could not connect to server: Connection refused  
    Is the server running on host "my host name" (IP) and accepting  
    TCP/IP connections on port 5432?
```

### 解决方法

编辑安装 pg 机器上的 `/var/lib/pgsql/data/pg_hba.conf` 文件，在末尾添加如下：

```
host    all         all         <host_ip>/32         trust
```

host_ip 填写连接机器的 ip，或直接填入 `0.0.0.0/0`

trust 可以更改为 password 通过密码连接

修改完成后通过命令重启 pg 即可

```
[postgres@host] $ pg_ctl restart
```

## 重启失败

通过 pg_ctl 重启 pg 可能出现失败。

```
pg_ctl: no database directory specified and environment variable PGDATA unset
Try "pg_ctl --help" for more information.
```

### 解决方法

从报错信息可以看出是没有配置环境变量 PGDATA

编辑 postgres(或其他数据库管理员) 用户的用户配置文件[^4]

```
[postgres@host ~] $ vim ~/.bash_profile
```

按照实际路径在用户配置文件中添加 PGDATA 变量。默认该路径在数据库软件安装路径下的data目录。

```
export PGDATA=/var/lib/pgsql/data
```

然后重新接在配置文件

```
[postgres@host ~] $ source ~/.bash_profile
```

## 找不到 pg_ctl 命令

```
-bash: pg_ctl: command not found
```

### 解决方法

原因是 pg 的 bin 目录不在 PATH 环境变量下

可以修改配置文件，添加 bin 路径到 PATH 下[^3]

```
PATH=$PATH:$HOME/bin:/usr/pgsql-12/bin/
```

这里我直接在 postgres 用户的 ~/.bash_profile 下添加了一个 PATH，这样登录 postgres 才会添加这个 bin 路径

------------------------------------------------------------------------------------------

## 参考

[^1]: [PostgreSQL一些简单问题以及解决办法 - CSDN](https://blog.csdn.net/kongxx/article/details/5964638)

[^2]: [PostgreSQL问题解决--连接失败 - CSDN](https://blog.csdn.net/u012948976/article/details/51763565?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)

[^3]: [初识Postgresql和Sqoop - oschina](https://my.oschina.net/zhangjiawen/blog/180637)

[^4]: [使用pg_ctl启动数据库时报错 - CSDN](https://blog.csdn.net/pg_hgdb/article/details/78712694)

[^5]: [psql: FATAL: Ident authentication failed for user “postgres” - stackoverflow](https://stackoverflow.com/questions/2942485/psql-fatal-ident-authentication-failed-for-user-postgres)
