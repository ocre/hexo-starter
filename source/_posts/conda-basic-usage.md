---
title: Conda简介及基本用法
date: 2020-02-18 13:59:10
tags: Conda,Python
---

# 什么是Conda？

Conda是一种通用包管理系统，旨在构建和管理任何语言和任何类型的软件。Conda主要提供包管理和环境管理两种能力。包管理与pip的使用类似，环境管理则允许用户同时安装多个版本的python，并支持快速切换。

# Conda常用命令

## Conda包管理

1.查看已安装的所有包: conda list
2.查看已安装的特定包：conda list scrapy
3.搜索云上的包: conda search scrapy
4.安装包: conda install scrapy
5.一次安装多个包: conda install numpy pandas scipy
6.安装固定版本的包: conda install numpy=1.10
7.从指定频道安装包: conda install scrapy -c conda-forge
8.升级一个包: conda update scrapy
9.升级全部包: conda upgrade --all

## Conda环境管理

1.查看所有虚拟环境： conda env list
2.激活特定环境：activate env_name
3.退出环境：deactivate env_name
4.创建虚拟环境：conda create -n env_name <list of package names>
5.创建虚拟环境并指定特定Python版本：conda create -n env_name python2=2.7
6.删除虚拟环境：conda env remove -n env_name