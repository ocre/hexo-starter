---
title: SQL基础面试题整理-表结构
date: 2022-06-25 11:49:51
tags: 
- SQL
---

## SQL基础面试题整理

### 表结构

* 学生表
	Student(s_id,s_name,s_birth,s_sex) –学生编号,学生姓名, 出生年月,学生性别
* 课程表
	Course(c_id,c_name,t_id) – –课程编号, 课程名称, 教师编号
* 教师表
	Teacher(t_id,t_name) –教师编号,教师姓名
* 成绩表
	Score(s_id,c_id,s_score) –学生编号,课程编号,分数


### 例题

1.  查询平均成绩大于60分的学生的学号和平均成绩
2.  查询所有学生的学号、姓名、选课数、总成绩
3.  查询没学过“张三”老师课的学生的学号、姓名

```sql
select s_id, s_name from student where s_id not in (
    select s_id from score a, course b, teacher c
    where a.c_id = b.c_id and b.t_id = c.t_id and c.t_name = '张三'
)
```

​	4. 其他问题 https://zhuanlan.zhihu.com/p/43289968

```sql
SELECT b.book_main_id, b.name, b.author, b.last_update_time, b.last_update_content from (select author, max(last_update_time) as last_update_time from book_main where author in ('$authors_str') group by author) as a, book_main as b where a.author=b.author and a.last_update_time=b.last_update_time";
```

