---
title: iptables常用配置
date: 2020-04-02 18:39:23
tags:
- Tools
---

### iptables初始设置
这是一份来自网友[清园](https://www.cnblogs.com/kreo/p/4368811.html)的iptables初始设置清单，供**有选择地**参考：
```shell
#查看iptables现有规则
iptables -L -n
#先允许所有,不然有可能会杯具
iptables -P INPUT ACCEPT
#清空所有默认规则
iptables -F
#清空所有自定义规则
iptables -X
#所有计数器归0
iptables -Z
#允许来自于lo接口的数据包(本地访问)
iptables -A INPUT -i lo -j ACCEPT
#开放22端口
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
#开放21端口(FTP)
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
#开放80端口(HTTP)
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
#开放443端口(HTTPS)
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
#允许ping
iptables -A INPUT -p icmp --icmp-type 8 -j ACCEPT
#允许接受本机请求之后的返回数据 RELATED,是为FTP设置的
iptables -A INPUT -m state --state  RELATED,ESTABLISHED -j ACCEPT
#其他入站一律丢弃
iptables -P INPUT DROP
#所有出站一律绿灯
iptables -P OUTPUT ACCEPT
#所有转发一律丢弃
iptables -P FORWARD DROP
```

### iptables常用规则
#### 1. 删除现有规则
```shell
iptables -F
```
#### 2. 阻止某个特定ip
比如，要阻止*101.101.101.101*这个ip的访问：
```shell
iptables -A INPUT -s 101.101.101.101 -j DROP
```

#### 3. 只允许内网访问某端口
比如，对于*mysql*默认端口*3306*,只能从内网访问：
```shell
iptables -A INPUT --dport 3306 -s 192.168.1.0/24 -j ACCEPT
iptables -A INPUT --dport 3306 -j DROP
```
更多规则可参考[这篇文章](https://www.jianshu.com/p/ee4ee15d3658)。

### iptables规则的保存
首先确保启用`iptables`服务：
```shell
systemctl enable iptables.service
systemctl start iptables.service
```
然后使用
```shell
service iptables save
```
命令把规则保存到`/etc/sysconfig/iptables`文件里。这样这样当计算机重启时，可自动重新加载。
