---
title: 命令行操作sqlite
date: 2020-04-04 11:03:32
tags:
- Tools
---

`sqlite`是世界上最广泛部署的数据库引擎。它无需服务器，仅仅需要一个数据文件，就能够实现自给自足、零配置且带事务支持的数据库引擎。因为轻便简单，是各种嵌入式程序的首选数据库。通过命令行操作`sqlite`也十分简单。最常用的命令列举如下：
1. 打开数据库
```shell
sqlite3 your_db_filename.sqlite3
```
`sqlite`数据库文件一般以*.sqlite3*或*.db*结尾。

2. 查看帮助
```shell
.help
```
3. 查看数据库表
```shell
.table
```
4. 查看建表语句
```shell
.schema your_table_name
```
5. 查询某个表的数据
```shell
select * from your_table_name;
```
注意这里的sql语句后面必须带分号。

同`select`一样，一般的标准sql语句都是支持的。