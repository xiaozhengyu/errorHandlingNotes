# MySQL：排序（ORDER）与去重（DISTINCT）一起使用遇到的问题



[TOC]



---



## 抛出问题：

```mysql
SELECT DISTINCE 
    column_a 
FROM 
    tbl_test
ORDER BY 
    column_b
```

上面的 SQL 在数据库 A 可以正常执行，但是在数据库 B 执行时会报错：

```
SQL错误（3065）：Expression #1 of ORDER BY clause is not in SELECT list, references column 'db0.tbl_test.column_b' which is not in SELECT list; this is incompatible with DISTINCT
```



## 分析问题：

数据库 A 的 MySQL 版本为 5.6，数据库 B 的 MySQL 版本为 5.7。MySQL 5.7 版本开始对 SQL 语法提出了更严格的要求，包括：<font color = red>requires ORDER BY clause in select DISTINCT statement</font>



## 解决问题：

方法一：修改配置，然后重启数据库：

```
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 
```



方法二：修改 SQL——先通过子查询排序，然后通过外部的查询去重

```mysql
SELECT DISTINCE 
    sub.clumn_a
FROM (SELECT column_a FROM tbl_test ORDER BY column_b) AS sub  
```



---

参考资料：

-   [Tolerating SQL errors (orderby not in distinct, select not in group by) in MySQL 5.7.13 vs 5.7.11](https://dba.stackexchange.com/questions/143619/tolerating-sql-errors-orderby-not-in-distinct-select-not-in-group-by-in-mysql)
-   [Mysql 5.7 requires order by clause in select DISTINCT statement (Paginator)](https://github.com/doctrine/orm/issues/5622#ref-issue-121698669)