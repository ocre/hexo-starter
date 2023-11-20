---
tetitle: Python中遍历列表时带上索引
date: 2020-04-12 19:48:30
tags:
- Python
---

Python默认的`for ... in ...`循环是不带索引序号的，例如：
```python
presidents = ["Washington", "Adams", "Jefferson", "Madison", "Monroe", "Adams", "Jackson"]
for name in presidents:
    print("President : {}".format(name))

```
执行结果如下：
```shell
President : Washington
President : Adams
President : Jefferson
President : Madison
President : Monroe
President : Adams
President : Jackson
```
在这个例子中，我们可以打印出总统的名字，但不能打印出该总统是第几任总统。那么，如何打印出是第几任总统呢？用`enumerate()`函数。改成如下形式：
```python
presidents = ["Washington", "Adams", "Jefferson", "Madison", "Monroe", "Adams", "Jackson"]
for num, name in enumerate(presidents, start=1):
    print("President {}: {}".format(num, name))

```
执行结果如下：
```
President 1: Washington
President 2: Adams
President 3: Jefferson
President 4: Madison
President 5: Monroe
President 6: Adams
President 7: Jackson
```
搞定！