---
title: "[4] PostgreSQL 语法零散总结"
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

### Comment 为 pg 数据库的表和列添加注释

1. 添加表注释

```sql
COMMENT ON TABLE "public".t1 IS '表注释内容';   
```

2. 查看注释

```sql
select description from pg_description join pg_class on pg_description.objoid = pg_class.oid where relname = 't1';
```

注释存储在 pg_description 表的 description 字段中，用来查找的每张表的 objoid 存储在 pg_class 中。

3. 添加列注释

```sql
create table t1(id int primary key, cc char(10));
comment on column t1.id is '列注释内容';
```

4. Postgres 查重复数据

```sql
select * from pg_public_tree_node ou where (select count(*) from pg_public_tree_node inr where inr.id = ou.id) > 1;
```


