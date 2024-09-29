---
title: "[1] PostgreSQL 快速入门"
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

## PostgreSQL 下载安装

在官网页面[^1]能够找到不同系统和不同软件版本的安装方式 https://www.postgresql.org/download

本文以 CentOS 7 为例，安装 PostgreSQL-12.2 版本，其余版本的安装根据官网操作即可

根据提示选择 RetHat 安装，选择安装版本，安装平台，系统架构，会自动出现 RPM 安装说明

4. Install the repository RPM:

```
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

5. Install the client packages:

```
yum install postgresql12
```

6. Optionally install the server packages:

```
yum install postgresql12-server
```

7. Optionally initialize the database and enable automatic start:

```
/usr/pgsql-12/bin/postgresql-12-setup initdb
systemctl enable postgresql-12
systemctl start postgresql-12
```

至此已经安装完成并启动了 pg，一般按照官网途径进行安装不会出现太大问题，如果使用的是非官方的途径或源码安装失败可以尝试卸载后使用官方方式重新安装

安装过程中可能会询问安装用户、密码、位置、端口等信息，为了避免不必要的麻烦一般使用默认值即可

## PostgreSQL 基本使用

安装成功后会建立一个 pg 超级用户 `postgres` 默认密码为 `postgres`，通过命令

```
[root@host] # su - postgres
Password:
```

可登录 postgres 账户

登录后可按照以下步骤来创建数据库

```
[postgres@host] $ createdb testdb
[postgres@host] $ psql testdb
psql (12.2, server 11.1)
Type "help" for help.

postgres=#
```

至此数据库就创建成功了

根据提示，使用 psql 时候可以使用 help 命令查看帮助，对于一个特定的命令语法使用下面的命令

```
postgres=# help <command_name>
```

然后就可以直接输入 SQl 语句进行数据库操作（需要注意的是 SQL 语句要以分号结尾）

## 查看 PostgreSQL 服务

通过以下命令可以查看 PostgreSQL 的运行情况，以及端口信息等

```
ps aux | grep postgres
netstat -npl | grep postgres
netstat -a | grep PGSQL
```

------------------------------------------------------------------------------------------

## 参考

[^1]: [PostgreSQL: Download](https://www.postgresql.org/download/)
