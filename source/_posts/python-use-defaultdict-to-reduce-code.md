---
title: 使用defaultdict精简Python代码
date: 2020-02-26 22:27
tags:
- Python
---

### 普通dict实现计数统计
我们常常用Python的`dict`来做简单的计数统计。先看下面一段代码：
```python
names = ['alice','bob','cindy','ocre','alice','ocre','ocre','cindy','tom']
name_counts = {}
for name in names:
    if name not in counts:
        name_counts[name] = 1
    else:
        name_counts[name] += 1

print(name_counts)
```
这段代码能够统计出每个名字出现的次数。这里我们使用了普通的`dict`结构names_counts来保存名字和出现次数的映射关系。我们知道，如果直接访问字典中不存在的键，会抛出`KeyError`异常。比如：
```python
>>> name_counts = {}
>>> name_counts['ocre'] += 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'ocre'
>>>
```
为了避免`KeyError`异常，通常需要使用一个`if name in name_counts: ... else ...`的代码段来检查词典中是否存在特定的键。如果键已经存在，则直接累加操作，如果不存在，则先赋一个初始值。

### 引入defaultdict来简化代码
对于有代码洁癖的猿来说，这样的代码仍然有一股坏味道。我们可以用`defaultdict`来简化一下。简化后的代码如下：
```python
from collections import defaultdict

names = ['alice','bob','cindy','ocre','alice','ocre','ocre','cindy','tom']
name_counts = defaultdict(int)
for name in names:
    name_counts[name] +=1
    
print(name_counts)
```
可见，在引入`defaultdict`后，烦人的`if-else`被干掉了。（BTW， 统计代码还可以利用其他方法进一步简化，参考我的另一篇博文：{% post_link four-ways-to-count-in-python Python中实现计数统计的4种方法 %}）

### 引申：如何构造defaultdict？
这里我们使用了`defaultdict`来代替普通`dict`，来给name_counts中不存在的键设置默认值，默认值由`defaultdict()`的第一个参数确定。

构造`defaultdict`，需传递一个工厂函数作为参数：
```python
mydict = collections.defaultdict( factory_function )
```
这里的工厂函数，可以是普通类型，比如 `list`,`set`,`str`,`int`等，当访问一个不存在键不存在时，返回这些类型的默认值，比如：
```python
>>> from collections import defaultdict
>>>
>>> d1 = defaultdict(int)
>>> d2 = defaultdict(list)
>>> d3 = defaultdict(set)
>>> d4 = defaultdict(str)
>>> d1
defaultdict(<class 'int'>, {})
>>> d1['key']
0
>>> d2['key']
[]
>>> d3['key']
set()
>>> d4['key']
''
>>>
```

工厂函数也可以是普通的无参函数，比如：

```pytyon
>>> from collections import defaultdict
>>>
>>> def factory_func():
...     return []
...
>>> d5 = defaultdict(factory_func)
>>> d5
defaultdict(<function factory_func at 0x0000027817B3F378>, {})
>>> d5['ocre']
[]
>>>
```

工厂函数还可以用lambda表达式（匿名函数）来代替，比如：
```python
>>> from collections import defaultdict
>>>
>>> d6 = defaultdict(lambda : 0)
>>> d6
defaultdict(<function <lambda> at 0x0000027817C2E268>, {})
>>> d6['ocre']
0
>>>
```