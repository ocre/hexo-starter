---
title: 简单三步实现ssh免密登录
date: 2022-07-15 09:38:21
tags: 
- Linux
---

## 简单三步实现ssh免密登录

假设我们有两台Linux服务器A(172.22.170.149), B(172.22.170.148), 现在要配置A免密登录B. 操作步骤如下:

### 先人工登录一次
```shell
ssh root@172.22.170.148
```
第一次登录时, 会提示是否把对方机器加入当前机器的已知host列表里(类似白名单).  

```shell
The authenticity of host '172.22.170.148 (172.22.170.148)' can't be established.
ECDSA key fingerprint is SHA256:78SJbYxxxxxxxxxxxxxxxxxxxxxxxxwvB2F3up7VI.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```
输入`yes`继续.
```shell
Warning: Permanently added '172.22.170.148' (ECDSA) to the list of known hosts.
root@172.22.170.148's password: 这里输入密码
```
按提示输入密码即可成功登录服务器B.  出现类似以下提示即表示登录成功(这里是阿里云的ECS服务器).
```shell
Welcome to Alibaba Cloud Elastic Compute Service !

Last login: Fri Jul 15 09:40:47 2022 from 101.206.167.221
[root@iZ8vb0nljx0sx0od8wvle6Z ~]#
```

然后输入`exit`退出服务器B, 返回服务器A.
```shell
exit
```

### 在A服务器生成密钥对
```shell
ssh-keygen
```
出现交互选择信息时, 一路回车即可.

### 传输A服务的公钥到B服务器
```shell
ssh-copy-id 172.22.170.148
```
这里要再输入一次密码. 才能把公钥传过去. 

然后A服务器就可以ssh免密登录B服务器了.

### 验证一下
使用如下命令验证免密登录是否成功:

```shell
ssh root@172.22.170.148
```
 快来动手试试吧!