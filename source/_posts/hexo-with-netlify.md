---
title: 使用netlify自动发布hexo博客
date: 2019-12-08 20:30:01
tags:
- Hexo
---

使用hexo博客发布一篇博文的流程为：
```
hexo n "a new post"
hexo g -d
```
以上操作将博文的.md文件编译成.html文件，连同资源文件一起打包发布到网站空间里。对于.md源文件，默认是没有版本控制的。 为了防止丢失、误删，以及使用多个电脑管理博客，我们需要为hexo工程建一个单独的git仓库，并同步到github、coding等托管平台。实际使用中，除了上面列出来的两条命令外，还需要执行以下操作：
```
git add .
git commit -m "add a new post, bla bla bla"
git push
```

我正在尝试一种新方式，使用netlify来做持续集成和自动部署。
在source/_posts目录下新建一个.md文件用来写博文，可以用任何markdown编辑器来写，写完后直接`add commit push`即可。具体命令为：
```
git add .
git commit -m "add a new post, bla bla bla"
git push
```
这种新方式非常灵活，不需要hexo命令，也就不需要在每台电脑上安装hexo以及所依赖的node.js, npm等。只需要电脑上有git即可操作。

netlify配置步骤，可参考官方blog：[A Step-by-Step Guide: Hexo on Netlify](https://www.netlify.com/blog/2015/10/26/a-step-by-step-guide-hexo-on-netlify/)

