---
title: raw.githubusercontent.com拒绝连接
date: 2020-10-10 12:28:31
tags:
- Tools
---

今天安装一个来自github的脚本，需要从raw.githubusercontent.com下载安装，但是一直提示拒绝连接。
一般这种情况就是域名查询过程中被墙了。
解决思路很简单，找到这个域名的香港ip，直接在host文件中配置一下，绕过域名查询就可以了。

香港ip可以从这里获取: [https://site.ip138.com/raw.githubusercontent.com/](https://site.ip138.com/raw.githubusercontent.com/)
