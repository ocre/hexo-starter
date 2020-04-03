---
title: centos7安装并启用iptables
date: 2020-04-02 19:02:31
tags:
- Tools
---

centos从7之后默认使用*firewalld*作为防火墙配置服务, 很多Linux老手不熟悉，还是喜欢原汁原味的*iptables*。这里整理一下如何把*firewalld*换回*iptables*。

### 安装iptables-service
首先检查是否安装了*iptables*，如果没有安装，需要先安装：
```shell
yum install -y iptables
```
若已经安装了，先升级：
```shell
yum update iptables
```
再安装*iptables-service*:
```shell
yum install -y iptables-service
```

### 停用自带的firewalld
```shell
systemctl stop firewalld
systemctl disable firewalld
```

### 开启iptables-service
```shell
systemctl enable iptables.service
systemctl start iptables.service
```

OK，接下来就可以用*iptables*愉快地玩耍了。更多iptables设置请参考{% post_link iptables-common-configuration 这篇文章 %}。