---
title: CentOS 7 Yum安装低版本的MySQL（MySQL 5.6）
date: 2020-01-19 18:10:15
tags: Linux,MySQL
---

我有一台阿里云RDS数据库到期了，考虑到目前业务规模，续费的性价比太低，不如在自己的ECS云服务器上装MySQL再把数据迁移过来。为了避免数据文件迁移时产生兼容性问题，要尽量保证自建MySQL跟RDS上的MySQL的主版本保持一致。
我的RDS上MySQL是5.6版本，而Mysql Yum仓库默认已经升级到8.0版本了，在Yum安装前需要做一些额外设置。

## 添加MySQL Yum源
首先，从 [MySQL官方Yum仓库](https://dev.mysql.com/downloads/repo/yum/)选择适合当前CentOS版本的rpm包。

![1579442107075](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200119215509-263995.png)

下载到ECS云服务器上。
```shell
wget -i -c https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```
添加Yum仓库。
```shell
sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
```

## 设置默认安装版本
查询可安装的MySQL版本：
```shell
yum repolist all | grep mysql
```

![1579442778319](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200119220746-868577.png)

图中enabled状态的版本为Yum安装时的默认版本。如果默认版本不是5.6，需手工编辑`/etc/yum.repos.d/mysql-community.repo`文件来调整所需版本的启用状态，比如，要想安装5.6版本，则需要将以下代表5.6版本的代码段的enabled设置为1，同时将其他代码片段的enabled值置0。

```shell
# Enable to use MySQL 5.6
[mysql56-community]
name=MySQL 5.6 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/7/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

调整完毕后，可重新执行```yum repolist all | grep mysql```命令复查一下，看默认版本是否符合预期。

## 执行安装
MySQL默认版本设置检查无误后，执行以下命令即可安装MySQL：
```shell
sudo yum install mysql-community-server
```
检查MySQL版本号：
```shell
mysql -V
```
我这里输出为：
```shell
mysql  Ver 14.14 Distrib 5.6.47, for Linux (x86_64) using  EditLine wrapper
```
版本号为5.6.XX，符合预期，安装成功！

## 参考资料：

[A Quick Guide to Using the MySQL Yum Repository](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

