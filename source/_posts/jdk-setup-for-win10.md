---
title: 安装配置java
date: 2020-03-30 16:34
tags:
- tools
---

java的安装配置比较简单。步骤如下：
###  安装java
从[Oracle官网](https://www.oracle.com/java/)下载最新版本，直接安装即可。

### 配置java
右键“我的电脑“选择"属性"，打开左侧菜单左下方的”高级系统设置“，再点击”环境变量(N)...“，完成以下两个步骤:
1. 新建环境变量 JAVA_HOME
新建->变量名"JAVA_HOME"，变量值C:\Java\jdk1.8.0_05（即JDK的安装路径）
2. 设置环境变量 Path
编辑->变量名"Path"，在原变量值的最后面加上 %JAVA_HOME%\bin
然后，一路确定保存即可。

### 测试验证
打开 cmd，输入`java -version` 观察命令是否有效，以及版本号是否跟我们安装的版本一致。再输入`javac -version`检查命令是否存在，以及版本号是否正确。