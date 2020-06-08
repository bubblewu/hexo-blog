---
title: CentOS 7 安装 mongodb
tags: mongodb
categories: Linux
date: 2020-05-20 22:32:33
---
CentOS 7 安装 mongodb
<!-- more -->

## 安装
```bash
# 创建 repo文件
touch /etc/yum.repos.d/mongodb-org-4.0.repo
# 配置 repo文件(复制以下内容到.repo文件)
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb- org/4.0/x86_64/
gpgcheck=1
enabled=1

gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
# 安装
sudo yum install -y mongodb-org
# 编辑配置文件: 修改绑定地址为0.0.0.0 
vim /etc/mongod.conf
# 启动、停止、重启
service mongod start/stop/restart
```

## 配置
```bash
# 1.修改数据和日志的存储位置
# bin目录下增加mongodb.conf文件
# 内容：
port=27017
dbpath=/usr/local/mongodb/data/
logpath=/usr/local/mongodb/log/mongodb.log
fork = true

# 释义：
# port: 数据库服务使用端口
# dbpath: 数据存放的文件位置
# logpath: 日志文件的存放位置
# fork: 后台守护进程运行

# 启动命令：
./mongod -f mongodb.conf

# 关闭方式：
./mongo
# >
# >use admin
# >db.shutdownServer()
```

## 卸载
```bash
# 卸载
yum erase $(rpm -qa | grep mongodb-org)
rm -r /var/log/mongodb
rm -r /var/lib/mongo
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}