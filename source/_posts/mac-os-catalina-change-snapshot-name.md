---
title: Mac OS Catalina修改默认截图名称
date: 2020-06-30 08:51:09
tags:
- tools
---

在Mac系统中，截图是一件极其简单的事。无需安装任何额外软件，直接使用`command+shift+4`就可以用光标框选任意屏幕大小进行截图了，截图会自动保存到桌面上。
用户操作简单，通常意味着Mac系统默默帮我们做了很多事情。如果是中文系统，默认截图文件命名为类似**“截屏 2020-06-30 上午 08.54.32”**这样的格式，对于轻微强迫症的我来说，**"截屏"**两个汉字做前缀稍微觉得不喜，可以按如下命令修改为**“snapshot_”**:
```shell
defaults write com.apple.screencapture name "snapshot_"
```
这样以后的截图文件名就改为**“snapshot_2020-06-30 上午 08.54.32”**这样的格式了。
如果对后面的日期格式也不满意，还可以去掉日期：
```shell
defaults write com.apple.screencapture "include-date" 0
```
这样截图文件命名将变成**“snapshot_1”**、**“snapshot_2”**，后面的数字按截图先后顺序依次递增。