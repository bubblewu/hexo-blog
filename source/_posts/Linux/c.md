---
title: CentOS 7  c 编译依赖安装
tags: gcc
categories: Linux
date: 2020-05-20 22:24:04
---
Linux使用源码安装软件时，需要c编译时，首先安装c编译依赖
<!-- more -->

# 安装
```bash
yum install gcc
yum install pcre-devel
yum install zlib zlib-devel
yum install openssl openssl-devel

yum install gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}