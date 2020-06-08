---
layout: page
group: group-adb
order: 3
title: 3. apk安装&卸载
# short_title: 3. apk安装&卸载
sidebar: [group-adb, blogger, toc]
date: 2020-05-17 20:35:53
---

## 安装(adb install)
```bash
# 普通安装
adb install <apk_path>

# 覆盖安装
adb install -r <apk_path>

# 安装debug包
adb install -t <apk_path>

# 降级安装
adb install -d <apk_path>  # 只针对debug包，对release包无效

# 针对特定设备安装(多设备同时连接电脑时)
adb -s <devices_id> install <apk_path>
```

## 卸载(adb uninstall)
```bash
# 普通卸载
adb uninstall <package_name>  # 例如: com.jd.xxx

# 针对特定设备卸载(多设备同时连接电脑时)
adb -s <devices_id> uninstall <package_name>  # 例如: com.jd.xxx
```

## 自动化下的安装卸载(pm命令)
```bash
# 安装apk
pm install <device_apk_path>  # device_apk_path为手机路径 /sdcard

# 卸载apk
pm install <package_name>    # 例如: com.jd.xxx
```

{% btn large center, pm 更多用法, /adb/pm/ %}
