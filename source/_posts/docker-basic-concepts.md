---
title: Docker简介
date: 2020-01-12 13:59:10
tags: 
- Docker
---

## Docker 基本概念
理解Docker, 必须先搞懂以下三个基本概念:
* ***镜像(Image)***: 相当于一套root文件系统。
* ***容器(Container)***: 容器是镜像运行的实体，实际上就是一个普通进程。
* ***仓库(Repository)***: 存储Docker镜像的中心仓库。比如Docker Hub就是公共的镜像仓库。

容器和镜像的关系类似于面向对象语言的类和实例。

## Docker 结构图
Docker采用C/S模式管理远程Docker容器。
Docker容器可被创建、启动、停止、删除、暂停。
Docker容器通过Docker镜像来创建。

![Docker运行结构图](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200112212304-731097.png)

