---
title: CentOS 7 时间时区调整
tags: timedatectl
categories: Linux
date: 2020-05-20 22:42:07
---
CentOS 7 调整时间为中国时区时间
<!-- more -->

## 启用NTP同步
```bash
# 安装ntp服务
yum install ntp
# 开机启动服务
systemctl enable ntpd
# 启动服务
systemctl start ntpd
# 更改时区
timedatectl set-timezone Asia/Shanghai
# 启用ntp同步
timedatectl set-ntp yes
# 同步时间
ntpq -p
```

## timedatectl 命令同步时区
```bash
# 读取时间  等同于 timedatectl status
timedatectl
# 设置时间
timedatectl set-time "YYYY-MM-DD HH:MM:SS"
# 列出所有时区
timedatectl list-timezones
# 更改时区
timedatectl set-timezone Asia/Shanghai
# 或   ln -sf /usr/share/zoneinfor/Asia/Shanghai /etc/localtime     
设置是否与NTP服务器同步 //yes或者no
timedatectl set-ntp yes
# 将硬件时钟调整为与本地时钟一致
hwclock --systohc --localtime 或 timedatectl set-local-rtc 1
# 注，硬件时钟默认使用UTC时间，因为硬件时钟不能保存时区和夏令时调整，修改后就无法从硬件时钟中读取出准确标准时间，因此不建议修改。修改后系统会出现警告。
# 将硬件时间设置成 UTC
hwclock --systohc --utc 或 timedatectl set-local-rtc 1
# 显示硬件时间：
hwclock --show
# 设置硬件时间：
hwclock --set --date ‘08/02/2012 12:00:00’
# 将硬件时间同步到系统时间：
hwclock --hctosys
# 强制把系统时间写入CMOS：
# clock -w
```

## 时钟概念
 - 在CentOS 6版本，时间设置有date、hwclock命令，从CentOS 7开始，使用了一个新的命令timedatectl。
 - （1）UTC  
整个地球分为二十四时区，每个时区都有自己的本地时间。在国际无线电通信场合，为了统一起见，使用一个统一的时间，称为通用协调时(UTC, Universal Time Coordinated)。
 - （2）GMT  
格林威治标准时间 (Greenwich Mean Time)指位于英国伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。(UTC与GMT时间基本相同，本文中不做区分)
 - （3）CST  
中国标准时间 (China Standard Time)【GMT + 8 = UTC + 8 = CST】
 - （4）DST  
夏令时(Daylight Saving Time) 指在夏天太阳升起的比较早时，将时钟拨快一小时，以提早日光的使用。（中国不使用）

 - 硬件时钟：  
RTC(Real-Time Clock)或CMOS时钟，一般在主板上靠电池供电，服务器断电后也会继续运行。仅保存日期时间数值，无法保存时区和夏令时设置。
 - 系统时钟：  
一般在服务器启动时复制RTC时间，之后独立运行，保存了时间、时区和夏令时设置。

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}