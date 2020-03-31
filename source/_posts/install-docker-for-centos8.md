---
title: centos8安装docker
date: 2020-03-31 18:37:03
tags:
- tools
---

1. 安装依赖
```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
2. 安装docker engine community
```shell
yum install -y docker-ce docker-ce-cli containerd.io
```
此时，若报错：*requires containerd.io >= 1.2.2-3* ，那就先通过rpm安装装新版的 containerd.io：
```shell
dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
```
再安装docker-ce和docker-ce-cli：
```shell
yum install -y docker-ce docker-ce-cli
```
检查安装：
```shell
docker -v
```
3. 设置开机启动docker服务
```shell
systemctl enable docker
```
4. 启动docker服务
```shell
systemctl restart docker
```

Have fun!