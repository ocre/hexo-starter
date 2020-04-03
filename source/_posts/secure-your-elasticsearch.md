---
title: Elasticsearch安全设置
date: 2020-04-03 11:55:32
tags:
- Elasticsearch
---

经常在网上看到因Elasticsearch服务裸奔造成的安全事件，比如某婚庆网站因Elasticsearch数据暴露在公网上被曝光，我本来不以为意，没想到自己的一台小服务器今天也中招了。
```shell
# curl -XGET 192.168.1.11:9200/_cat/indices
yellow open locationkeywordv1     XH3c3vSWRAWOeAD9ZRDUKA 5 1 93 0 147.4kb 147.4kb
yellow open nightlionsecurity.com X_uOYfJ9ThitglEA17LzbA 5 1  0 0    955b    955b
```
攻击者删除了我几乎所有的索引数据，还嚣张地以用他的网站域名*nightlionsecurity.com*建了一个新索引。

看来侥幸心理要不得，安全问题随时都不能马虎。那么，如何保障Elasticsearch服务的安全呢？
今天只说一条：禁止外网访问Elasticsearch。

### 禁止外网访问Elasticsearch
首先要做的就是修改Elasticsearch的配置文件。
#### 1. 将Elasticsearch绑定到内网地址
修改配置文件*elasticsearch.yml*，设置固定的内网IP：
```
network.host: 192.168.1.11
```

然后需要禁止从外网访问Elasticsearch。可以从以下三方面入手：
#### 2. 防火墙禁止Elasticsearch端口访问
Elasticsearch节点暴露了三个默认端口：
* 9200： 集群对外访问端口
* 9300： 集群内部通信端口
* 5601：Kibana的对外访问端口

我们需要做的，就是限制直接从外网访问这几个端口。如果已经修改了默认端口，限制方法相同。我们拿9200端口举例，看一下如何限制访问。

在这里我们把目标明确一下：外网无法访问9200端口，内网同一网段可以访问。
假定我的内网IP为*192.168.1.30*。按以下步骤操作
```shell
# 允许内网同一网段ip过来的请求
iptables -A INPUT -p tcp --dport 9200 -s 192.168.1.0/24 -j ACCEPT
# 拒绝其他请求
iptables -A INPUT -p tcp --dport 9200 -j DROP
# 
```
其他两个端口9300和5601按类似步骤操作即可。
配置完成后，可使用以下命令检查规则顺序：
```shell
iptables -L -n
```
最后，这里设置的规则只是临时的，重启后就失效了。如果想重启后继续生效，需要执行以下命令将规则保存起来：
```shell
service iptables save
```
注意，若执行 `service iptables save`时提示不存在`save`指令等情况，说明没有安装*iptables-service* 服务。需要先{% post_link centos7-enable-iptables 安装iptables-service %}后再执行保存。
