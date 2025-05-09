---
title: RAM Permissions and Domain Management in Alibaba Cloud
slug: ram
description: This guide details steps to manage subdomains and assign RAM permissions for secure domain handling in Alibaba Cloud, ensuring each environment has its own AccessKey. It covers subdomain creation, RAM policy setup, and AccessKey application for streamlined operations.
date: "2025-05-09T19:17:31+08:00"
lastmod: "2025-05-09T19:17:31+08:00"
weight: 1
categories: 
- "Cloud Computing"
- "Domain Management"
- "Security"
tags: 
- "Alibaba Cloud"
- "RAM Permissions"
- "Domain Configuration"
- "AccessKey Management"
- "ACME"
- "DDNS"

<!-- markdown-front-matter auto -->
---

## 1. 背景

背景：

- 假设只有一个二级域名 `domain.com`，有多套环境的情况下，可能需要分配不同的三级子域名 `sub.domain.com`，每个环境可能需要再配置四级子域名 `sub.sub.domain.com`
- 如果使用一个 AK 拥有所有域名的 DNS 权限，可能不太安全（即使互相信任，也无法避免误操作导致影响其他环境）

需求：

- 每套环境需要拥有自己的一套 AK，并能自己管理自己的域名，互不冲突

## 2. 操作步骤

### 2.1 添加子域名

阿里云的 RAM 访问控制中，不允许使用通配等方式配置域名资源（因为操作是针对 AUTHORITY SECTION 的），因此必须先拆分出子域名。

首先在 [域名解析](https://dns.console.aliyun.com/) 页面添加子域名（本文以 `env1.domain.com` 为例）

![image](https://img2023.cnblogs.com/blog/2038910/202305/2038910-20230525094954695-265454889.png)

添加域名需要 TXT 记录验证

![image](https://img2023.cnblogs.com/blog/2038910/202305/2038910-20230525095051168-579963715.png)

按照提示要求在你的主域名添加对应的 TXT 主机记录后点击验证即可添加成功。

### 2.2 创建子域名的 RAM 权限策略

其策略类似如下（需要把 `${your-sub-domain}` 改为刚才创建的子域名，如本文的 `env1.domain.com`），此处虽然可以使用通配，但名称必须是域名解析中列出的域名值。

```json
{
  "Version": "1",
  "Statement": [
    {
      "Action": "alidns:*",
      "Resource": "acs:alidns:::domain/${your-sub-domain}",
      "Effect": "Allow"
    },
    {
      "Action": ["alidns:Describe*"],
      "Resource": "acs:alidns:::*",
      "Effect": "Allow"
    }
  ]
}
```

这里配置了两个策略：

- 第一个策略是允许 `acs:alidns:::domain/${your-sub-domain}` 资源的所有 `alidns:*` 操作
- 第二个策略是允许所有 `acs:alidns:::*` 资源的 Describe `alidns:Describe*` 操作（此处可能还需要 Describe 其他的资源，阿里云文档和客服并没有给出明确的答复）

### 2.3 子账号赋权和 AK 申请

新建一个 RAM 用户，需要勾选 `OpenAPI 调用访问`

![image](https://img2023.cnblogs.com/blog/2038910/202305/2038910-20230525103254130-608849459.png)

之后进入用户详情创建 AK

![image](https://img2023.cnblogs.com/blog/2038910/202305/2038910-20230525103411240-349407655.png)

进入权限管理为用户赋予刚才创建的策略，或者你也可以为用户组赋权后将用户加入用户组（推荐）

![image](https://img2023.cnblogs.com/blog/2038910/202305/2038910-20230525104202528-918302234.png)

### 2.4 使用 AK

之后根据使用的不同的 ACME 或 DDNS 服务等的文档配置 AK 即可
