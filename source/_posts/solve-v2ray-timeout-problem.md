---
title: v2ray突然失效超时问题处理
date: 2020-05-22 13:46
tags:
- Tools
---
今天打开Google，发现不能用了，折腾了近两个小时，才不完美的解决了。

#### 问题描述
这个问题的直接表现就是，Google打不开。查了下v2ray客户端日志，发现如下记录：
```
2020/05/22 13:43:00 [Info] [1562828084] v2ray.com/core/transport/internet/tcp: dialing TCP to tcp:1.1.1.1:11111
2020/05/22 13:43:01 [Warning] [1562828084] v2ray.com/core/app/proxyman/outbound: failed to process outbound traffic > v2ray.com/core/proxy/vmess/outbound: failed to find an available destination > v2ray.com/core/common/retry: [dial tcp 1.1.1.1:11111: i/o timeout dial tcp 1.1.1.1:11111: operation was canceled] > v2ray.com/core/common/retry: all retry attempts failed
```
*（保密起见，记录中ip地址和端口号已经替换成假的，下文同。）*

上述日志表明，连接远程v2ray服务端发生了网络IO超时。这意味着，要么服务器出故障了，要么网络出故障了。
那就一项一项排查呗，先检查服务器，再检查网络。

#### 服务器排查
第一步，检查一下服务器是否还在正常工作。一般情况下用服务器ip是否能够ping通作为判断标准。
```
ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1): 56 data bytes
64 bytes from 1.1.1.1: icmp_seq=0 ttl=50 time=205.611 ms
Request timeout for icmp_seq 1
64 bytes from 1.1.1.1: icmp_seq=2 ttl=50 time=205.445 ms
64 bytes from 1.1.1.1: icmp_seq=3 ttl=50 time=207.564 ms
```
可见，服务器本身是好的。

第二步，检查v2ray服务端端口是否正常工作。
在本地用tcping工具检查v2ray服务端的端口号，发现端口状态为“closed”。
```
# tcping 1.1.1.1 11111
1.1.1.1 port 11111 closed.
```
端口closed。似乎抓到背后问题的尾巴了！
顺手再查查其他端口比如80、443等常用HTTP端口，发现状态都是“open"。
那么，这里可以下一个初步结论：这个端口从本机访问不了了。
不过这种情况还无法直接定位到问题根源，需要继续排查。

第三步，检查服务器中v2ray程序是否启动。
```
# ps -ef | grep v2ray
  root      2988     1  0 14:02 ?        00:00:02 /usr/bin/v2ray/v2ray -config /etc/v2ray/config.json
```
第四步，检查服务器上v2ray监听端口是否开启。
```
# netstat -nap | grep 11111
tcp6       0      0 :::11111                 :::*                    LISTEN      2988/v2ray
```
经过以上两个步骤，可以看出v2ray服务端并没有什么问题，仍在正常工作中。
第五步，检查服务器防火墙配置，看看有没有禁用v2ray的监听端口。
```
# iptables --list
...
Chain IN_public_allow (1 references)
...
ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:6170 ctstate NEW,UNTRACKED
ACCEPT     udp  --  anywhere             anywhere             udp dpt:6170 ctstate NEW,UNTRACKED
...
```
可见，防火墙对v2ray监听端口也正确设置了放行规则。

经过以上检查步骤，断定服务器端配置没有问题。另外，由于本地电脑和服务器都是我一个人在用，也不存在别人改了我的配置，导致本机v2ray和服务端配置不一致的情况。

#### 网络排查
其实经过上面服务器端检查的第二步，已经能够说明很多问题了。网站服务可正常访问，ssh也能用，唯独v2ray服务受到影响，可见不是服务器机房网络的问题了，而是涉及该端口11111的流量，在传输过程中被ban了。这种情况就难以下确切结论了，只能想其他办法了。

#### 不断试错，最终解决
找不到根本原因，那就求助万能的搜索引擎吧，看看网友们怎么说。谷歌用不了就只能度娘了。打开百度输入关键词*”v2ray连接超时“*，竟然第一条就找到了一个貌似提供解决方案的blog，打开一看发布日期，”2020年5月20日“，哦哦，还就是这两天发布的，看来靠谱。该博文给出的方案是v2ray服务器端绑定ipv4监听地址，其实就是修改v2ray配置文件`/etc/v2ray/config.json`，在`inbound`节点中加上`"listen": "1.1.1.1"`这样的配置项，然后保存退出，重启v2ray。
按这个方案试了一下，不行。 回头看博文下面的评论，讨论挺热烈，大家都说方案跑不通。
再翻翻其他博文，也都说的云里雾里。

实在没辙了，先换个端口试试吧。
修改服务器端v2ray配置文件，端口号由原来的11111改为22222。
```
# vim /etc/v2ray/config.json
...
"inbound": {
    "port": 11111, <------这里修改端口
    "protocol": "vmess",
    "listen":"12.34.56.78",
...
```
修改后，同步修改本机v2ray客户端配置里的端口号。另外别忘了在服务器防火墙里配置开放这个端口。
再次打开Google测试，竟然可以访问了！

**喜大普奔！**

冷静下来后，再次检查本机v2ray日志：
```
2020/05/22 14:08:11 [Info] [713431801] v2ray.com/core/app/dispatcher: default route for tcp:www.google.com:443
2020/05/22 14:08:11 [Info] [713431801] v2ray.com/core/transport/internet/tcp: dialing TCP to tcp:1.1.1.1:22222
2020/05/22 14:08:11 tcp:127.0.0.1:63193 accepted tcp:www.gstatic.com:443 [proxy] 
2020/05/22 14:08:11 tcp:127.0.0.1:63192 accepted tcp:www.google.com:443 [proxy] 
2020/05/22 14:08:11 [Info] [3172428981] v2ray.com/core/proxy/vmess/outbound: tunneling request to tcp:www.gstatic.com:443 via tcp:1.1.1.1:22222
2020/05/22 14:08:11 [Info] [713431801] v2ray.com/core/proxy/vmess/outbound: tunneling request to tcp:www.google.com:443 via tcp:1.1.1.1:22222
```
发现跟远程服务器端的tunnel通道已经成功建立了。
最后总结一句话解决办法： **换端口**。

#### 问题反思
这次问题算是临时得到了解决。至于为什么原来的端口不能用了，怀疑是跟网络运营商，或是国际线路节点有关，可能是因为一些不能明说的原因比如BT下载等，这个端口或端口区间被ban了。这种情况也没办法说理去，只能换端口了。下次要是遇到类似的情况，估计还得换端口。所以说这是不完美的解决办法。