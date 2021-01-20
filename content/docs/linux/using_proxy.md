---
title: "[Linux] 使用代理"
date: 2020-03-14T14:53:28+08:00
categories:
- Linux
tags:
- Linux
- 网络
thumbnailImage: images/mapel.jpg
thumbnailImagePosition: right
metaAlignment: center
coverImage: images/mapel.jpg
coverMeta: in
coverSize: partial
---
<!--more-->

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

---
## 参考

https://zhuanlan.zhihu.com/p/46973701

https://samzong.me/2017/11/17/howto-use-ssr-on-linux-terminal/

https://www.serverlab.ca/tutorials/linux/administration-linux/how-to-set-the-proxy-for-apt-for-ubuntu-18-04/

zhangminghao.com/post/48.html

https://www.zhangminghao.com/post/47.html
