---
title: 'Curl 输出格式'
description: ''
slug: curl-write-out-format
date: 2019-10-09T14:53:28+08:00
lastmod: 2020-05-28T15:22:06+08:00
weight: 1
image: cover.jpg
categories:
  - linux
tags:
  - linux
  - curl
---

## 操作方法

默认情况下，curl 不会输出耗时信息，状态码等，若需要输出，需要使用 `-w, --write-out FORMAT` 选项配置 Write Out 格式。

```sh
curl -w "\n\ntime_total:  %{time_total}s\n" https://www.example.com
```

Write Out 中支持的变量请参考：

https://everything.curl.dev/usingcurl/verbose/writeout#available-write-out-variables

也可以使用文件

```sh
curl -w "@curl-format.txt" https://www.example.com
```

一个简单的文件格式参考如下：

```txt
\n
time_namelookup:    %{time_namelookup}s\n
time_connect:       %{time_connect}s\n
time_appconnect:    %{time_appconnect}s\n
time_pretransfer:   %{time_pretransfer}s\n
time_redirect:      %{time_redirect}s\n
time_starttransfer: %{time_starttransfer}s\n
----------\n
time_total:         %{time_total}s\n
```

用于 debug 的详细信息格式参考：

```txt
\n
url_effective:      %{url_effective}\n
ssl_verify_result:  %{ssl_verify_result}\n
content_type:       %{content_type}\n
filename_effective: %{filename_effective}\n
ftp_entry_path:     %{ftp_entry_path}\n
http_code:          %{http_code}\n
http_connect:       %{http_connect}\n
local_ip:           %{local_ip}\n
local_port:         %{local_port}\n
num_connects:       %{num_connects}\n
num_redirects:      %{num_redirects}\n
redirect_url:       %{redirect_url}\n
remote_ip:          %{remote_ip}\n
remote_port:        %{remote_port}\n
response_code:      %{response_code}\n
size_download:      %{size_download} bytes\n
size_header:        %{size_header} bytes\n
size_request:       %{size_request} bytes\n
size_upload:        %{size_upload} bytes\n
speed_download:     %{speed_download} bytes/s\n
speed_upload:       %{speed_upload} bytes/s\n
time_appconnect:    %{time_appconnect}s\n
time_connect:       %{time_connect}s\n
time_namelookup:    %{time_namelookup}s\n
time_pretransfer:   %{time_pretransfer}s\n
time_redirect:      %{time_redirect}s\n
time_starttransfer: %{time_starttransfer}s\n
time_total:         %{time_total}s\n
```
