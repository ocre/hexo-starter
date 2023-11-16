---
title: 命令行导出MySQL数据库表数据示例
date: 2022-06-22 13:59:10
tags: 
- MySQL
- example

---

## 命令行导出MySQL数据库表数据示例

* 导出所有数据库所有表:

  ```shell
  mysqldump -uroot -ppassword --all-databases > ./all_dbs_data.sql
  ```

  

* 导出`db1`, `db2`数据库:

  ```shell
  mysqldump -uroot -ppassword --databases db1 db2 > ./db1_db2_data.sql
  ```

  

* 导出`db1`数据库的`t1`,`t2`表:

  ```shell
  mysqldump -uroot -ppassword --databases db1 --tables t1 t2 > ./db1_t1_t2_data.sql
  ```

  

* 导出`db1`数据库的`t1`表中`id=1`的数据:

  ```shell
  mysqldump -uroot -ppassword --databases db1 --tables t1 --where='id=1' > ./t1_w1.sql
  ```

  

* 只导出表结构, 不含数据:

  ```shell
  mysqldump -uroot -ppassword --no-data --databases db1 --tables t1 > ./t1_structure.sql
  ```

  

* 导出远程MySQL服务器的数据:

  ```shell
  mysqldump -hhost -Pport -uroot -ppassword --databases db1 > ./remote_db1_data.sql
  ```

  

* 从`host1`服务器导出`db1.tb1`表数据,然后导入到`host2`服务器的`db2.tb1`表(会自动建表):

  ```shell
  mysqldump -hhost1 -Pport1 -uroot1 -ppassword1 --databases db1 --tables t1 | mysql -hhost2 -Pport2 -uroot2 -ppassword2 db2
  ```

  
