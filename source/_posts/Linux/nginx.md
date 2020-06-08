---
title: CentOS 7 安装 nginx
tags: nginx
categories: Linux
date: 2020-05-20 22:37:41
---
CentOS 7 安装 nginx
<!-- more -->

## 安装
```bash
# 安装依赖包 c编译gcc依赖
yum install gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
# 下载安装包
wget -c https://nginx.org/download/nginx-1.12.0.tar.gz
# 解压安装包
tar -zxvf nginx-1.12.0.tar.gz
# 进入安装包
cd nginx-1.12.0
# 编译预处理
./configure
# 编译并安装
make && make install

# 进入nginx目录
cd /usr/local/nginx/sbin/
# 启动nginx ./nginx [-s stop|reload]
./nginx -s reload -t
```

## 配置文件路径说明
```bash
# ./configure           \
# --prefix=/etc/nginx                     \
# --sbin-path=/usr/sbin/nginx                 \
# --conf-path=/etc/nginx/nginx.conf              \
# --error-log-path=/var/log/nginx/error.log          \
# --http-log-path=/var/log/nginx/access.log          \
# --pid-path=/var/run/nginx.pid                \
# --lock-path=/var/run/nginx.lock
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}