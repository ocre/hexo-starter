---
title: Mac下安装配置maven
date: 2020-05-08 16:45
tags:
- Tools
---

在mac下安装maven，有两种可选方案：

### maven的安装
#### 方法1，通过官网压缩包安装
1. 访问[官网](http://maven.apache.org/download.cgi)下载maven压缩包，`.zip`或`.tar.gz`后缀的都可以。
2. 解压压缩包到你日常放置绿色软件的目录
3. `vim ~/.bash_profile`，添加`M2_HOME`环境变量:
```shell
export M2_HOME=/Users/xxx/Documents/maven/apache-maven-3.6.3
export PATH=$PATH:$M2_HOME/bin
```
注意把上边命令中的路径替换为你电脑上的路径。
`wq`保存退出后，执行以下命令使配置生效：
```shell
source ~/.bash_profile
```
4. `mvn -v`检查安装是否成功


#### 方法2，通过brew直接安装

brew安装很简单，不需要再配置`M2_HOME`等环境变量。

注意，brew 安装时会自动安装maven的依赖 openjdk，可能比较慢，如果已经通过非brew的方式安装了jdk，建议还是走官网压缩包安装的方式。

brew安装命令如下：

```shell
# 查找maven
brew search maven
# 查看版本信息和依赖包
brew info maven
# 安装
brew install maven
```

最后，使用`mvn -v`检查安装是否成功。


### 安装完成后的配置
一般我们需要配置本地仓库和加速镜像即可。所有配置项都在`settings.xml`文件中定义。
#### 配置本地仓库
修改`settings.xml`文件，配置以下内容：
```xml
<localRepository>/Users/xxx/maven_repo</localRepository>
```
#### 配置加速镜像
修改`settings.xml`文件，配置以下内容:
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

