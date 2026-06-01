---
title: Using Proxy
slug: using-proxy
description: This guide demonstrates how to set environment variables to route network traffic through HTTP and SOCKS5 proxies using specific IP addresses and ports. It includes instructions for both direct and SOCKS5 proxy configurations.
date: "2020-03-14T14:53:28+08:00"
lastmod: "2025-05-12T10:48:56+08:00"
weight: 100
categories:
  - "linux"
tags:
  - "proxy settings"
  - "network routing"
  - "HTTP proxy"
  - "SOCKS5 proxy"
  - "environment variables"
---

<!-- markdown-front-matter -->

```sh
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```

```sh
export http_proxy="socks5://127.0.0.1:1080"
export https_proxy="socks5://127.0.0.1:1080"
```

```sh
export ALL_PROXY=socks5://127.0.0.1:1080
```
