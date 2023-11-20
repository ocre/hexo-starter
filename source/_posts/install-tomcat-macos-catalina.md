---
title: Mac下安装tomcat
date: 2020-09-28 16:45
tags:
- Tools
---

> 提示：Tomcat依赖Java运行环境，没装java的先安装java

1. 从[Tomcat官网](https://tomcat.apache.org/download-native.cgi)下载所需版本。因为是Mac系统，我们选择*.tar.gz*后缀或*.zip*后缀的。
2. 下载完成后Finder进入下载文件夹（默认为~/Downloads），双击所下载的tar.gz压缩文件，Mac会自动解压并创建同名目录，比如，我的是`apache-tomcat-8.5.58`。
3. 解压缩之后，其实就算安装完毕了。可直接运行`bin/startup.sh`启动tomcat。我比较追求完美，把tomcat移动到了专门安装软件的`/usr/local`目录下，具体操作见后续步骤。
4. 移动tomcat至`/usr/local`目录
```shell
sudo mv ~/Downloads/apache-tomcat-8.5.58 /usr/local/
```
5. 创建软连接
```shell
sudo ln -s /usr/local/apache-tomcat-8.5.58 /Library/Tomcat
```
6. 测试启动、关闭脚本
终端直接输入启动脚本并执行，如下：
```shell
/Library/Tomcat/bin/startup.sh
```
关闭脚本跟启动脚本在同一个目录下，名字为`shutdown.sh`。

7. 验证安装。 
打开 `http://localhost:8080/`，看到Tomcat页面则说明安装成功。