---
title: Python中实现计数统计的4种方法
date: 2020-02-26 19:10
tags:
- Python
---

我们在日常开发过程中经常会遇到这样的需求，统计某个IP的访问次数，统计某个商户的订单数 等等。这类统计统称为计数统计。
作为一门优秀的高级语言，Python提供了至少4种方式来方便地实现计数统计。下面我们举例说明。
首先给出问题: 给定下面的输入和输出，实现`name_count`方法。

```python
# Input:
names = ['alice','bob','cindy','ocre','alice','ocre','ocre','cindy','tom']

# Inteface
result = name_count(names)

# Output:
{'alice': 2, 'bob': 1, 'cindy': 2, 'ocre': 3, 'tom': 1}

```

### 1. 使用dict字典实现计数统计

```python
def name_count(names):
    name_counts = {}
    for name in names:
        if name not in counts:
            name_counts[name] = 1
        else:
            name_counts[name] += 1
    return name_counts
```

### 2. 使用defaultdict实现计数统计

```python
from collections import defaultdict

def name_count(names):
    name_counts = defaultdict(lambda: 0)
    for name in names:
        name_counts[name] += 1
    return name_counts
```

### 3. 使用set和list实现计数统计

```python
def name_count(names):
    name_set = set(names)
    name_list = []
    for name in name_set:
        name_list.append(name, names.count(name))
    return name_list
```

### 4.使用Counter实现计数统计

```python
from collections import Counter

def name_count(names):
    return dict(Counter(names))
```

可见，使用`Counter`类的第4种方法代码最简洁。
有了以上4种方法，平时面对小规模数据集的计数统计问题时就能够手到擒来了。

更多统计实现方式等待发现！