---
title: CentOS 7 安装 JDK
tags: jdk
categories: Linux
date: 2020-05-20 22:12:16
---
CentOS 7 安装 JDK1.8 并配置环境变量
<!-- more -->

## yum安装jdk
```bash
yum install java-1.8.0-openjdk*
```

## 配置环境变量
```bash
vim ~/.bash_profile

# set java environment
# JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64
# PATH=$PATH:$JAVA_HOME/bin
# CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# export JAVA_HOME  CLASSPATH  PATH

source ~/.bash_profile
```

## 验证
```bash
java --version
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}