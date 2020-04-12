---
title: 修改MySQL的自增ID
date: 2020-04-12 20:05:13
tags:
- Tools
---

近期在做一个数据迁移的项目时，需要把产品分类数据导入新表中。因为数据是爬虫抓取下来的，有些字段数据内容不完整或格式错误，因此执行了多次导入才最终导入成功。期间需要清空错误数据然后重新导入，如果不重置自增ID，则每次导入同样的数据，ID会越来越大。 那么如何设置自增ID呢？
首先，可以用如下语句查看当前的自增ID是多少：
```sql
SELECT AUTO_INCREMENT FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'your-database-name' AND TABLE_NAME = 'your-table-name';
```
然后，我们来修改自增ID：
```sql
ALTER TABLE your-table-name AUTO_INCREMENT=1000;
```
以上语句就可以把自增ID设置为1000。
至于最终设置的对不对，用上面第一条SQL查一下就知道了。