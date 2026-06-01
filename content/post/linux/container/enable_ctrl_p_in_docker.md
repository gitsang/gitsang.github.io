---
title: Enable Ctrl+P in Docker
slug: enable-ctrl-p-in-docker
description: Learn how to edit the Docker configuration file to change default detach keys. This guide provides a simple example using the 'config.json' file.
date: "2021-09-24T15:57:22+08:00"
lastmod: "2025-05-12T10:45:47+08:00"
weight: 100
categories:
  - "linux"
tags:
  - "Docker"
  - "configuration"
  - "detach keys"
  - "config.json"
  - "Docker settings"
---

<!-- markdown-front-matter -->

## 1. Config

edit `~/.docker/config.json` [^1]

```
{
    "detachKeys": "ctrl-q,q"
}
```

## 2. Reference

[^1]: [docker ：把Ctrl+p换成别的什么了](https://cloud.tencent.com/developer/ask/82394)
