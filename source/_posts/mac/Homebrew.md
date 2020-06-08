---
title: Homebrew
tags: mac
categories: Mac
references:
  - name: null
    url: null
date: 2020-05-17 02:18:30
---
mac提供包管理器的安装和使用(类似Ubuntu的apt，Linux的yum、pkg)
<!-- more -->
## Homebrew安装
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
## 简单使用
```bash
brew list package
brew install <package_name>
brew uninstall <package_name>
```
## 其他软件
```bash
# 安装Cakebrew和LaunchRocket
brew cask install cakebrew
brew cask install launchrocket
# PS：注意 launchrocket 只能管理Homebrew安装的服务，不支持源码下载安装的服务
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}