---
title: 在阿里云OS里安装nodejs
date: 2022-07-26 09:38:21
tags: 
- Linux
- node.js
---

## 安装Node环境

安装方式分两种: 一种是nvm安装多版本, 一种是使用二进制文件安装. 这里采用nvm方式安装.
步骤如下:

首先, 使用git拉取nvm代码:
```shell
git clone https://github.com/cnpm/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`
```
然后, 激活nvm:
```shell
echo ". ~/.nvm/nvm.sh" >> /etc/profile
source /etc/profile
```
激活后即可ls出所支持的node版本:
```shell
nvm list-remote
```
选择一个版本号执行安装, 安装命令如下:
```shell
nvm install v14.20.0
```
检查已安装版本:
```shell
nvm ls
```
切换到目标版本:
```shell
nvm use v14.20.0
```
查看当前node版本:
```shell
node -v
```

## 退出后找不到 node 及 npm 命令问题
退出云服务器再次登录后, 执行`node -v`, 会报错: `node: command not found`.
这时, 需要先`nvm use v14.20.0`一下, 然后查找一下node可执行文件路径:
```shell
whereis node
```
比如我的路径是: `/root/.nvm/versions/node/v14.20.0/bin/node`,
这里可以建立一个指向node可执行文件路径的软链接:
```shell
ln -s /root/.nvm/versions/node/v14.20.0/bin/node /usr/local/bin/node
```
这样就可以在重新登录后继续使用`node`命令了. 

`nvm`命令也需要按上述步骤处理一下.

#### 小尾巴

显然, 这种处理方式不完美, 每次切换到不同node版本后, 需要重新建一遍软链接, 指向当前node版本.

