---
title: Scrapy开发环境搭建
date: 2020-02-18 11:49:51
tags: Scrapy,crawler,spider,Python
---

# 准备Python3运行环境
Scrapy基于Python语言编写，因此运行Scrapy需要先安装Python运行环境。所谓Python运行环境指可以编译执行Python代码的软件集合。已经安装了运行环境的请直接跳过。
目前常见的Python运行环境有三种方式：
1.直接安装[Python3](https://www.python.org/)
2.安装[Anaconda](https://www.anaconda.com/)
3.安装[Miniconda](https://conda.io/miniconda.html)

我选择安装Anaconda，因为相对于直接安装Python3，Anaconda提供了更简便强大的包管理和环境管理功能。
Anaconda是Python的一个科学计算发行版，支持 Linux, Mac和Windows系统，可以很方便地解决多版本python并存、切换以及各种第三方包安装问题。Anaconda内置了数百个Python标准库，装上Anaconda，就相当于把Python和一些如Numpy、Pandas、Scrip、Matplotlib等常用的库自动安装好了，使得安装比常规python安装要容易。因而建议直接安装Anaconda。直接从[官网]((https://www.anaconda.com/)下载最新版Anaconda社区版安装包安装即可。

当然，喜欢掌控一切的同学可以选择安装Miniconda。Miniconda可以看做是Anaconda的一个精简版。如果拿房子来比喻，Anaconda是精装房，Miniconda是毛坯房。Miniconda去掉了Anaconda内置的大量常用工具包，只提供一个较为纯净的Python运行环境和Conda（Conda是一种通用包管理系统，旨在构建和管理任何语言和任何类型的软件。[Conda简介及基本用法](./conda-basic-usage.html)）。需要什么包就自己装什么包。

装好Anaconda后，就可以继续安装Scrapy了。

# 安装Scrapy
按惯例，参照[Scrapy官网](https://scrapy.org/)的建议进行安装。官网提供了conda和pip两种安装方式，一条命令即可搞定。
```shell
# 选择conda方式安装
conda install scrapy -c conda-forge
```
```shell
# 选择pip方式安装
pip install scrapy
```
安装好Scrapy后，就可以创建Scrapy项目了。

# 创建Scrapy项目
首先进入将要存放Scrapy工程代码的目录，执行如下命令即可创建一个名为`tutorial`的工程。
```shell
scrapy startproject tutorial
```
我在执行上面命令的时候系统报错了，提示`Fatal error in launcher: Unable to create process using '"d:\bld\scrapy_1572360424769\_h_env\python.exe"`。改为执行以下命令即可：
```shell
python -m scrapy startproject tutorial
```
执行成功后，将在当前目录下创建一个新的`tutorial`目录，目录结构如下：
```
tutorial/
    scrapy.cfg            # Scrapy的部署配置文件
    tutorial/             # Scrapy项目的Python模块
        __init__.py
        items.py          # items definition file
        middlewares.py    # project middlewares file
        pipelines.py      # project pipelines file
        settings.py       # project settings file
        spiders/          # spider子模块
            __init__.py
```
接下来就可以编写自己的spider类了。请参考Scrapy官方的[Tutorial](https://docs.scrapy.org/en/latest/intro/tutorial.html#our-first-spider)
