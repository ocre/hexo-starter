---
title: java annotation (1)
date: 2019-12-11 09:45:02
tags: 
- Java
- annotation
---

在各种流行的Java开发框架比如Spring boot中，大量使用了注解Annotation来做自动化配置。学习Spring有必要先了解一下注解。
注解是从Java 1.5版本开始加入到Java语言中的，因其便利性逐渐被Java开发者广泛接受。那么，注解到底是什么东西呢？

## 注解的定义
就个人理解，注解就是贴在程序源码上的语法标签，为编译器和JVM提供关于被贴代码（类、方法、构造函数、变量、参数等等）的一些辅助信息，这些信息可以帮助编译器做一些额外处理（比如@Overrride注解告诉编译器需要检查是否真的做了方法覆盖），或者让JVM在加载这段代码时做必要的前置工作（比如@DependsOn注解告知JVM需要先加载所依赖的类）。
我看到的比较靠谱的注解定义[在这里](https://www.geeksforgeeks.org/annotations-in-java/)。

## 元注解
Java语言提供如下元注解：
* @Override 
  典型的标记类注解，作用是明确告知编译器该方法Override了基类、接口、Object等的共有方法。编译器将对该注解标记的方法进行检查，若发现不符合Override定义，会直接报错。
* @Deprecated
  标记类注解，标记方法已被弃用。如果有人继续使用该方法，编译器将抛出警告⚠️。
* @SuppressWarnings
  让编译器忽略指定的一种或多种编译器警告。
* @Retention
  指定被它标记的注解的存储位置，从而决定它的生命周期。比如`RetentionPolicy.SOURCE`  定义注解只存储在源码中，不会被编译进.class文件，那么注解的生命周期也就被限制在源码编译成字节码之前。 默认值为`RetentionPolicy.CLASS`。
* @Target
  指定被它标记的注解的作用目标。目前取值有`TYPE`,`FIELD`,`METHOD`,`PARAMETER`,`CONSTRUCTOR`,`LOCAL_VARIABLE`,`ANNOTATION_TYPE`,`PACKAGE`,`TYPE_PARAMETER`,`TYPE_USE`。
* @Documented
  标记文档生成工具在生成的文档中包含被它标记的注解。
* @Inherited
  定义Java子类在继承父类时，同时继承拥有这个元注解的注解。注意，只针对普通类之间的继承，接口继承、接口实现时不会继承注解。
* @Repeatable
  定义注解可以多次重复应用于相同的目标Target。
* @Native
  标记目标常量可以被native代码引用。
* @FunctionalInterface
  该注解用于标记函数式接口。加上该注解后，若编写的接口不符合函数式接口的定义，编译器将报错。
* @SafeVarargs
  针对可变参数构造函数或方法生效，让编译器忽略unchecked警告⚠️。

## 自定义注解
  我们可以通过组合现有注解的方式来定义新的注解。注解的定义语法如下：
```
[Access Specifier] @interface <AnnotationName> {
  DataType <Method Name>() [default Value];
}
```
参照以上语法和现有元注解，我们可以自定义如下新注解：
```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface HelloAnnotation {
  String value() default "Hi";
}
```
然后，可以像其他注解一样使用：
```
@HelloAnnotation("Hello")
public class MyApplication {
  public static void main(String[] args) {
    HelloAnnotation annotation = MyApplication.class.getAnnotation(HelloAnnotation.class);
    System.out.println(annotation.value());
  }
}
```
