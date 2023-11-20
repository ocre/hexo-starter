---
title: Django修改request对象的两种方法
date: 2020-04-15 16:33:08
tags:
- Tools
- Django
---

Django使用`request.GET`或`request.POST`来获取参数，而这两个属性都是不可变的`django.http.QueryDict`对象，默认是不支持修改的。而我们有时候需要修改请求参数，怎么做呢？

#### 方法1：拷贝副本-> 修改 -> 赋值回去
可以利用`QueryDict`的`copy()`方法，得到一个可变的`QueryDict`副本，在副本上修改参数值后，再赋值给`request.GET`对象。 代码如下：
```python
def local_product_list(request):
  """
  获取本地产品列表
  """
  params = request.GET.copy()
  params['local'] = True
  request.GET = params
  return product_list(request)


def product_list(request):
  """
  获取产品列表
  """
  ...
```

#### 方法2：修改不可变属性
直接上代码：
```python
def local_product_list(request):
  """
  获取本地产品列表
  """
  mutable_value = request.GET._mutable
  if not mutable_value:
    # 原来不可变，改为可变
    request.GET._mutable = True
  # 修改参数值
  request.GET['local'] = True
  if not mutable_value:
    # 原来不可变，这里改回去，保持原值
    request.GET._mutable = False
  return product_list(request)


def product_list(request):
  """
  获取产品列表
  """
  ...
```

**注意：**`QueryDict`的`update()`方法跟我们常用的`dict.update()`有所不同。
以下两段代码的效果是不一样的：
```python
>>> q2 = QueryDict('local=False', mutable=True)
>>> q2
<QueryDict: {'local': ['False']}>
>>> q2['local'] = True
>>> q2
<QueryDict: {'local': [True]}>
```
```python
>>> q3 = QueryDict('local=False', mutable=True)
>>> q3
<QueryDict: {'local': ['False']}>
>>> q3.update({'local': True})
>>> q3
<QueryDict: {'local': ['False', True]}>
```
这是因为， `request.GET['local'] = True`是直接修改原`local`参数的值，而`request.GET.update({'local': True})`是**追加**新的值到`local`参数值的数组中。请务必小心！

