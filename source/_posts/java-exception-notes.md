---
title: Java学习笔记-异常
date: 2020-06-09 14:43:21
tags:
- Java
---

### 异常机制的基本概念
异常就是指**不正常**的意思，在Java中指的是程序执行过程中发生的不正常情况。

#### 异常VS错误
Java将程序的不正常行为按严重程度分为错误（Error）和异常（Exception）两大类。
java.lang.Throwable是描述错误Error和异常Exception的超类。
Error类指的是Jvm无法处理的严重错误，一旦发生Error，通常Jvm无能为力，只能挂掉。
Exception类通常用来描述因编程错误或偶然外部因素导致的轻微错误，一般可以通过编程解决。

#### 异常的分类
Java官方提供的异常类很多，但通常可以按异常检测的时间点分为两大类：
* 检测性异常，也就是说编译器可以检测到的异常，比如IOException等。
* 非检测性异常，RuntimeException，也叫运行时异常。 比如，被0除时会抛出ArithmeticException,空指针时的NullPointException,等等。

#### 异常的继承结构
前面已经说过，所有异常都继承自Exception类，而Exception又继承自Throwable类。
再加上我们平时最常用的几个Runtime异常，构成的异常继承结构图如下：
![常见异常的继承结构](https://cdn132.vieek.com/2020-06-09-%E6%88%AA%E5%B1%8F2020-06-09%20%E4%B8%8A%E5%8D%8811.48.04.png)

### 异常的代码结构
```java
try {
    ①
} catch (Exception e) {
    ②
} finally {
    ③
}
```
#### finally的注意事项
* finally
### 异常的处理
异常的处理，一般有三种方式：
* 异常避免
* 异常捕获
* 异常抛出
如果拿感冒来比喻异常，这三种方式就好比我们的对待感冒的策略一样。首先，我们平时注意锻炼、保暖，以避免得上感冒，这相当于异常的避免。而万一得了感冒，我们可以吃点感冒药，这相当于异常的捕获。如果感冒严重了吃感冒药也不顶用，那就只能去医院看医生了，这就相当于抛出异常。
#### 异常避免
一般使用**if语句**来提前判断，避免异常。比如下面的语句，通过判断b是否等于0，来避免ArithmeticException：
```java
int a = 10;
int b = 0;
if (b != 0) {
    System.out.println(a / b);
}
```
再比如，提前判断引用变量是否为空，来避免空指针异常NullPointException：
```java
FileInputStream fis = null;
...
if (null != fis) {
    fis.close();
}
```
#### 异常捕获
使用**catch语句**来捕获异常并处理；使用finally做善后处理。

catch语句注意：
* 多个catch语句，小异常类应放到前面。

tips: *finally语句在函数return之前必然执行。*

考点：请问下面代码返回结果是几？
```java
try {
    System.out.println(3 / 0);
    return 0;
} catch (ArithmeticException e) {
    return 1;
} finally {
    return 2;
}
```
结果是 2， 因为finally抢在return 1之前return了。

#### 异常抛出
若方法决定自己不处理异常，则需要将异常抛出。


#### 什么时候抛出异常，什么时候捕获异常？
经验：
1. 方法重写时，若被重写的方法未抛出异常，则重写后的方法也不应该抛出异常。
2. 如果方法之间有好多层调用关系，可以抛出异常，交由最外层方法捕获。

### 自定义异常
有时候我们需要自定义异常。

#### 为什么要自定义异常呢
Java官方提供的异常不够用。现实业务开发中，异常各种各样，需要自定义。

#### 如何自定义
编码示例：
```java
public class XXXException extends Exception {
    static final long serialVersionUID = 1111111L;
    
    public XXXException() {
    }
    
    public XXXException(String message) {
        super(message);
    }
}