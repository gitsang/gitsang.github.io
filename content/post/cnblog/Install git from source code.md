# Install Git from Source Code

## Prerequisite

### CentOS

```
yum install zlib-devel
yum install asciidoc
yum install xmlto
```

### Debian

```
apt-get install zlib1g-dev
apt-get install asciidoc
apt-get install xmlto
```

## Install

You can get it via the kernel.org site, at https://www.kernel.org/pub/software/scm/git, or the mirror on the GitHub website, at https://github.com/git/git/tags.

```
wget https://github.com/git/git/archive/refs/tags/v2.40.1.tar.gz
tar -zxf v2.40.1.tar.gz
cd v2.40.1
make configure
./configure --prefix=/usr
make all doc info
sudo make install install-doc install-html install-info
```

## Reference

- https://git-scm.com/book/en/v2/Getting-Started-Installing-Git