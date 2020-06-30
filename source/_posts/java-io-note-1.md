---
title: Java IO流笔记（一）
date: 2020-06-12 15:37:03
tags:
- Java
---

### IO流的概念和分类

#### 什么是流

读写数据时像流水一样，从一端到另一端，因此叫做“流”。

#### 流的分类

按处理内容分为：

- 字节流
- 字符流。 （双字节）

按输入输出类别分为：

- 输入流
- 输出流

按跟数据源的关系分为:
- 节点流
- 处理流

### IO流的体系结构
主要的IO流类可以用下面一张表整合：
| 分类 | 字节输入流 | 字节输出流 | 字符输入流 | 字符输出流 |
| -- | -- | -- | -- | -- |
| 抽象基类 | InputStream | OutputStream | Reader | Writer |
| 访问文件 | FileInputStream | FileOutputStream | FileReader | FileWriter |
| 访问数组 | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader | CharArrayWriter |
| 访问管道 | PipedInputStream | PipedOutputStream | PipedReader | PipedWriter |
| 访问字符串 | -- | -- | StringReader | StringWriter |
| 缓冲流 | BufferedInputStream | BufferedOutputStream | BufferedReader | BufferedWriter |
| 转换流 | -- | -- | InputStreamReader | InputStreamWriter |
| 对象流 | ObjectInputStream | ObjectOutputStream | -- | -- |
|  | FilterInputStream | FilterOutputStream | FilterReader | FilterWriter |
| 打印流 | -- | PrintStream | -- | PrintWriter |
| 推回输入流 | PushbackInputStream | -- | PushbackReader | -- |
| 特殊流 | DataInputStream | DataOutputStream |  |  |

我们常用需要掌握的结构：
![继承结构图](https://cdn132.vieek.com/2020-06-11-%E6%88%AA%E5%B1%8F2020-06-11%20%E4%B8%8B%E5%8D%883.37.28.png)

