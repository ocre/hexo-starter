---
title: 阿里云RDS Mysql物理备份文件恢复到自建数据库
date: 2020-01-19 20:30:01
tags:
- MySQL
---

今天需要将Mysql数据从RDS迁移到ECS云服务器上自建Mysql中。因我的RDS已到期停机，无法通过mysqldump等命令导出数据，好在RDS给提供了数据库物理文件的每日全量备份，姑且用之。
整个操作流程可分为三个：下载解压物理文件备份， 使用Percona XtraBackup恢复数据，启动MySQL并校验数据。

## 下载解压备份文件
在RDS的备份恢复界面找到最新备份:

![1579445856935](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200119225737-71726.png)

获取下载地址并使用wget或curl下载到ECS机器上。下载得到的是一个tar.gz压缩包，使用如下命令解压缩：
```shell
tar -izxvf '<backup_file_name.tar.gz>' -C '<MySQL data dir>'
# for example，my command is:
# tar -izxvf /mnt/backup_data/db_backup/rds_data_20200118.tar.gz -C /var/lib/mysql
```
这里我直接解压到了默认的MySQL数据目录/var/lib/mysql.（我的MySQL是新装的，还没启动，因此默认数据目录下是空的）。

## 使用Percona XtraBackup恢复数据
首先，需要安装Percona XtraBackup。这里需要注意，不同的MySQL版本，需要不同的Percona XtraBackup版本。比如，MySQL 5.6及之前的版本需要[安装 Percona XtraBackup 2.3](https://www.percona.com/doc/percona-xtrabackup/2.3/installation/yum_repo.html#installing-percona-xtrabackup-using-downloaded-rpm-packages)，MySQL 5.7版本需要[安装 Percona XtraBackup 2.4](https://www.percona.com/doc/percona-xtrabackup/2.4/installation.html)，MySQL 8.0版本需要[安装 Percona XtraBackup 8.0](https://www.percona.com/doc/percona-xtrabackup/8.0/installation.html)。
安装完毕后，执行如下命令，恢复解压后的文件：
```shell
innobackupex --defaults-file=/var/lib/mysql/backup-my.cnf --apply-log /var/lib/mysql
```
若恢复成功，则会提示类似文字：

![恢复成功提示](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200119232144-778390.jpeg)

## 启动MySQL并校验数据
设置正确的文件属主：
```shell
chown -R mysql:mysql /var/lib/mysql
```
执行如下命令启动mysql：
```shell
service mysql restart
```
使用各种客户端软件连接MySQL，根据业务情况校验各数据库、各表的数据。

## 参考文档
* [RDS MySQL 物理备份文件恢复到自建数据库 - 阿里云](https://help.aliyun.com/knowledge_detail/41817.html)