---
title: MySQL数据库编码更改为utf8mb4
date: 2020-04-11 10:04:31
tags:
- Tools
---

现在大家打字聊天都喜欢发表情符号，而一个表情符号需要4个字节存储，对应的字符编码是utf8mb4。MySQL如果不做设置，默认编码会是乱七八糟的，什么都可能出现。比如下面这个：

![1586571131272](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200411101213-172439.png)

如上图所示，我们可以使用`SHOW VARIABLES WHERE Variable_name LIKE 'character_set_%' OR Variable_name LIKE 'collation%';`命令查询数据库服务器的编码设置。

使用utf8编码大家应该都没有异议，虽然关于utf8的争议从来就没有消停过，但它仍然一骑绝尘，逐渐成为存储多字节文字的事实标准。所以在这里不讨论为何不适用gbk甚至更古老的gb2312的话题。

### 为什么使用utf8mb4
那么，为什么一定要使用utf8mb4编码呢？继续使用utf8编码不可以吗? 以前可以，现在不可以了。简单来说，utf8mb4是utf8的超集。utf8mb4完全兼容utf8，换句话说，用utf8mb4代替utf8是没有问题的；反之，utf8是不能替代utf8mb4的。采用utf8mb4,主要是为了解决文章开头我们提到的存储表情符号的问题。关于mysql使用utf8编码，网上有很多惨痛案例， 比如[记住：永远不要在 MySQL 中使用 UTF-8](https://zhuanlan.zhihu.com/p/63360270)

### 如何设置
首先，设置 `my.cnf`文件，这个文件一般位于`/etc/my.cnf`。设置如下：
```shell
[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4
[mysqld]
character-set-client-handshake=FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
```
注意，不同版本mysql或mariadb, 配置文件位置可能不一样，请灵活处理。

设置完成后，需要重启mysql服务。重启后，登陆mysql后台，继续用`SHOW VARIABLES WHERE Variable_name LIKE 'character_set_%' OR Variable_name LIKE 'collation%';`命令检查配置是否OK。如果出现下面的配置，说明配置OK了。

![1586573272451](https://raw.githubusercontent.com/ocre/blog-pub-images/master/typora/20200411104753-963241.png)

另外，不要忘了把现有的数据库和表都转成utf8mb4编码。
更改数据库编码：
```sql
ALTER DATABASE caitu99 CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```
更改表编码：
```sql
ALTER TABLE TABLE_NAME CONVERT TO CHARACTER SET utf8mb4 COLLATEutf8mb4_general_ci; 
```
