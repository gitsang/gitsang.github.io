
---

title: "Install PHP 7.4 on CentOS 7"
description: ""
lead: ""
date: 2021-09-30T09:23:35+08:00
lastmod: 2021-09-30T09:23:35+08:00
draft: false
images: []
menu:
  docs:
    parent: "linux"
weight: 100
toc: true

---

## Step 1: Add EPEL and REMI Repository

```
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum -y install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
```

## Step 2: Install PHP 7.4 on CentOS 7

- Enable PHP 7.4 Remi repository

```
sudo yum -y install yum-utils
sudo yum-config-manager --enable remi-php74
```

- Install

```
sudo yum update
sudo yum install php php-cli
```

- Install additional packages:

```
sudo yum install php-xxx
```

Example:

```
sudo yum install php  php-cli php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json
```

- Check Version

```
$ php -v
PHP 7.4.0 (cli) (built: Nov 26 2019 20:13:36) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```

View enabled modules

```
$ php --modules
```
