---
title: 一次性搞定Git中忽略.DS_Store文件
date: 2020-08-19 19:12:03
tags:
- Tools
- Git
---

.DS_Store文件是Mac系统用来存储当前文件夹的显示属性元数据的，如图标位置等设置。.DS_Store广泛存在于每个文件夹下，一般供Finder、Spotlight使用。而对于基于Git托管的代码仓库来说，.DS_Store就是无用数据了。我们需要在Git中忽略它们。

忽略.DS_Store文件，我们通常有两种选择：

#### 1. 当前项目忽略
在当前项目根目录的.gitignore文件中添加如下一行即可:
```shell
# vim .gitignore
**/.DS_Store
```
.gitignore文件常用规则可以{% post_link common-rules-about-gitignore-file 看这里 %}。

对于已经在Git版本控制的中.DS_Store，希望Git能够忽略，但不删除本地文件，需要在terminal中输入以下命令：

```shell
git rm -r --cached .DS_Store
```
Git如何屏蔽已经加入版本控制的文件可以{% post_link gitignore-files-already-under-git-version-control 看这里 %}。

这种方式每当开新项目时都要配置，用起来比较麻烦。

#### 2. 全局忽略
首先创建一个 ~/.gitignore_global文件：
```shell
touch ~/.gitignore_global
```
然后在文件中添加如下内容：
```shell
# vim ~/.gitignore_global
**/.DS_Store
```
最后，将.gitignore_global作为Git的全局忽略配置文件：
```shell
git config --global core.excludesfile ~/.gitignore_global
```
这种方式一劳永逸，不用每个项目都单独配置了。
