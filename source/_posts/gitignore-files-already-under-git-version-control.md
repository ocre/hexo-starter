---

title: 正确设置.gitignore忽略已经纳入版本控制的文件
date: 2020-04-23 18:59:02
tags:
- Tools
- Git
---

初建git项目时，考虑不完整，常常会将logs、cache等目录也纳入版本控制中。后续进入开发过程中，才意识到要将这些目录屏蔽掉。于是想到`.gitignore`文件。
简单设置.gitignore 文件后，执行git push，发现被屏蔽的目录下文件仍然赫然在列，忽略设置并未生效。咋回事呢？
#### 不生效原因
被纳入版本管理的文件，在git中会有缓存，即使声明了要ignore掉也不行
#### 解决办法
知道是缓存搞怪，清掉本地缓存重新push就ok了。具体操作如下：
```shell
git rm -r --cached .
git add .
git commit -m "update .gitignore and clear local cache"
git push
```
