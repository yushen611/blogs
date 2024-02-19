---
title: 'mysql八股笔记'
date: 2023-06-20 08:27:35
tags: []
published: true
hideInList: true
feature: 
isTop: false
---
😇这里将全面的整理mysql的所有八股
<!-- more -->

# 一、MYSQL底层知识与原理

## 1.1 引擎

### TODO:如何选择MySQL的存储引擎？

### TODO:MySQL存储引擎MyISAM与InnoDB区别

### TODO：InnoDB引擎的4大特性

## 1.2 索引

### TODO:索引的使用场景有哪些？

### TODO:索引有哪几种类型？

### TODO:索引的数据结构（b+树，hash）

### TODO:创建索引的原则

### TODO:百万级别或以上的数据如何删除

### TODO:最左前缀匹配原则

### TODO:数据库为什么使用B+树而不是B树

### TODO:什么是聚簇索引？何时使用聚簇索引与非聚簇索引

### TODO:非聚簇索引一定会回表查询吗？

## 1.3 事务

### TODO:事务的四大特性是什么？

### TODO:什么是脏读？幻读？不可重复读？

### TODO:什么是事务的隔离级别？MySQL的默认隔离级别是什么？



## 1.X 其他

### mysql有关权限的表都有哪几个

MySQL服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql_install_db脚本初始化。这些权限表分别user，db，table_priv，columns_priv。下面分别介绍一下这些表的结构和内容：

user权限表：记录允许连接到服务器的用户帐号信息，里面的权限是全局级的。

<img src="https://gitee.com/yushen611/img/raw/master/image-20230620085905449.png" alt="image-20230620085905449" style="zoom:60%;" />

db权限表：记录各个帐号在各个数据库上的操作权限。

<img src="https://gitee.com/yushen611/img/raw/master/image-20230620085944817.png" alt="image-20230620085944817" style="zoom:67%;" />

table_priv权限表：记录数据表级的操作权限。

<img src="https://gitee.com/yushen611/img/raw/master/image-20230620090312150.png" alt="image-20230620090312150" style="zoom:67%;" />

columns_priv权限表：记录数据列级的操作权限。

<img src="https://gitee.com/yushen611/img/raw/master/image-20230620090336968.png" alt="image-20230620090336968" style="zoom:67%;" />

### Mysql的binlog有有几种录入格式？分别有什么区别？

有三种格式，statement，row和mixed。

statement模式下，每一条会修改数据的sql都会记录在binlog中。不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。由于sql的执行是有上下文的，因此在保存的时候需要保存相关的信息，同时还有一些使用了函数之类的语句无法被记录复制。

row级别下，不记录sql语句上下文相关信息，仅保存哪条记录被修改。记录单元为每一行的改动，基本是可以全部记下来但是由于很多操作，会导致大量行的改动(比如altertable)，因此这种模式的文件保存的信息太多，日志量太大。

mixed，一种折中的方案，普通操作使用statement记录，当无法使用statement的时候使用row。此外，新版的MySQL中对row级别也做了一些优化，当表结构发生变化的时候，会记录语句而不是逐行记录。

默认是row模式

![image-20230620085806365](https://gitee.com/yushen611/img/raw/master/image-20230620085806365.png)

### 



# 二、数据库设计

### **数据库三大范式是什么**

第一范式：每个列都不可以再拆分。

第二范式：在第一范式的基础上，非主键列完全依赖于主键，而不能是依赖于主键的一部分。

第三范式：在第二范式的基础上，非主键列只依赖于主键，不依赖于其他非主键。

在设计数据库结构的时候，要尽量遵守三范式，如果不遵守，必须有足够的理由。比如性能。事实上我

们经常会为了性能而妥协数据库的设计。

### TODO:mysql有哪些数据类型以及使用场景



# 三、数据库操作

