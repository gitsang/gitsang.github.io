---
title: 'How To Remove Systemd Service'
slug: how-to-remove-systemd-service
description: |-
  The correct way to remove systemd service.
date: 2021-03-18T09:21:00+08:00
lastmod: 2021-03-18T09:21:00+08:00
weight: 1
categories:
  - linux
tags:
  - linux
  - systemd
---

## Method

```
systemctl stop [servicename]
systemctl disable [servicename]
rm /your/service/locations/[servicename]
rm /your/service/locations/[servicename] # and symlinks that might be related
systemctl daemon-reload
systemctl reset-failed
```

Systemd uses unit (file to define services) to remove a service the unit have to be removed... here is a list of unit locations :[^1]

```
/etc/systemd/system/ (and sub directories)
/usr/local/etc/systemd/system/ (and sub directories)
~/.config/systemd/user/ (and sub directories)
/usr/lib/systemd/ (and sub directories)
/usr/local/lib/systemd/ (and sub directories)
/etc/init.d/ (Converted old service system)
```

You can easily find location in `loaded` property using `systemctl status [service]`

```
$ systemctl status bluetooth.service
 bluetooth.service - Bluetooth service
  Loaded: loaded (/lib/systemd/system/bluetooth.service; disabled; vendor preset: enabled)
  Active: inactive (dead)
    Docs: man:bluetoothd(8)
```

Or using `systemctl cat [service]`

```
# systemctl cat frps.service
# /lib/systemd/system/frps.service
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
User=nobody
Restart=on-failure
RestartSec=5s
ExecStart=/usr/bin/frps -c /etc/frp/frps.ini

[Install]
WantedBy=multi-user.target
```

### About "reset-failed" Option

From the systemd man page:

> reset-failed [PATTERN...]
>
> Reset the "failed" state of the specified units, or if no unit name is passed, reset the state of all units. When a unit fails in some way (i.e. process exiting with non-zero error code, terminating abnormally or timing out), it will automatically enter the "failed" state and its exit code and status is recorded for introspection by the administrator until the service is restarted or reset with this command.

## Reference

[^1]: [How to remove systemd services](https://superuser.com/questions/513159/how-to-remove-systemd-services/1344239#1344239)
