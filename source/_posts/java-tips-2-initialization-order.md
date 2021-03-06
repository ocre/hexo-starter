---
title: Java静态代码块、构造代码块、构造函数执行顺序
date: 2020-04-01 15:18:03
tags:
- Java
---

### 名词解释
**静态代码块**：类中以*static {}*形式包围起来的代码块，Jvm加载类时执行，仅执行一次。
**构造代码块**：类中以*{}*形式包围起来的代码块，不包括方法中的普通代码块。每次创建对象时都要执行。
**构造函数**：每次创建对象时就会执行构造函数，构造函数的作用时给对象初始化。

### 单个类中的执行顺序

#### 执行顺序
静态代码块 > 构造代码块 > 构造函数

#### 验证
执行以下代码：
```java
public class HelloTest {
    public HelloTest() {
        System.out.println("执行 HelloTest() 构造函数");
    }
    
    {
        System.out.println("执行构造代码块");
    }
    
    static {
        System.out.println("执行静态代码块");
    }
    
    public static void main(String[] args) {
        System.out.println("准备new第一个对象");
        HelloTest one = new HelloTest();
        System.out.println("准备new第二个对象");
        HelloTest two = new HelloTest();
    }
}
```
结果输出：
```
准备new第一个对象
执行静态代码块
执行构造代码块
执行 HelloTest() 构造函数
准备new第二个对象
执行构造代码块
执行 HelloTest() 构造函数
```

### 继承关系下的执行顺序
考虑父子继承情况，顺序又会是怎么样呢？

#### 执行顺序

父类静态代码块 > 子类静态代码块 > 父类构造代码块 > 父类构造函数 > 子类构造代码块 > 子类构造函数

#### 验证
执行以下代码：
```java
public static class HelloA {
    public HelloA() {
        System.out.println("执行HelloA的构造函数");
    }

    {
        System.out.println("执行HelloA的构造代码块");
    }

    static {
        System.out.println("执行HelloA的静态代码块");
    }
}

public static class HelloB extends HelloA {
    public HelloB() {
        System.out.println("执行HelloB的构造函数");
    }

    {
        System.out.println("执行HelloB的构造代码块");
    }

    static {
        System.out.println("执行HelloB的静态代码块");
    }
    
    public static void main(String[] args) {
        HelloB b = new HelloB();
    }
}
```
执行结果如下:
```
执行HelloA的静态代码块
执行HelloB的静态代码块
执行HelloA的构造代码块
执行HelloA的构造函数
执行HelloB的构造代码块
执行HelloB的构造函数
```