---
title: ssh-keygen
tags: ssh-keygen
categories: Linux
date: 2020-05-20 08:05:10
---
ssh-keygen简单用法  
本文主要针对ssh-keygen配置GitHub Pages使用ssh免密部署  
ssh秘钥登录特点：1.安全；2.免输密码。
<!-- more -->

## 安装
```bash
# ssh-keygen为ssh自带命令，Mac、Linux、Ubuntu等都自带
```

## 检查是否配置过ssh服务
```bash
# mac、linux、ubuntu
cd ~/.ssh
ls
# id_rsa  id_rsa.pub  known_hosts
# 如果有id_rsa  id_rsa.pub，证明以前曾经配置过，如果忘记曾经配置过，请删除此两项
```

## 配置ssh
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# 后面全部enter，不选择任何选项，最后生成的秘钥为无密码密钥
```

## 查看公钥和私钥
```bash
# 查看公钥
cat ~/.ssh/id_rsa.pub

# 查看私钥
cat ~/.ssh/id_rsa
```

## 服务器配置公钥
```bash
# 以github为例
# 配置路径：Setting -> SSH and GPG keys -> New SSH key or Add SSH key
# title可以随便起（尽量和自己的私钥所在电脑相关联）
# Key:    # 复制id_rsa.pub文件的全部内容
# 最后保存即可
```

## 测试是否连通
```bash
# 以github为例
ssh -T git@github.com
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}