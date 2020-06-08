---
title: ipv6
tags: mac
categories: Mac
references:
  - name: null
    url: null
date: 2020-05-17 02:23:52
---
Mac系统关闭ipv6
<!-- more -->
## Mac修改网络时遇到无法修改，需先关闭ipv6
```bash
# command
networksetup -listallnetworkservices | sed 1d | xargs -I {} networksetup -setv6off {}
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}