---
title: 使用git stash暂存和恢复工作进度
date: 2020-05-03 21:56:31
tags:
- Tools
---

我们有时候正在开发一个feature，突然同事插进来说发现一个重大bug，需要立即修复。但是我们的feature才编码一半，不能提交。这时候就轮到`git stash`闪亮登场了。
`git stash`命令可以让我们先把当前的修改内容暂存在本地，保持working directory干净状态，接着就可以切换分支去修bug了。修完bug后，再把分支切回来，使用`git stash pop`命令恢复之前的修改内容，继续我们的feature编码。
`git stash`命令支持调用多次，它有一个类似栈的概念，每调用一次会把当前未暂存的修改压栈，然后可以使用`git stash pop` 从栈顶拿出最近一次压栈的修改内容。

### git stash常见用法
* `git stash`  - 暂存当前修改
* `git stash save 'some message'` - 功能同`git stash`，可额外添加一个注释信息便于找回
* `git stash list` - 查看当前有哪些暂存的修改，可以列出stash_id，供后续恢复时使用
* `git stash pop` - 取出最新的修改内容
* `git stash pop --index`  - 恢复最新的修改内容到工作区和暂存区
* `git stash pop 'stash_id'` - 恢复到指定的stash_id对应的修改内容
* `git stash apply` - 功能同`git stash pop`但不删除刚从栈中取出的这个stash
* `git stash drop` - 删除最新的修改内容stash
* `git stash drop 'stash_id'` - 根据stash_id删除栈记录
* `git stash clear` - 清空stash栈（删除所有stash记录）

