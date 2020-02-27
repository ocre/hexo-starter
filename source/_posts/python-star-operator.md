---
title: Python的*和**运算符是什么意思？有什么区别？
date: 2020-02-24 10:12:03
tags:
- Python
---

看到这么一段`Flask`代码：
```python
view_data = dict(
    rows = posts[start:end],
    page = page,
    has_next_page = has_next_page,
)
return render_template('posts.html', **view_data)
```
代码中最后一行时，发现里边有`**`这样两个星号连在一起用的语法，不解其意。查阅资料后，才发现星号运算符还是真是神通广大。具体来说，有4种不同用途。

### 1. 算数运算符
`*` 表示乘法运算， `**`表示乘方运算。
```python
>>> 3 * 2
6
>>> 3 ** 2
9
```

### 2. 序列解包
`序列解包`是Python的一种语法糖，可以用来简化代码。
普通的序列解包，要求赋值运算符`=`左侧的变量个数和右侧的变量值个数相等。示例如下：
```python
>>> lat, lon = 108, 33
>>> lat
108
>>> lon
33
```
若`=`左边的变量个数和右侧的变量值个数不相等，则解包失败报错：
```python
>>> a, b = 1, 2, 3
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: too many values to unpack (expected 2)
>>>
```
```python
>>> a, b, c = 1, 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: not enough values to unpack (expected 3, got 2)
>>>

这时，就可以使用 `*`星号运算符来处理了。`*`运算符将右侧多余的变量值**按顺序合并**成一个序列赋值给`*`标记的变量，用法示例如下:
​```python
>>> a, *b = 1, 2, 3
>>> a
1
>>> b
[2, 3]
```

```python
>>> *a, b = 1, 2, 3
>>> a
[1, 2]
>>> b
3
```

```python
>>> a, *b = 1, 2
>>> a
1
>>> b
[2]
```

```python
>>> a, *b = 1
>>> a
1
>>> b
[]
```

### 3. 函数形参
`*`和`**`都可以用作函数的形参，用来将不定数量的参数传递给一个函数。一个星号用来传递元组`tuple`，两个星号用来传递字典`dict`，通常用`*args`和`*kwargs`来分别表示。
示例如下：
```python
>>> def func(a, *args):
...     print(a, args)
...
>>> func(1, 2, 3)
1 (2, 3)
>>>
```

```python
>>> def func(a, **kwargs):
...     print(a, kwargs)
...
>>> func(1, lat=108, lon=33)
1 {'lat': 108, 'lon': 33}
```

```python
>>> def func(*args, **kwargs):
...     print(args, kwargs)
...
>>> func(1, 2, 3, name='ocre', sex='male')
(1, 2, 3) {'name': 'ocre', 'sex': 'male'}
```

### 4. 函数实参
为避免实参过长，可以用`*`和`**`对多个参数进行打包，示例如下：
```python
>>> def func(a, **kwargs):
...     print(a, kwargs)
...
>>> func(1, name='ocre', sex='male')
1 {'name': 'ocre', 'sex': 'male'}
>>> data = {'name':'ocre', 'sex':'male'}
>>> func(1, **data)
1 {'name': 'ocre', 'sex': 'male'}
```
本文开头我碰到的情况，就属于最后这种用法。
