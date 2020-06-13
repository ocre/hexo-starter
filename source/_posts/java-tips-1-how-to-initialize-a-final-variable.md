---
title: Java tips 1 - final变量的几种初始化方式
date: 2020-04-01
tags:
- Java
---

### final成员变量的初始化方式
#### 方法1：在定义变量时直接赋值
```java
public final class MyString {
    private final char value[] = "";
}
```
#### 方法2：在构造函数中初始化
```java
public final class MyString {
    private final char value[];
    
    public MyString() {
        this.value = "".value;
    }
}
```

#### 方法3：在构造代码块中初始化
```java
public final class MyString {
    private final char value[];
    
    {
        this.value = "".value;
    }
}
```
**注意： 构造代码块会在构造函数之前被执行。** （{% post_link java-tips-2-initialization-order 构造代码块、静态代码块执行顺序%}）

### final静态变量的初始化方式
#### 方法1： 在定义时直接赋值
```java
public final class MyString {
    private static final long serialVersionUID = -6849794470754667710L;
}
```
#### 方法2： 在静态代码块中赋值
```java
public final class MyString {
    private static final long serialVersionUID;
    
    static {
        this.serialVersionUID = -6849794470754667710L;
    }
}
```

关于静态代码块、构造代码块、构造函数的执行顺序，请参考我的另一篇blog：{% post_link java-tips-2-initialization-order 构造代码块、静态代码块执行顺序%}。