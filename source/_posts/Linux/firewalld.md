---
title: CentOS 7 firewalld
tags: firewalld
categories: Linux
date: 2020-05-20 22:56:02
---
CentOS 7 通过防火墙开放指定端口
<!-- more -->

## firewall-cmd
```bash
# 查看开通端口列表
firewall-cmd --list-ports

# 开放80端口
firewall-cmd --permanent --zone=public --add-port=80/tcp

# 关闭80端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent

# 重启防火墙生效
systemctl restart firewalld

# PS: 
# firewalld配置的防火墙策略也分为两种模式
# 1、runtime，当前生效，重启后失效
# 2、permanent，当前失效，重启后生效，为了让permanet生效，一般会执行friewall-cmd --reload，使其立即生效
```

## 说明
 - Linux上新的防火墙软件，和iptables差不多的工具。firewalld是centos7的一大特性，两大优点：支持动态更新，不用重启服务；加入了防火墙的“zone”概念。
 - firewalld能动态修改单条规则，不像iptables，修改规则后必须全部刷新才能生效。
 - firewalld在使用上要比iptables人性化很多。firewalld自身并不具备防火墙的功能，而是和iptables一样需要通过内核的netfilter来实现，也就是说firewalld和iptables一样，他们的作用都是用于维护规则，而真正使用规则干活的是内核的netfilter。
---
 - rpm -qa | grep firewalld | 查看是否安装
 - yum install firewalld 安装
 - rpm -qa | grep firewall-config 查看是否安装图形界面
 - yum install firewall-config 如果需要图形界面的话，需要安装
 - systemctl start firewalld 启动
 - systemctl restart firewalld 重启
 - systemctl disable firewalld 停止
 - systemctl stop firewalld 禁用
 - systemctl status firewalld | - firewall-cmd --state 查看状态
 - firewall-cmd --list-ports 查看开通端口列表
 - firewall-cmd --zone=public --remove-port=80/tcp --permanent 关闭端口
 - firewall-cmd --permanent --zone=public --add-port=80/tcp 开通端口
 - firewall-cmd --get-default-zone 查看default zone
 - firewall-cmd --get-active-zones 查看active zone

 - 备注：--zone #作用域 --add-port=80/tcp #添加端口，格式为：端口/通讯协议 --permanent #永久生效，没有此参数重启后失效
firewall防火墙默认的几个zone（由firewalld 提供的区域按照从不信任到信任的顺序排序）


<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}