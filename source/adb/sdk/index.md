---
layout: page
group: group-adb
order: 1
title: 1. SDK下载安装
# short_title: 1. SDK下载安装
date: 2020-05-17 14:59:42
sidebar: [group-adb, blogger, toc]
---
## 下载Android SDK
下载地址：[https://www.androiddevtools.cn](https://www.androiddevtools.cn/)  
官方地址：[http://developer.android.com/sdk](http://developer.android.com/sdk/)  
点击Android SDK工具，选择SDK，按不同系统下载对应版本

## 安装Android SDK
打开Android SDK文件夹，点击SDK Manager，只勾选platform-tools、build-tools、tools三个工具安装即可

## 环境变量配置
```bash
# win 编辑环境变量
ANDROID_HOME=<android-sdk的path目录路径>  # 新增ANDROID_HOME
PATH=$ANDROID_HOME/bin;$ANDROID_HOME/platform-tools;$ANDROID_HOME/build-tools;$ANDROID_HOME/tools

# mac 修改.bashrc或.zshrc(zsh使用者)
export ANDROID_HOME=<android-sdk的path目录路径>
export CLASSPATH=.:$ANDROID_HOME/bin;$ANDROID_HOME/platform-tools;$ANDROID_HOME/build-tools;$ANDROID_HOME/tools
```
