---
title: Finder
tags: mac
categories: Mac
references:
  - name: null
    url: null
date: 2020-05-17 02:04:43
---
Finder 显示与隐藏 隐藏文件(.文件)
<!-- more -->
## 命令行永久改变显示方式
```bash
# 显示
defaults write com.apple.finder AppleShowAllFiles -bool true; killall Finder
# 隐藏
defaults write com.apple.finder AppleShowAllFiles -bool false; killall Finder
# PS: 输入完命令回车后，需重启Finder：左上角的苹果标志-->强制退出-->Finder-->重新启动
```
## 临时显示/隐藏快捷键
```bash
command + shift + .
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}