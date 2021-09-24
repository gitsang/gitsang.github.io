
---

title: "Using Proxy"
description: ""
lead: "Linux 使用代理"
date: 2020-03-14T14:53:28+08:00
lastmod: 2020-03-14T14:53:28+08:00
draft: false
images: []
menu:
  docs:
    parent: "linux"
weight: 100
toc: true

---

```
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```

```
export http_proxy="socks5://127.0.0.1:1080"
export https_proxy="socks5://127.0.0.1:1080"
```

```
export ALL_PROXY=socks5://127.0.0.1:1080
```

