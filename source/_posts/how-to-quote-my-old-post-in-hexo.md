---
title: Hexo引用自己的文章
date: 2020-01-07 13:23:29
tags:
- Tools
---

用Hexo写Blog文章时，经常需要引用自己的另一篇文章，如何做呢？
可以使用Hexo自带的`post_link`标签。用法如下：
```
{% post_link url_slug show_title %}
```
其中，`url_slug`表示Hexo项目`source/_posts`目录中markdown文件的名字。比如，我有一篇Blog {% post_link hexo-with-netlify 使用netlify自动发布hexo博客 %}, 它对应的markdown文件是*hexo-with-netlify.md*, 则`url_slug`就是*hexo-with-netlify*。
`show_title`表示你想要在这里显示给用户看的文章标题，可以不用跟原博客标题保持一致。
拿刚刚这篇文章来说，我在引用它时，需要输入：
```
{% post_link hexo-with-netlify 使用netlify自动发布hexo博客 %}
```
