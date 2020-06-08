---
layout: page
group: group-adb
order: 2
title: 2. 连接调试
# short_title: 2. 连接调试
sidebar: [group-adb, blogger, toc]
date: 2020-05-17 16:02:45
---
## win
```bash
# 什么进程占用了 5037
netstat -ano | findstr "5037"
# 结果显示进程PID 21152（示例）

# 查看进程是哪个程序启动的这个进程（21152为进程PID）
tasklist | findstr "21152"
#结果显示 某个服务

# 杀进程
taskkill /f /pid 21152
```
## mac
```bash
# 什么进程占用了 5037
netstat -an|grep 5037

# 查看进程是哪个程序启动的这个进程（21152为进程PID）
lsof -i :5037

# 杀进程(PID为53067的进程)
kill -9 53067

# ps
ps -ef | grep 5037
```
