---
title: celery和redis交互全流程解析
tags: celery
categories: python
date: 2020-06-03 23:51:04
---
celery 和 redis 之间交互的基本原理
<!-- more -->
```text
 celery 和 redis 之间交互的基本原理：
1、当发起一个 task 时，会向 redis 的 celery key 中插入一条记录。
2、如果这时有正在待命的空闲 worker，这个 task 会立即被 worker 领取。
3、如果这时没有空闲的 worker，这个 task 的记录会保留在 celery key 中。
4、这时会将这个 task 的记录从 key celery 中移除，并添加相关信息到 unacked 和 unacked_index 中。
5、worker 根据 task 设定的期望执行时间执行任务，如果接到的不是延时任务或者已经超过了期望时间，则立刻执行。
6、worker 开始执行任务时，通知 redis。（如果设置了 CELERY_ACKS_LATE = True 那么会在任务执行结束时再通知）
7、redis 接到通知后，将 unacked 和 unacked_index 中相关记录移除。
8、如果在接到通知前，worker 中断了，这时 redis 中的 unacked 和 unacked_index 记录会重新回到 celery key 中。(这个回写的操作是由 worker 在 “临死” 前自己完成的，所以在关闭 worker 时为防止任务丢失，请务必使用正确的方法停止它，如: celery multi stop w1 -A proj1)
9、在 celery key 中的 task 可以再次重复上述 2 以下的流程。
10、celery 只是利用 redis 的 list 类型，当作个简单的 Queue，并没有使用消息订阅等功能


--------------

1、需要配置celery的backend，backend的作用就是存储任务执行结果。
2、当celery key中的任务成功被worker认领并且执行成功，celery会向配置好的bachend中，写入以task_id为key。
3、所以，可以通过查看bachend中是否有以task_id为key的对象来判断task是否执行成功。
```
<br><br>{% btn large center, 向博主反馈问题, <https://github.com/wongqingbin/blog/issues> , fas fa-paper-plane %}
