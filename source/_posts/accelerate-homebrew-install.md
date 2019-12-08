---
title: Homebrew每次装软件都卡在更新界面怎么办？
date: 2019-12-08 11:15:03
tags:
---

自从用上Mac，Homebrew就成了一个绕不开的工具。Homebrew是Mac的软件包管理工具，作用相当于CentOS上的yum。Mac上的好多软件，都需要用Homebrew安装。基本上使用`brew install <packageName>`或者`brew cask install <packageName>`就能一步到位装好软件了。但是头疼的是，我在运行`brew install`的时候，经常会提示`Updating Homebrew...`，然后卡住好几分钟，也不知道后台实际上在干些什么，体验很不好。截图如下：
![brew install 时卡住了。。。](https://tva1.sinaimg.cn/large/006tNbRwly1g9o9r4bbrdj30ta026dfs.jpg)

一般遇到这种情况，直接使用`control + C`快捷键就可以打断更新，直接进入后续的install环节。
![control + C打断更新](https://tva1.sinaimg.cn/large/006tNbRwly1g9oa2txfudj30v605mt9b.jpg)
`control + C`一时爽，可是如果每次都要这样操作，却有违俺样的美学。所谓知其然，还要知其所以然。于是收集各种网上资料，并结合自己的实践测试了一下，对卡顿原因有了一定的了解。

## 问题分析

检查Homebrew脚本代码 `/usr/local/Homebrew/Library/Homebrew/brew.sh`可以发现，Homebrew在执行install操作时，默认会先去更新自己。

![](https://tva1.sinaimg.cn/large/006tNbRwly1g9ojpggsjrj30v40c0ac4.jpg)

而更新操作，实际上是调用git pull从Homebrew位于github的官方镜像仓库拉取最新的版本到本地仓库（即Homebrew的安装目录）。如果github被墙或者访问速度太慢，就会出现卡顿现象。
![check official repository](https://tva1.sinaimg.cn/large/006tNbRwly1g9oapkonfej30ym09q0u7.jpg)

可以使用`brew update --verbose`测试一下实际的更新速度，感受一下实际的卡顿：

![](https://tva1.sinaimg.cn/large/006tNbRwly1g9obm2zg75j312g0nudhq.jpg)



## 解决方法：

弄明白原因后，就可以对症下药了。

总的来说，要解决这个卡顿问题，有三种方法： 一是简单粗暴直接禁用brew update，二是使用国内镜像源，三是延长update的检查时间间隔。

### 方法1: 禁用brew update
设置环境变量`HOMEBREW_NO_AUTO_UPDATE`即可。
这里需要区分一下shell种类，可以使用`echo $SHELL`命令查看。
针对`/bin/zsh`, 执行如下命令：
```
echo 'export HOMEBREW_NO_AUTO_UPDATE=true' >> ~/.zshrc
source ~/.zshrc
```
针对`/bin/bash`, 执行如下命令：
```
echo 'export HOMEBREW_NO_AUTO_UPDATE=true' >> ~/.bash_profile
source ~/.bash_profile
```
禁用之后，如果需要更新brew包，可以执行如下命令：
```
brew update && brew upgrade && brew cleanup ; say brew updated
brew update && brew upgrade brew-cask && brew cleanup ; say brew-cask updated
```
### 方法2: 更换更快的镜像源
可选的镜像源有：
- 清华：[git://mirrors.tuna.tsinghua.edu.cn/homebrew.git](git://mirrors.tuna.tsinghua.edu.cn/homebrew.git)
- 中科大：[http://mirrors.ustc.edu.cn/homebrew.git](http://mirrors.ustc.edu.cn/homebrew.git)
- Coding.net：[https://git.coding.net/homebrew/homebrew.git](https://git.coding.net/homebrew/homebrew.git)
这里我使用了清华的[镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)，操作命令如下：
```shell
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git
```
顺便也可以设置一下Homebrew二进制预编译包的镜像地址，具体需要通过设置环境变量`HOMEBREW_BOTTLE_DOMAIN`来实现。这里也需要区分一下shell版本：

如果使用 `/bin/zsh`， 则执行如下命令：
```
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```
而如果使用为`/bin/bash`，则执行如下命令：
```
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```
然后重新执行一下：
```
cd $home
brew update
```
装两个软件试试：
![brew_install_test](https://tva1.sinaimg.cn/large/006tNbRwly1g9oi1oqzw3j30km0akt90.jpg)
卡顿有了明显改善。:)

### 方法3: 延长自动update的检查间隔时间
通过阅读源码或帮助文档manpage，发现可以通过设置环境变量`HOMEBREW_AUTO_UPDATE_SECS`来控制检查更新的间隔时间。
![auto_update_secs_env_var](https://tva1.sinaimg.cn/large/006tNbRwly1g9oikrji0tj315i0me78p.jpg)

在近期发布的Homebrew 2.2.0版本，也提到了一个主要更新feature：

- [HOMEBREW_AUTO_UPDATE_SECS 的无操作情况明显更快，默认为 5 分钟（而不是 1 分钟）。](https://www.oschina.net/news/111734/homebrew-2-2-0-released)

这在homebrew/brew的代码中也得到了证实：

![update_secs](https://tva1.sinaimg.cn/large/006tNbRwly1g9oiwq89r3j30y40b8myx.jpg)

可见，Homebrew官方自己也认为这个时间设置的太短了没啥意义。

考虑到我们装软件是个低频操作，不会天天装软件，犯不着每次装软件都去检查一次更新。这个环境变量的取值可以设置的大胆一些，比如一个月，或着两周。设置方法跟本文中其他环境变量的方式相同，这里不再赘述。

注意，这种方法只是尽可能延迟检查更新的时间，达到减少更新次数的目的，并不能完全避免卡顿，所以显得比较鸡肋。个人更喜欢第二种方法，更换更快的镜像源。

