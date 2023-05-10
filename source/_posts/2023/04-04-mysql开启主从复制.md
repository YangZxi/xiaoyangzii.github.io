---
layout: post
title: mysql开启主从复制
date: 2023-04-04 13:42:28
tags: 
  - mysql
---


## 原理
mysql主从复制主要是通过监听master（主库）的binlog日志，slave（从库）的IO Thread将所有改动操作写入relay log日志，然后在slave中的SQL Thread执行对应的sql语句来实现的

1. Master主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中
2. 从库读取主库的二进制日志文件Binlog,写入到从库的中继日志Relay Log
3. slave重做 relay 日志中的事件，将改变反映它自己的数据

## 实现步骤
1. 准备两台数据库服务器
2. 
