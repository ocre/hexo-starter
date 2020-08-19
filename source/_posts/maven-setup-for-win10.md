---
title: 安装和配置maven
date: 2020-03-30 17:52:09
tags:
- Tools
---

安装完JDK，我们接下来安装maven。
maven是java生态系统下的最普及的包管理软件，可以对Java项目进行构建和依赖管理。

### 下载安装maven
*注意，maven是基于java构建的一个开源项目，因此安装maven之前需要先安装JDK。*

#### 下载

首先从[maven官网](https://maven.apache.org/download.cgi)下载最新版maven。

#### 安装
maven是绿色软件，无需安装，直接解压即可。建议将maven解压到专门保存可执行程序的目录，比如 *D:\Tools\apache-maven* 这样的。

### 配置maven
#### 配置环境变量
首先，新建环境变量 `MAVEN_HOME`，取值为: `D:\Tools\apache-maven`。（请根据你的maven实际解压目录调整）。
然后，编辑环境变量`Path`，追加`%MAVEN_HOME%\bin`。
最后，使用`mvn -version`来测试环境变量是否配置完成。

#### 配置本地仓库目录
maven本地仓库默认在C盘，为避免C盘空间不够，建议设置到其他磁盘分区。可如下操作：
首先，新建文件夹*D:\Tools\apache-maven\repository*用作本地仓库。
然后，修改配置文件 *D:\Tools\apache-maven\conf\settings.xml*的localRepository如下：
```xml
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>D:\Tools\apache-maven\repository</localRepository>
```
可执行 `mvn help:system` 命令检查配置是否生效。

#### 配置远程仓库
maven默认的中央仓库在国外，国内访问很慢。可以考虑使用阿里云的远程仓库来替代。
首先，在*settings.xml*文件的*mirrors*节点下增加如下内容：
```xml
<mirrors>
    <!-- aliyun -->
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```
可再次执行 `mvn help:system` 命令检查远程仓库配置是否生效。
