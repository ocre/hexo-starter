---
title: 一步步打造高效coding环境 - git的安装和配置
date: 2020-03-30 16:05:01
tags:
- Tools
- Git
---

### 安装git
从[git官方](https://git-scm.com/downloads)下载。
安装时一路next即可。

### 配置git
1. 配置user和email账号
```shell
git config --global user.name "your name"
git config --global user.email "your email address"
```
2. 创建SSH key
```shell
ssh-keygen -t rsa -C "your email address"
```
以上命令将在用户目录下生成一个隐藏目录`.ssh`，里边有`id_rsa`和`id_rsa.pub`两个密钥文件。这两个文件就是本机SSH的密钥对，前者是私钥，务必保密，后者是公钥，可以放心告诉其他人。

3. 关联GitHub
登录GitHub，点击右上角用户头像打开`settings` -> `SSH and GPG keys`页面，点击  `Add SSH Key`,将上一步生成的公钥`id_rsa.pub`文件内容拷贝到输入框，点`Add Key`保存即可。
