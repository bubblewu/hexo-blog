---
title: CentOS 7 安装 network
tags: network
categories: Linux
date: 2020-05-20 22:49:51
---
CentOS 7网络配置(Mac虚拟机下的共享配置)
<!-- more -->

## 编辑网络配置文件
```bash
# 编辑配置文件ifcfg-eth0 ifcfg-eth1 ...
vi /etc/sysconfig/network-scripts/ifcfg-eth0
# 复制以下项
DEVICE=eth0
BOOTPROTO=static
NOBOOT=yes
IPADDR=192.168.10.192
NETMASK=255.255.252.0
GATEWAY=192.168.10.254
DNS1=192.168.10.3
DNS2=223.6.6.6
NM_CONTROLLED=no

# 重启网络CentOS7网络后生效
systemctl restart network
```

## 参数解读
```bash
# 注：这里面的IP地址，子网掩码，GATE ，DNS都按照主机的配置。
# 之后，重启一下虚拟机应该就可以。
# 之后，ping下主机IP地址，ping 下局域网其他主机IP地址，再ping下外网ip地址，可以的话就算成功

####################################

# 从dhcp改成static
BOOTPROTO=static
# 从no改成yes。系统将在启动时自动开启该接口。
ONBOOT=yes
# 设置IP地址
IPADDR=
# 设置子网掩码
NETMASK=
# 设置网关
GATEWAY=
# 设置DNS
DNS1=
DNS2=
# 表示该接口将通过该配置文件进行设置，而不是通过网络管理器进行管理
NM_CONTROLLED=no

####################################
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}