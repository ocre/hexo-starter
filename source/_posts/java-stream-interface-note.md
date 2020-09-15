---
title: Java Stream接口学习笔记
date: 2020-09-15 14:11:23
tags:
- Java
---

#### Stream接口概念辨析
Java中有两类`Stream`，一类是IO流，常见的有InputStream、OutputStream等，还有一类是Java8 新增的`Stream接口`。
Stream接口位于`java.util.stream`包中,是对集合功能的增强。
如何增强呢？ 简单来说，它支持集合元素的筛选、切片、映射、排序、匹配查找、聚合等多种复杂常见操作，使用`Lambda表达式`简化代码编写，并支持并行和串行两种模式的操作。

#### Stream接口使用步骤
1. 创建Stream，通过一个数据源来获取一个流
2. 转换Stream，每次转换可得到一个新的Stream对象
3. 对Stream进行聚合操作产生最终结果

#### Stream流的创建
有以下四种创建方式：
1. 基于现有集合，调用集合的stream()方法创建：
```java
List<Integer> integerList = List.of(1, 2, 3, 4, 5, 6);
Stream<Integer> integerStream = integerList.stream();
```
2. 基于数组，通过Stream工具类的stream()静态方法创建：
```java
IntStream intStream = IntStream.of(1, 2, 3, 4, 5, 6);
```
3. 通过Stream接口的of(T.. values)静态方法创建：
```java
Stream<Integer> integerStream1 = Stream.of(1, 2, 3, 4, 5, 6);
```
4. 通过Stream接口的generate(Supplier<? extends T> s)静态方法创建：
```java
Stream<Integer> limit  = Stream.generate(new Random()::nextInt).limit(10);
```

#### Stream中间操作（结果仍为Stream)
* 筛选、切片

| 方法声明 | 功能 |
| -- | -- |
| Stream filter<Predicate<? super T> predicate) | 过滤，返回一个包含匹配元素的流 |
| Stream distinct() | 去重，返回不含重复元素的流 |
| Stream limit(long maxSize) | 切片，返回不超过maxSize数量的元素组成的流 |
| Stream skip(long n) | 切片，返回丢弃前n个元素后的流 |

* 映射

| 方法声明                                                     | 功能                                               |
| ------------------------------------------------------------ | -------------------------------------------------- |
| Stream map(Function<? super T, ? extends R> mapper) | 返回每个处理过元素组成的流  |
| Stream flatMap(Function<? super T,? extends Stream<? extends R>> mapper) | 返回每个被替换过元素组成的流，并将所有流合成一个流 |

  

* 排序

| 方法声明 | 功能 |
| ---- | ---- |
| Stream sorted() | 返回经过自然排序后元素组成的流 |
| Stream sorted(Comparator<? super T> comparator) | 返回经过比较器排序后元素组成的流 |

#### Stream终止操作
* 匹配与查找

| 方法声明 | 功能 |
| -- | -- |
| Optional findFirst() | 返回该流的第一个元素 |
| boolean allMatch(Predicate<? super T> predicate) | 判断所有元素是否匹配 |
| boolean noneMatch(Predicate<? super T> predicate) | 判断没有元素是否匹配 |
| Optional max(Comparator<? super T> comparator)  | 根据比较器返回最大元素 |
| Optional min(Comparator<? super T> comparator)  | 根据比较器返回最小元素 |
| long count()  | 返回元素的个数 |
| void forEach(Consumer<? super T> action)  | 对流中每个元素执行操作 |

* 规约（reduce）
| 方法声明 | 功能 |
| -- | -- |
| Optional reduce(BinaryOperator accumulator) | 返回结合后的元素值 |

* 收集
| 方法声明 | 功能 |
| -- | -- |
| <R,A> R collect(Collector<? super T,A,R> collector) | 使用收集器对元素进行处理 |

