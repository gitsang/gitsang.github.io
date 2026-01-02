---
title: WSL With Docker Could Not Be Found
slug: wsl-with-docker-could-not-be-found
description: This guide addresses the problem of Docker command not being found in a WSL 1 distribution by converting it to WSL 2 and enabling integration in Docker Desktop settings.
date: "2021-09-24T14:21:20+08:00"
lastmod: "2025-05-12T10:49:50+08:00"
weight: 100
categories:
  - "linux"
tags:
  - "Docker"
  - "WSL 1"
  - "WSL 2"
  - "Docker Desktop"
  - "Windows Subsystem for Linux"
---

<!-- markdown-front-matter -->

## 1. Problem

```powershell
$ docker

The command $ docker could not be found in this WSL 1 distro.
We recommend to convert this distro to WSL 2 and activate the WSL integration in Docker Desktop settings.

See https://docs.docker.com/docker-for-windows/wsl/ for details.
```

## 2. Solution

Run in `cmd.exe`

```powershell
> wsl --list --verbose
  NAME             STATE           VERSION
  Ubuntu           Running         1
```

Then to switch it with `wsl --set-version <your proc> 2`[^1]

```powershell
> wsl --set-version Ubuntu 2
Conversion in progress, this may take a few minutes...
For information on key differences with WSL 2 please visit https://aka.ms/wsl2
Conversion complete.
```

Also you need to go to the docker desktop settings, and enable integration with your distro in "Resources -> WSL Integration".

## 3. Reference

[^1]: [Ubuntu WSL with docker could not be found - Stack Overflow](https://stackoverflow.com/questions/63497928/ubuntu-wsl-with-docker-could-not-be-found)
