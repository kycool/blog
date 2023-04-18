---
title: Debian | Media change please insert the disc labeled
date: 2021-04-16 10:36:16
tags:
  - Debian
categories:
  - Linux
abbrlink: 20380
---

使用 `apt install xxx` 时，出现下面的报错

<!--more-->

```shell
Media change: please insert the disc labeled

media change: please insert the disc labeled
Debian 10.9.3 - Release amd64 (20210327.10) in the drive /media/cdrom/ and press enter
```

查看下 `/etc/apt/sources.list`

```python
# deb cdrom:[Debian GNU/Linux 10.9.0 _Buster_ - Official amd64 DVD Binary-1 20210327-10:39]/ buster contrib main

deb cdrom:[Debian GNU/Linux 10.9.0 _Buster_ - Official amd64 DVD Binary-1 20210327-10:39]/ buster contrib main

deb http://security.debian.org/debian-security buster/updates main contrib
deb-src http://security.debian.org/debian-security buster/updates main contrib

# buster-updates, previously known as 'volatile'
# A network mirror was not selected during install.  The following entries
# are provided as examples, but you should amend them as appropriate
# for your mirror of choice.
#
# deb http://deb.debian.org/debian/ buster-updates main contrib
# deb-src http://deb.debian.org/debian/ buster-updates main contrib
```

果断把包含 `deb cdrom` 这行给注释掉，然后又重新把 `apt` 源设置成清华源，修改后的文件

```python

# deb cdrom:[Debian GNU/Linux 10.9.0 _Buster_ - Official amd64 DVD Binary-1 20210327-10:39]/ buster contrib main

# deb cdrom:[Debian GNU/Linux 10.9.0 _Buster_ - Official amd64 DVD Binary-1 20210327-10:39]/ buster contrib main

# deb https://security.debian.org/debian-security buster/updates main contrib
# deb-src http://security.debian.org/debian-security buster/updates main contrib

# buster-updates, previously known as 'volatile'
# A network mirror was not selected during install.  The following entries
# are provided as examples, but you should amend them as appropriate
# for your mirror of choice.
#
# deb http://deb.debian.org/debian/ buster-updates main contrib
# deb-src http://deb.debian.org/debian/ buster-updates main contrib

# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
```

然后执行 `apt update`，最后重新安装软件成功。
