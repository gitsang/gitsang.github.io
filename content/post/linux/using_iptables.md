
---

title: "Using Iptables"
description: ""
lead: ""
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

## 1. 使用 iptables 进行端口映射[^1][^2]

### 第一步 : 打开端口映射功能 :

#### 方法一 : (允许数据包转发)

```
sudo echo '1' > /proc/sys/net/ipv4/ip_forward
```

#### 方法二 :

```
vim /etc/sysctl.conf
```

将 `;net.ipv4.ip_forward = 0` 这一行的注视去掉 , 并将 0 改为 1

修改后的结果为 :

```
net.ipv4.ip_forward = 1
```

### 第二步 : 进行映射 :

DNAT

```
iptables -t nat -A PREROUTING -d 本机IP -p tcp --dport 本机端口 -j DNAT --to-destination 目标机IP:目标机端口
```

SNAT

```
iptables -t nat -A PREROUTING -d 本机IP -p tcp --dport 本机端口 -j SNAT --to-destination 目标机IP:目标机端口
```

## 参考：

[^1]: [Linux 端口映射](https://www.jianshu.com/p/d3f30fb9ebf6)
[^2]: [iptables端口转发](https://blog.csdn.net/zzhongcy/article/details/42738285)
