---
title: Mac开启任何来源
tags: mac
categories: Mac
references:
  - name: null
    url: null
date: 2020-05-15 00:20:07
---
Mac在macOS系统下，系统偏好设置中的“安全与隐私”默认已经去除了允许“任何来源”App的选项，如果不开启会直接影响到无法运行一些第三方应用。
<!-- more -->

## 开启权限命令
```bash
# 显示
sudo spctl --status --master-disable
# 隐藏
sudo spctl --status --master-enable
```

![任何来源.png](https://gitee.com/wongqingbin/Frieza/raw/master/image/spctl.png)

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}