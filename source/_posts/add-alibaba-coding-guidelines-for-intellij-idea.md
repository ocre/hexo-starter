---
title: intellij idea添加阿里巴巴编码规范插件
date: 2020-03-31 15:49:10
tags:
- tools
---

我们编写java程序都需要遵循一定的编码规范。阿里巴巴提供了一个优秀的IDEA插件，用来检测代码是否符合《阿里巴巴Java开发手册》里规定的编码规范。

### 安装插件
安装方法很简单：
依次打开IDEA -> File -> Settings -> Plugins， 然后在搜索框里搜索*alibaba*，第一个就是“Alibaba Java Coding Guidelines"，点击安装即可。安装完成后，重启IDEA生效。 

### 使用插件
在代码窗口右键，点击”编码规约扫描“即可启动扫描程序，对不符合阿里编码规范的地方会在底部*Event Log*窗口展示出来。