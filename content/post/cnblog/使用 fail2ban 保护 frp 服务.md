## 背景

我们一般会使用 fail2ban 来保护暴露到公网的提供密码登录的 ssh 连接等。

但使用 frp 穿透后所有的从外网访问都会变成 127.0.0.1 进入的，原本能用 fail2ban 保护的如 ssh 服务将无法使用。

因此 fail2ban 应该放到 frps 服务器上。但 frps 的日志并不会对失败进行辨别，无论你访问哪个服务，frp 日志只会有连接和断开两种日志。

### 不完美的解决途径

正常情况下，我们不会频繁地连接和断开，只有被扫描时才容易出现。

因此添加自定义 filter，并设置一段时间内连接超过阈值后进入监狱。

编写文件 `/etc/fail2ban/filter.d/frps.conf`

```
[Definition]

failregex = ^.*get a user connection \[<HOST>:[0-9]*\]
            ^.*get a new work connection: \[<HOST>:[0-9]*\]
ignoreregex =
```

编写文件 `/etc/fail2ban/jail.local` 添加

```
[frp]
enabled = true
findtime = 10m
maxretry = 100
bantime = 1d
filter = frps
logpath = /data/frp/log/frps.log
protocol = all
chain = all
port = all
action = iptables-allports[name=frp,protocol=tcp]
```

记得 `fail2ban-client reload` 重载服务和 `fail2ban-client status frp` 确认服务状态。

如果你要添加自己的过滤规则可以使用 `fail2ban-regex <LOG> <REGEX> [IGNOREREGEX]` 进行验证，比如 `fail2ban-regex /data/frp/log/frps.log /etc/fail2ban/filter.d/frps.conf` （记得要用绝对路径）

然后你可以把阈值改小一点，用多次 telnet 来验证是否能过成功封锁。 然后用 `fail2ban-client set frp unbanip 12.36.14.241` 来解除封锁。

## Outlook

不是很完美的方案，比如如果是 http 连接，很可能超过限制，实际使用需要做一些排除的匹配。

也许能通过 tcpdump 抓包日志来进行过滤，或编写程序输出一个更清晰的日志。