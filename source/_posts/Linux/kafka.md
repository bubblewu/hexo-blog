---
title: CentOS 7 安装 kafka
tags: kafka
categories: Linux
date: 2020-05-20 22:27:38
---
CentOS 7 安装 kafka，简单配置使用kafka
<!-- more -->

## 安装
```bash
# 下载安装包
wget http://mirror.bit.edu.cn/apache/kafka/2.2.0/kafka_2.12-2.2.0.tgz
# 解压安装包
tar -xzf kafka_2.12-2.2.0.tgz
```

## 启动
```bash
# 启动zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties
# 启动kafka
bin/kafka-server-start.sh config/server.properties
```

## 测试
```bash
# 创建topic
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 -- replication-factor 1 --partitions 1 --topic test
# 列出topic
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
# 发送消息
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
# 接收消息
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 -- topic test --from-beginning
```

## 启动zookeeper
```bash
# 1
bin/zookeeper-server-start.sh config/zookeeper.properties
# 2 守护进程启动方式 防止挂掉
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
```

## 启动kafka
```bash
# 1
bin/kafka-server-start.sh config/server.properties
# 2 守护进程启动方式 防止挂掉
bin/kafka-server-start.sh -daemon config/server.properties
```

## 查看topic列表
```bash
bin/kafka-topics.sh --list --zookeeper localhost:2181
```

## 创建topic
```bash
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

## 生产者
```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

## 消费者
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

## 关闭kafka
```bash
bin/kafka-server-stop.sh
```

## 关闭zookeeper
```bash
bin/zookeeper-server-stop.sh
```

<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}