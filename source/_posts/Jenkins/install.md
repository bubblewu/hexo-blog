---
title: Mac安装并配置Jenkins
tags: jenkins
categories: Jenkins
date: 2020-05-24 01:00:05
---
Mac使用Homebrew包管理器安装并配置管理Jenkins
<!-- more -->
## mac安装Jenkins
```bash
# 1. Mac安装JDK并配置环境变量
# 2.安装Jenkins
brew install jenkins
brew services start jenkins  # 命令会自动设置为开机启动
brew services stop jenkins
brew services restart jenkins
# 局域网访问:
# 使用brew安装jenkins会避免很多其他安装方式产生的用户权限问题，但是会将httpListenAddress默认设置为127.0.0.1，这样我们虽然可以在本地用localhost:8080访问，但是本机和局域网均无法用ip访问。解决办法为修改两个路径下的plist配置。并重启
# ～/Library/LaunchAgents/homebrew.mxcl.jenkins.plist
# /usr/local/opt/jenkins/homebrew.mxcl.jenkins.plist
# 将上面两个plist中的httpListenAddress后的ip地址，修改为本机IP或者0.0.0.0即可。

# 取消开机启动
sudo launchctl unload -w /Library/LaunchDaemons/org.jenkins-ci.plist 
# 设置开机启动
sudo launchctl load -w /Library/LaunchDaemons/org.jenkins-ci.plist 
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}
