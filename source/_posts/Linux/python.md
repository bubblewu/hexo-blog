---
title: CentOS 7 安装 python
tags: python
categories: Linux
date: 2020-05-20 22:16:46
---
CentOS 7 yum安装 python3.6
<!-- more -->

## 安装
```bash
# 安装依赖包
yum install -y openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel
# 下载安装包 
wget https://www.python.org/ftp/python/3.6.5/Python-3.6.5.tgz
# 解压安装包 
tar -xzvf Python-3.6.5.tgz
# 进入安装目录 
cd Python-3.6.5
# 编译预处理 
./configure --prefix=/usr/local/python
# 编译并安装 
make && make install
```

## 修改Linux的Python版本默认为Python3
```bash
# Linux默认Python为2.7
# 建立新安装的python3.6.5的软连接
$ ln -s /usr/python/bin/python3 /usr/bin/python3
```

## python3添加环境变量
```bash
vim ~/.bash_profile
#
# PATH=$PATH:$HOME/bin:/opt/python3.6.5/bin  # opt需修改为自己安装的Python所在目录
# export PATH
source ~/.bash_profile
```

## 验证
```bash
python3 --version

python --version
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}