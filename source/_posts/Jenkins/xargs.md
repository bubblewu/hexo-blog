---
title: Jenkins部署Python
tags: shell
categories: Jenkins
date: 2020-05-24 00:30:19
---
Jenkins部署 - 使用shell脚本后台守护进程运行
<!-- more -->

## 示例
```bash
#!/bin/bash

# 保证程序后台运行不杀死  和 nohub、& 一同使用
BUILD_ID=dontKillMe

# source /root/.bash_profile

# 停止服务(先杀死Python当前运行的进程)
# ps -ef | grep python3 | grep -v grep | awk -F ' ' '{print $2}' | xargs kill -9
pid=`ps -ef | grep python3 | grep -v grep | awk -F ' ' '{print $2}'`
for p in ${pid[@]};
do
	kill -9 $p
done

# 删除目录
if [ -d /root/Jenkins/production ];
then
	rm -rf /root/Jenkins/production
fi

# 创建新部署目录
mkdir -p /root/Jenkins/production

# 进入部署目录
cd /root/Jenkins/production

# 拉取代码
git clone <url>

# 进入
cd <proj_path>

# 启动  BUILD_ID=dontKillMe  保证程序后台运行不杀死  和 nohub、& 一同使用
nohup python3 app.py &
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}