---
title: java局部变量、实例变量、静态变量辨析
date:  2020-05-05 14:50:21
tags:
- Java
---

根据声明位置的不同，我们一般可以将java的变量分为两个类型：**成员变量**和**局部变量**。
### 变量的分类
成员变量是指声明在类中、类方法之外的变量，包括：
* 实例变量： 无static修饰。
* 类变量(静态变量)：有static修饰。
局部变量是指声明在类方法中的变量，包括：
* 方法参数变量：也就是形参。
* 方法局部变量：声明在方法体{}内，方法体嵌套的代码块外。
* 代码块局部变量：声明在方法体嵌套的代码块内。

### 各种变量的特征
接下来用一个表格阐述一下这5种类型的变量各自的特征。

| | 声明/定义方式  | 作用域&生命周期 | 访问权限 | 默认值&初始化 | 内存分配 |
| --- | --- | --- | --- | --- | --- |
| 方法参数变量 | 方法形参 | 方法执行时创建，执行完释放 | 方法体内可见 | 无，必须明确初始化 | 栈区(java虚拟机栈) |
| 方法局部变量 | 方法体{}内 | 方法执行时创建，执行完释放 | 方法体内可见 | 无，必须明确初始化 | 栈区(java虚拟机栈) |
| 代码块局部变量 | 方法体内的某个代码块{}内 | 代码块执行时创建，执行完释放 | 代码块内可见 | 无，必须明确初始化 | 栈区(java虚拟机栈) |
| 实例变量 | 类中，方法体外 | 对象创建时创建，对象销毁时释放 | 由修饰符决定 | 有，引用类型为null，基本数据类型为java预定义的默认值 | 随对象一起分配在堆上 |
| 静态变量 | 类中，方法体外，带static修饰符 | 类加载时创建，类卸载时销毁 | 由修饰符定 | 有，引用类型为null，基本数据类型为java预定义的默认值 | 方法区(静态存储区) |

