---
title: Linux 不同的发行版设置静态 IP
tags:
  - Debian
categories:
  - Linux
abbrlink: 29624
date: 2020-10-16 22:01:37
---

近来在折腾本地服务器，为了方式每次重启 IP 地址的变化，所以要给机器设置静态 IP <!--more-->

## 一 不同发行版静态 IP 设置

### 1 Centos 设置静态 IP

#### 修改网络配置

```
vi /etc/sysconfig/network-scripts/ifcfg-eth192
```

修改后的内容如下

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
# 这里可以使用 static 或者 no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens192
UUID=cd93f1df-d2d5-4c63-a64c-761e9ee23aae
DEVICE=ens192
# 开机启用此配置
ONBOOT=yes
# 静态 IP
IPADDR=192.168.1.12
# 默认网关
GATEWAY=192.168.1.1
```

#### 重启网络服务

```
systemctl restart network
```

#### 查看地址

```shell
[isproot@192 ~]$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:51:63:21 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.12/24 brd 192.168.1.255 scope global noprefixroute ens192
       valid_lft forever preferred_lft forever
    inet6 2408:8256:a80:313:6ba6:7ad0:edd0:defe/64 scope global noprefixroute dynamic
       valid_lft 183241sec preferred_lft 96841sec
    inet6 fe80::b6f3:1daa:4b7b:6994/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

### 2 Debian 设置静态 IP

#### 修改网络配置

```shell
vi /etc/network/interfaces
```

修改后的内容如下

```shell
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens192
# iface ens192 inet dhcp
# 设置成静态 IP
iface ens192 inet static
# 设置 IP
address 192.168.1.11
# 设置子网掩码
netmask 255.255.255.0
# 设置网关
gateway 192.168.1.1
# 设置 DNS 服务器，我这里设置成路由器的地址
dns-nameservers 192.168.1.1
# This is an autoconfigured IPv6 interface
iface ens192 inet6 auto
```

#### 重启网络服务

```
service networking restart
```

#### 查看地址

```shell
root@debian:~# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:0a:14:07 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.11/24 brd 192.168.1.255 scope global ens192
       valid_lft forever preferred_lft forever
    inet6 2408:8256:a81:c14f:20c:29ff:fe0a:1407/64 scope global dynamic mngtmpaddr
       valid_lft 206755sec preferred_lft 120355sec
    inet6 fe80::20c:29ff:fe0a:1407/64 scope link
       valid_lft forever preferred_lft forever
```

## 二 小结

从配置的过程来看，基本上都是配置文件的目录和配置项不同而已，其他基本上都差不多，毕竟原理都是一样的。
