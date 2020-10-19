---
title: Git手动打标签
date: 2020-10-02 10:33:21
tags:
- Tools
---

使用git很久了，以前都是直接用`git-flow`来管理git工作流，对标签tag的管理都由`git-flow`脚本封装好，自动完成了。这次因为项目特殊，需要每日手动打标签tag，特地记录一下。

##### git标签分类
git的标签分两种：轻量标签(lightweight) 和 附注标签(annotated)。轻量标签义如其名，仅仅是对某个commit的一个引用。附注标签则是存储于git数据库中的一个完整对象，包含了打标签者的名字、email、日期时间、以及标签信息，并且可以GPG签名验证。
一般我们建议创建附注标签。

##### 列举标签
```shell
git tag
```

##### 查看标签
```shell
git show v20201002-1
```

##### 创建标签
创建附注标签：
```shell
git tag -a v20201002-1 -m "new feature: dropbox support"
```
创建轻量标签：
```shell
git tag v20201002-1w
```

##### 删除标签
从本地仓库删除：
```shell
git tag -d v20201002-1
```
从远程仓库删除：
```shell
git push origin --delete v20201002-1
```