---
title: Mysql8忘记密码解决办法
date: 2020-11-25 13:36:01
tags: Mysql
---

# Mysql8 忘记密码的解决办法

1. 先暂停服务
``` shell
# 首先暂停服务
net stop mysql

# 以绕过权限表的形式开启mysql服务
mysqld --console --skip-grant-tables --shared-memory
```
2. 开启服务后，打开一个新的命令窗口
``` shell
# 无需输入密码（在输入密码处可直接回车进入）
mysql -u root -p
```
3. 修改密码为空，注意`authentication_string`的值是空字符串
``` sql
use mysql;
UPDATE mysql.user SET authentication_string='' WHERE user = 'root';
```
4. 此刻允许下面sql可以看到密码以及重置为空
``` sql
SELECT host,user,authentication_string FROM mysql.user;
```
5. 关闭之前保留的那个控制台窗口和现在使用的这个控制台窗口一共关闭两个控制台窗口。然后再打开一个新的窗口，启动MySQL的服务。
``` shell
# 启动mysql
net start mysql

# 无需密码登录
mysql -u root -p
```
6. 修改密码
``` sql
ALTER user 'root' IDENTIFIED BY '123456';
```
7. 如果第六步的修改密码出错可以使用下面的sql
``` sql
ALTER user 'root'@'localhost' IDENTIFIED BY '123456';
```
