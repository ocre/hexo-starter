---
title: Scrapy中的JSON Lines文本格式是什么鬼？
date: 2020-02-18 21:38:10
tags: JSON,Scrapy
---

# 什么是JSON Lines

[JSON Lines](http://jsonlines.org/)是一种类似JSON的文本格式。它采用`\\n`作为分隔符存储多个JSON对象的列表，每个JSON对象仍然使用JSON格式表示。它有三个硬性约束：
1.使用UTF-8编码。
2.每一行都是一个有效的JSON对象。
3.行分隔符为`\\n`。

JSON Line文件的建议扩展名是`.jsonl`。一般建议使用gzip或者bzip2进行压缩，生成`.jsonl.gz`或`.jsonl.bz2`文件，进一步节省存储空间。

# 为什么使用JSON Lines

我们平时习惯用`CSV`和`JSON`文本格式来存储列表类对象。同样的数据，`CSV`相比`JSON`更简洁，更节省空间，但是可读性很差（数据列分隔符随便定义没有统一标准，数据列定义只在第一行体现，不够直观），并且不支持嵌套数据。而`JSON`格式的可读性更好，能够支持嵌套数据，但是在存储数据列表时，需要识别数组首尾的`[`和`]`，涉及到读取、解析、存储整个文件，不适合大文件的高效存取。因此，`JSON Lines`应运而生。采用 JSON Lines 保存数据集，则文件中每一行就是一条`JSON`数据，可以一边读取一边解析、操作。而在添加数据时，只需要 append 数据到文件尾部即可。

# 示例

一个简单的`JSON Lines`格式文件内容如下：
```
{"name": "Gilbert", "wins": [["straight", "7♣"], ["one pair", "10♥"]]}
{"name": "Alexa", "wins": [["two pair", "4♠"], ["two pair", "9♠"]]}
{"name": "May", "wins": []}
{"name": "Deloise", "wins": [["three of a kind", "5♣"]]}
```

# 应用场景

`JSON Lines`文本格式由于同时具备可读性和便于流式处理的特点，常常被用于以下场景：
1.日志记录
2.流数据API
3.爬虫数据存储