---
title: git修改用户名密码
tags: git
categories: Git
date: 2020-05-26 22:57:23
---
用户使用git时的使用免密拉取和上传，修改免密设置，取消免密设置
<!-- more -->

## 第一次输入密码之后自动免密

```bash
# git 默认即支持首次输入用户名密码，后续自动记忆，使用免密设置
```

## 取消免密设置

```bash
git config --global --unset credential.helper store
# 使用 --global 参数则全局生效，不适用则针对当前目录下的.git生效
```

## 设置免密

```bash
git config --global credential.helper store
# 使用 --global 参数则全局生效，不适用则针对当前目录下的.git生效
```

## 适用场景

```bash
# 大多公司的账户密码考虑到安全性，隔段时间便会失效，需更改密码
# 这个时候大多git默认已经记忆上次用户名和密码，且不用跳出输入用户和密码的弹窗

# 1. 取消免密功能，使它能正确弹出用户名和密码框
git config --global --unset credential.helper store
# 2. 使用git拉取或提交代码，相当于首次提交 会自动弹出用户名密码弹窗，输入即可
# 3. 再次打开免密功能，记忆第二步的用户名和密码
git config --global credential.helper store
```

## win图形化修改git提交密码

```bash
# 设置路径
# 控制面板 --> 用户账号 --> 凭据管理器 --> windows凭据 --> 修改对应的git地址的用户名和密码后保存即可
```

<br><br>{% btn large center, 向博主反馈问题, <https://github.com/wongqingbin/blog/issues> , fas fa-paper-plane %}
