---
title: Django常用命令
date: 2020-04-04 11:27:09
tags:
- Tools
- Django
---

最近在用`Django`做项目，整理了一下常用的命令，以备自查。
#### 工程构建&运行

1. 查看`Django`版本
```shell
python -m django --version
```
2. 创建工程
```shell
django-admin startproject your_project_name
```
3. 创建App
```shell
python manage.py startapp your_app_name
```
4. 本地测试运行工程
```shell
python manage.py runserver 8000
```
5. 创建后台管理员
```shell
python manage.py createsuperuser
```

#### 数据迁移migration

1. 执行迁移
```shell
python manage.py migrate
```
2. 创建迁移
```shell
python manage.py makemigrations your_app_name
```
3. 检查迁移sql
```shell
python manage.py sqlmigration your_app_name your_migration_sequence_no
```

#### 日常调试 & 测试

1. 打开命令行AP
```shell
python manage.py shell
```
2. 执行自动测试
```shell
python manage.py test your_app_name
```