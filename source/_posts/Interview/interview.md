---
title: interview
tags: interview
categories: Interview
date: 2020-05-24 00:41:05
---
Interview
<!-- more -->

## 基础篇
```python
# 1. 冒泡排序
def bubble_sort(data):
    for i in range(len(data)-1, -1, -1):
        flag = False
        for j in range(i):
            if data[j] > data[j + 1]:
                data[j], data[j + 1] = data[j + 1], data[j]
                flag = True
        if not flag:
            break
        print('第{0}次排序: {1}'.format(len(data)-i, data))
    return data
```
```python
# 2. 装饰器单例
def singleton(cls, *args, **kwargs):
    _instance = {}
    def get_instance():
        if cls not in _instance:
            _instance.setdefault(cls, cls(*args, **kwargs))
        return _instance.get(cls)
    return get_instance
```
```sql
################ sql
# 添加
insert into tablename(id,name) values(1,w)
insert into tablename values(1,w)
# 更新
update tablename set 字段名='newvalue' where
# 删除
delete from tablename where
# 查找
select * from tablename where
# 分组
group by 字段名 having avg()
```
```bash
############# Linux
# 查看进程
ps -ef | grep 'kafka'  # 显示PID
# 杀死进程
kill -9 PID

# 实时查看日志后十行file.log
tail -n 10 -f file.log  # tail -f  == tailf

# 复制
cp -a oldfile newfolder

# 检查端口号是否被占用
netstat -anp | grep 8080

# mkdir 创建目录
mkdir a
mkdir -p a/b/c  # 创建多级目录时使用 -p 命令

# rm 删除目录、文件
rm -rf a/b/c  f  # 不提醒确认则删除     shell脚本中常用  r是目录也删除，递归删除
rm -r a/b/c     # 提醒用户是否确认删除  命令行使用

# cat 查看文件
# cat -n [文件名]  -n 显示行号

# cp  拷贝文件
cp A.txt b.txt
cp a b

# echo  $PATH

# grep
cat -n A.txt | grep filter  # 只显示filter内容
cat -n A.txt | grep -v filter  # -v不显示filter内容

# find
fine /root -name A.txt

# ps  查看进程PID
ps -ef | grep mongo

# kill -9 结束进程(根据PID)
kill -9 [PID]

# mv
mv a.txt /a/b

# netstat 检查端口是否被占用
netstat -anp | grep 8080

# yum search package
# yum install package
# yum uninstall package

# sed 修改文本
sed -e 's/oldstr/newstr/g' A.txt  # 's'字符串 'g'全局替换

# tar
tar -zcvf a.tar.gz /root/a  #归档打包
tar -zxvf a.tar.gz /root/a  #解压缩

# awk 处理带格式文件文本
kill -9 `ps | grep bash | awk ' ' '{print $1}'`  #  === kill -9 PID
# awk ' ' 空格分割字符串
# awk '{print $1}' 打印或取值第一个
# ` ` 代表执行代码块获取执行结果，优先执行

# nohub 不挂起进行 命令行保护进程启动程序

# head 从头开始查看文件前10行
head -n 10 a.log
# tail
tail -n 10 a.log  # 从末尾开始查看文件最后10行
tail -f a.log     # 实时查看末尾

# less
# more

# touch、vi 创建文件
touch a.txt
vi a.sh

# history  查看命令行的历史命令

# chmod  赋权限
chmod 777 A.txt
chmod -x A.txt
# x可执行权限 r只读 w写

# rz 上传  yum install lrzsz
# sz 下载
# scp 文件传输

# wc
wc -l a/b/A.txt  # 统计文件行数
# du
du -sh A.txt   # 查看文件大小
du -sh /a/b    # 查看目录大小
```
```text
    测试用例八大要素
用例编号
操作步骤
测试标题
重要级别
前置条件
测试输入
所属模块
预期结果
```
```text
  黑盒测试用例设计方法包括等价类划分法、边界值分析法、错误推测法、因果图法、判定表驱动法、正交试验设计法、功能图法、场景图法等。

  白盒子测试方法：（强度由低到高）语句覆盖、判定覆盖、条件覆盖、判定条件覆盖、条件组合覆盖、路径覆盖。

  测试人员经常用到的有等价类，边界值，场景法，因果图法。
```


<br><br>{% btn large center, 向博主反馈问题, https://github.com/wongqingbin/blog/issues , fas fa-paper-plane %}