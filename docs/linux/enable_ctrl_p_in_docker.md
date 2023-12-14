
---

title: "Enable Ctrl+P in Docker"
description: ""
lead: ""
date: 2021-09-24T15:57:22+08:00
lastmod: 2021-09-24T15:57:22+08:00
draft: false
images: []
menu:
  docs:
    parent: "linux"
weight: 100
toc: true

---

## Config

edit `~/.docker/config.json` [^1]

```
{
    "detachKeys": "ctrl-q,q"
}
```

## Reference

[^1]: [docker ：把Ctrl+p换成别的什么了](https://cloud.tencent.com/developer/ask/82394)
