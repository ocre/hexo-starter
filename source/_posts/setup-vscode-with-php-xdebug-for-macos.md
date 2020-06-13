---
title: MacOS中搭建VS Code + XDebug的PHP调试环境
date: 2020-04-24 11:48:03
tags:
- Tools
---

笔者近期在`MacOS Catalina`环境下搭建了`VS Code`+`XDebug`的调试环境，用起来体验还不错。首先交代一下我电脑的环境：
```
System: MacOS Catalina 10.15.4
Editor: Visual Studio Code 1.44.2
Language: PHP 7.4.4 (cli) (built: Mar 19 2020 20:12:27) ( NTS )
```

搭建步骤如下：

### 安装并配置XDebug
#### 1. 安装XDebug
首先，遵循[XDebug官网](https://xdebug.org/docs/install)指示，安装XDebug。
这里官方建议使用`pecl`包管理工具安装：
```shell
pecl install xdebug
```
也可选择从源码编译安装。安装步骤是标准的`configure -> make -> make install`, 这里不再赘述，具体请参考官方文档指示。

#### 2. 配置XDebug
这里主要是在php.ini文件中指定XDebug的动态链接库的位置、监听ip和端口等。
由于系统中可能存在多个`php.ini`文件，一不小心就改错文件了。因此首先需要定位到当前使用中的`php.ini`文件。这就需要使用命令`php --ini`，输出如下：
```shell
Configuration File (php.ini) Path: /usr/local/etc/php/7.4
Loaded Configuration File:         /usr/local/etc/php/7.4/php.ini
Scan for additional .ini files in: /usr/local/etc/php/7.4/conf.d
Additional .ini files parsed:      /usr/local/etc/php/7.4/conf.d/ext-opcache.ini
```
从上面的输出内容中可以看出来，我的`php.ini`文件位于`/usr/local/etc/php/7.4/php.ini`。

然后，在这个`php.ini`文件中添加`[xdebug]`配置段。我的配置如下：
```
; put lines below into your working php.ini
[xdebug]
zend_extension = /usr/local/lib/php/pecl/20190902/xdebug.so
xdebug.remote_enable = true
xdebug.remote_port = 9001
xdebug.profiler_enable = true
xdebug.remote_autostart = 1
xdebug.remote_host=localhost
xdebug.remote_log=/var/log/xdebug/xdebug.log
```
注意，`zend_extension`需指向你电脑里的`xdebug.so`所在位置。
xdebug默认端口是9000，由于我电脑里9000端口被`php-fpm`占了，这里把xdebug的端口改成了9001。
`remote_log`有时候还是很有用的，建议开启。

配置完成后，
### VS Code安装并配置PHP Debug插件
#### 1. 安装插件
点击vscode左侧边栏`Extensions`，打开搜索框，输入`PHP Debug`搜索，同名的插件有好几个，功能几乎没啥区别，我选择了带`felixfbecker.php-debug`标签的，因为它的配置文档比较详细一些。点击`install`安装即可。

#### 2. 配置插件
点击vscode左侧边栏`Run`，在打开的小侧栏里点击右上角的`⚙`齿轮图标，即可按提示新建或打开插件的配置文件`launch.json`。修改里边的端口号为你的`xdebug`监听端口号，其它配置可以不用改。这样就可以了。

### 测试一下
在vscode中随便打开一个php文件，打上断点（在代码行的左侧边栏点击一下，出现小红点即可）。然后执行它，就可以看到程序停在断点位置了。