---
title: terminal
tags: mac
categories: Mac
references:
  - name: null
    url: null
date: 2020-05-17 02:27:16
---
Mac终端terminal自动补全之忽略大小写
<!-- more -->
## 自动补全忽略大小写（terminal）
```bash
# 终端中输入以下命令：
$ nano .inputrc
# 在里面粘贴一下语句后, Control+O 保存
set completion-ignore-case on
set show-all-if-ambiguous on
TAB: menu-complete

# PS: 需重启终端生效
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}