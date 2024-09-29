使用 `iptables -L` 可能会看到类似如下的配置

```
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     all  --  anywhere             anywhere             ctstate RELATED,ESTABLISHED
ACCEPT     all  --  anywhere             anywhere
```

第二条规则看上去像是允许了任意来源和目标的流量，但实际验证的效果却是后续的防火墙规则仍然在工作。

这是因为，第二条规则实际上仅针对本地回环 (lo) 接口，当使用 `iptables -L` 查看时进行了省略，使用 iptables-save 可以得到完整的防火墙规则如下

```
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i lo -j ACCEPT
```