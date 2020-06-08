---
layout: page
title: 4. 玩转pm命令
date: 2020-05-17 21:05:23
group: group-adb
order: 4
sidebar: [group-adb, blogger, toc]
---

## 获取设备app包名称列表
```bash
adb shell pm list package     # 列出安装在设备上应用的包名
```
## 获取设备app包名称列表, 只显示系统应用
```bash
adb shell pm list package -s  # 列出安装在设备上应用的包名
```
## 获取设备app包名称列表, 只显示三方应用
```bash
adb shell pm list package -3  # 列出安装在设备上应用的包名
```
## 列出应用包名及对应的apk名及存放位置
```bash
adb shell pm list package -f  # 列出安装在设备上应用的包名
```
## 列出应用包名及其安装来源
```bash
adb shell pm list package -i
```
## 查看APP详细信息
```bash
adb shell pm dump <package_name>  # 例如：com.jd.xxxx
```
## 安装apk
```bash
adb shell pm install <apk_path>  # 目标 apk 存放于 Android 设备上(，请用 pm install 安装)
```
## 卸载apk
```bash
adb shell pm uninstall <package_name>
```
## 清除APP数据和缓存
```
adb shell pm clear <package_name>
```
## 重置所有APP权限
```bash
adb shell pm reset
```

## 查看帮助文档
```bash
adb shell pm
```
