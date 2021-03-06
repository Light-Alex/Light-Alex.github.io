---
title: MySQL学习笔记1
date: 2020-07-18 22:14:51
tags: ['狂神']
categories: ['MySQL']
typora-root-url: ..
---

# MySQL

## MySQl简介

* 由瑞典MySQL AB 公司开发，属于 Oracle 旗下产品
* MySQL 是最流行的关系型数据库管理系统
* 关系数据库将数据保存在不同的表中
* 所使用的 SQL 语言是用于访问数据库的最常用标准化语言
* 体积小、速度快、成本低

官网文档： https://dev.mysql.com/doc/refman/5.7/en/

<!--more-->

### 学习思路

对比：SQLyog的可视化操作，查看历史记录

必须记住基本的固定语法和关键字

## 1.初始MySQL

javaEE；企业级java开发 web

前端  （页面【展示，数据】）

后台 （连接点【连接数据库jdbc】，连接前端【控制：控制视图跳转，给前端传递数据】）

数据库（存数据）

### 1.1.什么是数据库

数据库（DataBase，DB）

概念：数据仓库，安转在操作系统之上，存储大量数据

作用：存储数据，管理数据

### 1.2.数据库分类

关系型数据库（SQL）

* MySQL，Oracle...
* 通过表与表之间，行和列之间的关系来存储数据

非关系型数据库（NOSQL）

* Redis，MongDB
* 非关系型数据库，对象存储，通过对象自身的属性来存储

### 1.3.DBMS（数据库管理软件）

* 数据库的管理软件，科学有效的管理数据。维护和获取数据

### 1.4.连接数据库

命令行连接

```sql
mysql -uroot -p
mysql -uroot -p123456    --连接数据库
```

```sql
-- 所有的语句都用分号结尾
flush privileges; 		--刷新权限
show databases;		--查看所有数据库

mysql> use school	--切换数据库
Database changed

show tables;		--查看当前数据库中的所有表
describe student;	--显示当前数据库中具体表的信息

create database first_study;	--创建数据库

exit;				--退出连接
```

**数据库 xx 语言**（CRUD）

DDL	定义

DML	操作

DQL	查询

DCL	控制

## 2.操作数据库

操作数据库>操作数据库中的表>操作表中的数据

### 2.1.操作数据库

#### 1.创建数据库

```sql
CREATE DATABASE first_study; 
CREATE DATABASE IF NOT EXISTS first_study;	--创建数据库
```

#### 2.删除数据库

```sql
DROP DATABASE hello;
DROP DATABASE IF EXISTS hello;
```

#### 3.使用数据库

```sql
USE first_study;
USE `school`;	--使用反引号，如果表名或字段名为特殊字符，需要加``
```

#### 4.查看数据库

```sql
shwo DATABASES;
```

### 2.2.数据库的列类型

#### 数值

* ==int			标准的整数               4字节==  【常用】
* bigint      较大的数据                8字节
* float         浮点数                     4字节
* double      浮点数                   8字节
* decimal    字符串形式的浮点数      【金融计算使用】

#### 字符串

* char           字符串固定大小      0~255
* varchar          可变字符串         0~65535      常用   String、
* tinytext           微型文本           2^8-1
* text                  大文本               2^16-1

#### 时间日期

* date   YYYY-MM-DD  日期格式
* time    HH:mm:ss     时间格式
* ==datetime     YYYY-MM-DD HH:mm:ss  最常用的时间格式==
* ==timestamp  时间戳    1970.1.1 到现在的毫秒数==
* year    年

#### null

* 没有值，未知
* 避免使用null进行运算

### 2.3.数据库的字段属性【重点】

#### Unsigned：

* 无符号的整数
* 声明该列不能定义为负数

#### Zerofill：

* 不足的位数，使用0来填充   
  *  int（M）     
  * int 比较特殊    如果是varchar这个M就代表存储大小
  * M指的是最大显示宽度，对打有效显示宽度为225，显示宽度与存储大小无关

#### 自增

* 自动在上一条记录的基础上 +1（默认）
* 通常用来设计唯一的主键  index  ，必须是整数类型
* 可以自定义设计主键自增的起始值和步长

#### 非空  not null

* 设置为 not null ，如果不赋值，会报错
* null，如果不填写值，默认为null

#### 默认

* 设置默认的值

#### 【拓展】

```
真实开发项目，每一个表都必须存在以下五个字段，表示一个记录的存在用意

id     主键
`version`    乐观锁
is_delete    伪删除
gmt_create   创建时间
gmt_update   修改时间
```

### 2.4.创建数据库表

```sql
-- 目标：创建一个school数据库
-- 创建学生表(列，字段)，使用SQL创建
-- 学号int 登录密码varchar(20) 姓名，性别varchar(2),出生日期(datetime),家庭住址,email

-- 注意点，使用英文(), 表的名称 和 字段尽量使用 `` 括起来
-- AUTO_INCREMENT 自增
-- 字符串 使用 单引号(或双引号)括起来
-- 所有的语句后面加, (英文的), 最后一个不用加
-- PRIMARY KEY 主键，一般一个表只有一个唯一的主键！
CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
	`name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
	`pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
	`birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
	`address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
	`email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY(`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8 COLLATE=utf8_general_ci
```

格式

```sql
CREATE TABLE [IF NOT EXISTS] `表名`(
	`字段名` 列类型 [属性] [索引] [注释],
    `字段名` 列类型 [属性] [索引] [注释],
    ......
    `字段名` 列类型 [属性] [索引] [注释]
)[表类型] [字符集设置] [注释]
```

常用命令

```sql
SHOW CREATE DATABASE school;   -- 查看创建数据库的语句
```

CREATE DATABASE `school` /*!40100 DEFAULT CHARACTER SET utf8 */

```sql
SHOW CREATE TABLE student;     -- 查看student数据表的定义语句
```

CREATE TABLE `student` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT '学号',
  `name` varchar(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
  `pwd` varchar(20) NOT NULL DEFAULT '123456' COMMENT '密码',
  `sex` varchar(2) NOT NULL DEFAULT '女' COMMENT '性别',
  `birthday` datetime DEFAULT NULL COMMENT '出生日期',
  `address` varchar(100) DEFAULT NULL COMMENT '家庭住址',
  `email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```sql
DESC student   -- 显示表的结构
```

![表结构](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%A1%A8%E7%BB%93%E6%9E%84.png)

### 2.5.数据库引擎

```sql
-- 数据库引擎
/*
  INNODB  默认使用
  MYISAM  早些年使用 
*/
```

|            |        MYISAM        |        INNODB        |
| :--------: | :------------------: | :------------------: |
|  事务支持  | 不支持（最新版支持） |         支持         |
| 数据行锁定 |        不支持        |         支持         |
|  外键约束  |        不支持        |         支持         |
|  全文索引  |         支持         | 不支持（最新版支持） |
| 表空间大小 |         较小         |    较大，约为2倍     |

* MYISAM   节约空间，速度较快
* INNODB   安全性高，事务的处理，多表多用户操作

#### 1.物理空间储存位置

所有的数据库文件都存在data目录下，一个文件夹对应一个数据库

本质还是文件从存储

MySQL引擎在物理文件上的区别

* InnoDB在数据库表中只有一个 ***.frm** 文件，以及其上级目录data目录下的 **ibdata1**  文件
* MYISAM 对应的文件
  * *.frm  表结构的定义文件
  * *.MYD   数据文件（data）
  * *.MYI    索引文件（index）

#### 2.设置数据库中表的字符集编码

```sql
CHARSET=utf8
```

必须设置，不设置的话，会使用MySQL默认的字符集编码（不支持中文！）

Mysql的默认编码是Latin1，不支持中文

我们可以在 **my.ini** 中配置默认的编码

```ini
character-set-server=utf8
```

### 2.6.修改和删除表

#### 1.修改表

```sql
-- 修改表名   ALTER TABLE 旧表名 RENAME AS 新表名
ALTER TABLE teacher RENAME AS teacher1
-- 增加表的字段	  ALTER TABLE 表名 ADD 字段名 属性
ALTER TABLE teacher1 ADD age INT(11)
-- 修改表的字段（修改约束，重命名）
-- ALTER TABLE 表名 MODIFY 字段名 列属性()
ALTER TABLE teacher1 MODIFY age VARCHAR(11)   -- 修改约束
-- ALTER TABLE 表名 CHANGE 旧字段名 新字段名 列属性()
ALTER TABLE teacher1 CHANGE age age1 INT(3)   -- 重命名	

-- 删除表的字段
-- ALTER TABLE 表名 DROP 字段名
ALTER TABLE teacher1 DROP age1
```

#### 2.删除表

```sql
-- 删除表 （如果表存在再删除）
-- DROP TABLE IF EXISTS 表名
DROP TABLE IF EXISTS teacher1
```

**所有的创建和删除尽量加上判断，以免报错**

**注意点：**

- `` 字段名，使用这个包裹！
- 注释两种： 单行注释--  和多行注释/**/
- sql 关键字大小写不敏感，建议写小写
- 所有符号全部用英文

## 3.数据库的数据管理

### 3.1.外键【了解】

#### 1.方式一

* grade表

```sql
CREATE TABLE `grade`(
    `gradeid` INT(10) NOT NULL AUTO_INCREMENT COMMENT '年级id',
    `gradename` VARCHAR(50) NOT NULL COMMENT '年级名称',
    PRIMARY KEY(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8 
```

* student表

```sql
-- primary key  主键 ， 一般一个表只有一个唯一的主键	
-- CONSTRAINT 约束名 FOREIGN KEY (作为外键的列) REFERENCES 被引用表(哪个字段)
CREATE TABLE IF NOT EXISTS `student`(
    `id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
    `name` VARCHAR(20) NOT NULL DEFAULT '匿名' COMMENT '姓名',
    `pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
    `sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
    `birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
    `gradeid` INT(10) NOT NULL COMMENT '学生的年级',
    `address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址', 
    `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY(`id`),	
    KEY `FK_gradeid` (`gradeid`),  -- 第一种添加索引的方式
    CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8   
```

外键：

![外键](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%96%E9%94%AE.png)

* 删除有外键关系的表的时候，先删除引用别人的表（从表），再删除被引用的表（主表）

#### 2.方式二

创建表成功后，添加外键约束

* 创建表的时候没有外键关系:

```sql
-- 创建表的时候没有外键关系
-- ALTER TABLE 表 ADD CONSTRAINT 约束名 FOREIGN KEY (作为外键的列) REFERENCES 被引用表(哪个字段)
ALTER TABLE `student`
ADD CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`)
```

**以上的操作都是物理外键，数据库级别的外键，不建议使用（避免数据库过多造成困扰）**

==最佳实践==

* 数据库就是单纯的表，只用来存数据，只有行（数据）和列（字段）
* 想使用多张表的数据，想使用外键（程序去实现）

### 3.2.DML语言【掌握】

**数据库意义**：数据存储，数据管理

DML语言：数据操作语言

* insert
* update
* delete

### 3.3.添加

语法：==insert into 表名（字段一,字段二,字段三,...）VALUES（'值一','值二','值三',...）,(...),(...)== 

```sql
-- insert into 表名（字段一,字段二,字段三,...）VALUES（'值一','值二','值三',...）,(...),(...) 
INSERT INTO `grade` (`gradename`) VALUES('大一');
-- 由于主键自增，可以省略主键
-- 写插入语句，一定要一一对应
INSERT INTO `grade` (`gradename`) 
VALUES('大二'),('大三'),('大四')

-- 插入多条数据
INSERT INTO `student` 
(`name`,`pwd`,`sex`,`birthday`,`gradeid`,`address`,`email`)
VALUES 
('李四','111111','男','1988-02-15',4,'广州白云','lisi@si4,com'),
('王五','111111','男','1985-01-20',4,'潮州潮安','wangwu@wu5,com')
```

注意事项

* 字段之间用 英文逗号 隔开
* 字段可以省略（添加部分），但是 values 后面的值一定要一一对应
* 可以同时插入多条数据，values 每条数据之间用括号隔开  values (...),(...),(...)

### 3.4.修改

update 修改谁  set 字段=新值（修改的条件）

```sql
-- 修改学生名字
-- UPDATE 表名 SET 字段名='新值' WHERE [条件]
update `student` set name='伯格曼' where id=5
-- 在不指定条件的情况下 会修改所有的表

-- UPDATE 表名 SET 字段一='新值'，字段二='新值'，字段三='新值' WHERE [条件]
UPDATE `student` SET NAME='洪尚秀',pwd='123456',address='韩国' WHERE id=4
```

条件： where子句  运算符  id =某个值，>某个值 ，在某个区间

| 操作符         | 含义       | 范围        | 结果  |
| -------------- | ---------- | ----------- | ----- |
| =              | 等于       | 1=2         | false |
| <>或！=        | 不等于     | 1<>2        | true  |
| >,<,>=,<=      | …          | …           | …     |
| between… and … | 在某个区间 | [1,3]       |       |
| and            | &&         | 1=2 and 1<2 | false |
| or             | \|\|       | 1=2 or 1<2  | true  |

```sql
-- 通过多个条件
UPDATE `student` SET `name`='abcd' WHERE NAME='aaaaa' AND sex='男' 
-- 修改多个属性  用英文逗号 ,  隔开
UPDATE `student` SET `birthday`=CURRENT_DATE,gradeid=3 WHERE NAME='cc'
-- 通过变量赋值
UPDATE `student` SET `birthday`=CURRENT_DATE WHERE NAME='cc'
```

语法：

``` sql
update 表名 set column_name=value, [column_name = value, ...] where [条件]
```

注意：

* 写SQL语句时数据库的字段（列），尽量加``
* 修改的时候如果没有条件，会修改所有行该字段的值
* value（新赋的值），可以是一个具体的值，也可以是一个变量  
  *  例如：  SET `birthday`=CURRENT_DATE

```sql
update `student` set `birthday`=current_time where `name`='长江7号' and sex='女';
```

### 3.5.删除

```sql
-- 清空整张表 （避免这样写，会全部删除）
delete from `student`; -- 不会影响自增

-- 删除指定数据
DELETE FROM `student` WHERE id=11;   -- 不会影响自增

-- 清空某张表
TRUNCATE `student`   -- 自增会归零
```

delete 和 TRUNCATE 区别

* 相同点：都能删除数据，都不会删除表结构（如：不会影响自增）
* 不同：
  * TRUNCATE  重新设置 自增会归零
  * TRUNCATE  不会影响事务

```sql
-- 测试delete 和 truncate 的区别
CREATE TABLE `test`(
    `id` INT(4) NOT NULL AUTO_INCREMENT,
    `coll` VARCHAR(20) NOT NULL,
    PRIMARY KEY(`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `test`(`coll`) VALUES ('1'),('2'),('3');  

DELETE FROM `test`;  -- 不会影响自增

TRUNCATE `test`; -- 重置自增, 自增会归零
```

`了解`：`delete删除的问题`，重启数据库，现象：

* InnoDB 自增会从1开始（存储在内存中，断电即失）
* MyISAM 继续从上一个自增量开始（存储在文件中，不会丢失）

## 4.DQL查询数据【最重点】

### 4.1.DQL

（Data Query Language：数据查询语言）

* 所有的查询操作都用它  select
* 简单的查询，复杂的查询都能做
* ==数据库中最核心的语言，最重要的语句==
* 使用频率最高的语句

### 4.2select语法

```sql
select [all | distinct]
{* | table.* | [table.field1 [as alias1] [,table.field2 [as alias2]][,...]]}
from table_name [as table_alias]
[left | right | innner join table_name2 [as table2_alias] on ...]  --连接查询
[where ...] -- 指定结果需满足的条件(根据条件对结果进行筛选)
[group by ...] -- 指定结果按照哪几个字段进行分组
[having ...] -- 过滤分组的记录必须满足的次要条件
[order by 字段名 ...] -- 指定查询记录按一个或多个条件排序
[limit {[offset(起始位置),]row_coun(显示行数)t | row_countOFFSET offset}];
```

**注意：[]括号代表可选的，{}括号代表必选的**



### 4.3.指定查询字段

==语法：SELECT 字段 FROM 表==

```sql
-- 查询所有学生 select 字段 from 表
SELECT * FROM student

-- 指定字段查询
SELECT `studentno`,`studentname` FROM student

-- 别名：给结果起一个别名  AS 可以给字段起别名 也可以给表起别名
SELECT `studentno` AS 学号,`studentname` AS 学生姓名 FROM student AS 学生表

-- 函数 CONCAT(a,b) ：拼接字符串
SELECT 
CONCAT('姓名：',`studentname`) 
AS 新的名字
FROM student
```

语法：`select 字段, ... from 表`

> 有时候，列名字不是那么的见名知意。我们起别名，使用 AS　用法：字段名 as 别名 表名 as 别名       



==去重: distinct==

作用：去除select查询出来的结果中重复的数据，重复数据只显示一条

```sql
-- 查询全部的考试成绩
select * from result

-- 查询哪些同学参加了考试
select `studentno` from result

-- distinct 去重
select distinct `studentno` from result
```

其他一些查询（表达式）

```sql
select version()		-- 查询系统版本		(函数)
select 59+55*5 as 运算结果	-- 计算			(表达式)
select @@auto_increment_increment -- 查询自增的步长	(变量)

-- 学生考试成绩 +1分
SELECT `studentno`,`studentresult` FROM `result`
-- 加一分后
SELECT `studentno`,`studentresult`+1 AS '加一分后' FROM `result`
```

数据库中的表达式：文本值，列，Null，函数，计算表达式，系统变量……

select ==表达式== from 表

### 4.4.where条件子句

作用：检索数据中`符合条件`的值

搜索的条件由一个或者多个表达式组成！ 结果是布尔值

> 逻辑运算符

| 运算符      | 语法               | 描述                             |
| ----------- | ------------------ | -------------------------------- |
| and　&&     | ａ and b 　a && b  | 逻辑与，两个都为真，结果为真     |
| or　   \|\| | a or b　　a \|\| b | 逻辑或，其中一个为真，则结果为真 |
| not　!      | not a　　! a       | 逻辑非，真为假，假为真           |

**尽量使用英文字母**

```sql
--  ============================ where 条件语句 ============================
-- 查询全部成绩
select `studentno`,`studentresult` from `result`

-- 查询考试成绩在 95~100 分之间的数据
-- 使用 and
SELECT `studentno`,`studentresult` FROM `result`  
WHERE `studentresult`>=95 and `studentresult`<=100

-- 使用 &&
select `studentno`, `studentresult` from `result`
where `studentresult`>=95 && `studentresult`<=100

--  使用between (区间)
SELECT `studentno`,`studentresult` FROM `result`  
WHERE `studentresult` between 90 AND 100

-- 除了学号为 1000 之外的学生成绩
-- 使用 !=
SELECT `studentno`,`studentresult` FROM `result`  
where `studentno` != 1000

-- 使用 not
SELECT `studentno`,`studentresult` FROM `result`  
where not `studentno` = 1000
```



> **模糊查询：比较运算符**

| 运算符      | 语法               | 描述                                          |
| ----------- | ------------------ | --------------------------------------------- |
| is null     | a is null          | 如果操作符为null，结果为真                    |
| is not null | a is not null      | 如果操作符不为null，结果为真                  |
| between     | a between b and c  | 若a在b和c之间，结果为真                       |
| **like**    | a like b           | SQL匹配，如果a匹配b，结果为真(可使用模糊查询) |
| **in**      | a in (a1,a2,a3...) | 假设a在a1,或者a2...其中某一个值中，结果为真   |

```sql
-- ================================ 模糊查询 ================================
-- ============= like =============
-- 查询姓 刘 的同学
-- like %(代表0到任意个字符)  _(只指代一个字符)
SELECT `studentno`,`studentname` FROM `student` 
WHERE `studentname` LIKE '刘%';

-- 查询姓 刘 的同学 并且 名字后面只有一个字的
SELECT `studentno`,`studentname` FROM student 
WHERE `studentname` LIKE '刘_';

-- 查询姓 李 的同学 并且 名字后面有两个字的
SELECT `studentno`,`studentname` FROM `student` 
WHERE `studentname` LIKE '李__';

-- 查询名字中有 志 的同学 
SELECT `studentno`,`studentname` FROM `student` 
WHERE `studentname` LIKE '%志%';

-- ============= in(具体的一个或多个值) =============
-- 查询 1001,1002，1003号学生
SELECT `studentno`,`studentname` FROM `student` 
WHERE `studentno` IN (1001,1002,1003)

-- 查询在 北京西城、广东潮州 的学生
SELECT `studentno`,`studentname` FROM `student`
WHERE `address` IN ('北京西城','广东潮州')

-- ========= null 、 not null ==============
-- 查询地址为空的同学 两种情况：null 空字符串''
SELECT `studentno`,`studentname` FROM `student` 
WHERE `address` ='' OR `address` IS NULL

-- 查询有出生日期的同学  不为空
SELECT `studentno`,`studentname` FROM `student` 
WHERE `borndate` IS NOT NULL

-- 查询没有出生日期的同学  为空
SELECT `studentno`,`studentname` FROM `student` 
WHERE `borndate` IS NULL
```

### 4.5.联表查询

#### 1.7种join方式

![7种join方式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/7%E7%A7%8Djoin%E6%96%B9%E5%BC%8F.png)

#### 2.on和where的区别

sql中的连接查询分为3种， cross join，inner join，和outer join ， 在 cross join和inner join中，筛选条件放在on后面还是where后面是没区别的，极端一点，在编写这两种连接查询的时候，只用on不使用where也没有什么问题。因此，on筛选和where筛选的差别只是针对outer join，也就是平时最常使用的left join和right join。

**有下面两条sql查询：**

1、只使用on筛选器

```sql
select * from main left JOIN ext on main.id = ext.id and address <> '杭州'
```

结果：

![on筛选器](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/on%E7%AD%9B%E9%80%89%E5%99%A8.png)

2、使用on筛选器和where筛选器

```sql
select * from main left JOIN ext on main.id = ext.id where address <> '杭州'
```

结果为：

![on加where筛选器](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/on%E5%8A%A0where%E7%AD%9B%E9%80%89%E5%99%A8.png)

**on和where的区别**首先需要从outer join查询的逻辑查询的各个阶段说起。总的来说，outer join 的执行过程分为4步：

**1、先对两个表执行交叉连接(笛卡尔积)**

**2、应用on筛选器**

**3、添加外部行**

**4、应用where筛选器**

**第一步**，对两个表执行交叉连接，结果如下，这一步会产生36条记录（此图显示不全）

![两张表交叉连接(笛卡尔积)](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%A4%E5%BC%A0%E8%A1%A8%E4%BA%A4%E5%8F%89%E8%BF%9E%E6%8E%A5(%E7%AC%9B%E5%8D%A1%E5%B0%94%E7%A7%AF).png)

**第二步**，应用on筛选器。筛选器中有两个条件，**main.id = ext.id and address<> '杭州'**，符合要求的记录如下:

![应用on筛选器](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%BA%94%E7%94%A8on%E7%AD%9B%E9%80%89%E5%99%A8.png)

**第三步**，添加外部行。outer join有一个特点就是以一侧的表为基，假如另一侧的表没有符合on筛选条件的记录，则以null替代。在这次的查询中，这一步的作用就是将那条原本应该被过滤掉的记录给添加了回来

![添加外部行](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B7%BB%E5%8A%A0%E5%A4%96%E9%83%A8%E8%A1%8C.png)

结果就成了这样:

![on筛选器](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/on%E7%AD%9B%E9%80%89%E5%99%A8.png)

**第四步**，应用where筛选器。将所有地址不属于杭州的记录筛选了出来

![on加where筛选器](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/on%E5%8A%A0where%E7%AD%9B%E9%80%89%E5%99%A8.png)

参考链接：[sql连接查询中on筛选与where筛选的区别](https://zhuanlan.zhihu.com/p/26420938)

#### 3.inner/left/right join

>  join

-- join (连接的表) on (判断的条件) 连接查询
-- where 等值查询

**查哪张表，则这张表就可以作为主表(左表), 连接的表就作为右表，left join就以左表为基准，right join就以右表为基准**

* a left join b (以a表为基准)  

* a right join b (以b表为基准)  



| 操作       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| inner join | 内连接，返回两张表的交集                                     |
| left join  | 左连接，会从 左表 中返回所有的值，即使 左表的值 在 右表 中没有匹配 |
| right join | 右连接，会从 右表 中返回所有的值，即使 右表的值 在 左表 中没有匹配 |

```sql
-- ============= 联表查询 ======================

-- 查询参加考试的同学 （学号，姓名[在另外一个表中]，科目编号，成绩）
/*  思路
     1.分析需求，分析查询的字段来自那些表 （连接查询）
     2.确定使用哪种连接方式查询？ 总共7种
	   确定交叉点(交集)：两表之间哪些数据是相同的
	   判断的条件： 学生表中的 `studentno` = 成绩表中的 `studentno`
*/
-- inner(内连接)：保留两张表中完全匹配的结果集
select `student`.`studentno`,`student`.`studentname`,`result`.`subjectno`,`result`.`studentresult`
from `student`
inner join `result`
where `student`.`studentno` = `result`.`studentno`

-- join (连接的表) on (判断的条件) 连接查询
-- where 根据where中的条件对结果进行筛选

-- left join(左连接)：返回左表所有的行，即使在右表中没有匹配的记录
SELECT student.`studentno`,student.`studentname`,result.`subjectno`,result.`studentresult`
FROM student
left JOIN result
on student.`studentno` = result.`studentno`

-- right join(右连接)：返回右表中所有的行，即使在左表中没有匹配的记录
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
RIGHT JOIN result AS r
ON s.`studentno` = r.`studentno`
```

> 案例一       了解联表查询

* left join(左连接)：返回左表所有的行，即使在右表中没有匹配的记录

```sql
-- left join ... on ... where ...
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
left JOIN result AS r
on s.`studentno` = r.`studentno`
```

![左连接查询结果](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B7%A6%E8%BF%9E%E6%8E%A5%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C.png)

左表中的 studentname 为 gokudu 的学生在 右表 中并没有值（即右表中没有studentid为1038的学生），但仍然能查询出来。

应验了 ==left join 会从 左表 中返回所有的值，即使 左表的值 在 右表 中没有匹配==

* right join：返回右表中所有的行，即使在左表中没有匹配的记录

```sql
-- right join ... on ... where ...
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
RIGHT JOIN result AS r
ON s.`studentno` = r.`studentno`
```

![右连接查询结果](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%8F%B3%E8%BF%9E%E6%8E%A5%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C.png)

查询不出来 gokudu ，因为右表中没有studentid 为 1038的学生

查询出来的结果没有null，因为右表中的studentno在左表中都能找到匹配。

> 案例二    利用左表查询，找出缺考的同学

```sql
-- 查询缺考的同学
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
LEFT JOIN result AS r
ON s.`studentno` = r.`studentno`
where `studentresult` is null -- 加上where对连接查询的结果进行筛选
```

> 案例三    思考题（查询参加考试的同学信息：学号，学生姓名，科目名，分数）

```sql
-- 思考题（查询参加考试的同学信息：学号，学生姓名，科目名，分数）
/* 思路
     1.分析需求，分析查询的字段来自哪些表，student，subject，result
     2.确定使用哪种连接方式查询？ 总共7种
     确定交叉点(交集)：两表之间哪些数据是相同的
	   判断的条件： 学生表 `studentno` = 成绩表 `studentno`
			成绩表 `subjectno` = 课程表 `subjectno`
	
*/
select s.`studentno`,s.`studentname`,su.`subjectname`,r.`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
inner join `subject` su
where r.`subjectno` = su.`subjectno`
```

**可以先查询其中的两个表，然后再来与第三个表找连接点**

1. 先把 result表 和 student表 连接起来， result表 左连接 student表 ，连接点是 studentno ，以result表为基准

```sql
select s.`studentno`,`studentname`,`subjectno`,`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
```

2. 通过 subjectno 将上面查询出来的结果 和 subject 表 连接起来

```sql
select s.`studentno`,`studentname`,su.`subjectname`,`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
inner join `subject` su
where r.`subjectno` = su.`subjectno`
```

#### 4.自连接【了解】

自己的表和自己的表连接，核心：**一张表拆成两张表**

> 案例

* 建表

```sql
--  ======= 自连接 ===========
CREATE TABLE category (
  categoryid INT(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主题ID',
  pid INT(10) NOT NULL COMMENT '父ID',
  categoryName VARCHAR(50) NOT NULL COMMENT '主题名字',
  PRIMARY KEY(categoryid)
) ENGINE=INNODB AUTO_INCREMENT=9 DEFAULT CHARSET = utf8;

INSERT INTO category(categoryid,pid,categoryName)
VALUES
('2','1','信息技术'),
('3','1','软件开发'),
('4','3','数据库'),
('5','1','美术设计'),
('6','3','web开发'),
('7','5','PS技术'),
('8','2','办公信息');
```

* 分析

父类

| categoryid | categoryName |
| :--------: | :----------: |
|     2      |   信息技术   |
|     3      |   软件开发   |
|     5      |   美术设计   |

子类

| pid  | categoryid | categoryName |
| :--: | :--------: | :----------: |
|  3   |     4      |    数据库    |
|  3   |     6      |   web开发    |
|  5   |     7      |    PS技术    |
|  2   |     8      |   办公信息   |

操作：查询父类对应的子类关系

|   父类   |   子类   |
| :------: | :------: |
| 信息技术 | 办公信息 |
| 软件开发 |  数据库  |
| 软件开发 | web开发  |
| 美术设计 |  PS技术  |

* 测试

```sql
-- 查询父子信息：把一张表分成两张一样的表来进行查询
SELECT a.`categoryName` AS '父栏目',b.`categoryName` AS '子栏目'
FROM `category` AS a,`category` AS b
WHERE a.`categoryid`=b.`pid`
```

> 案例二    查询参加 数据库结构 考试的同学信息

```sql
-- 思考题（查询参加 数据库结构 考试的同学信息：学号，学生姓名，科目名，分数）
/* 思路
     1.分析需求，分析查询的字段来自哪些表，student，subject，result
     2.确定使用哪种连接方式查询？ 总共7中
     确定交叉点：两表之间哪些数据是相同的
	   判断的条件： 学生表 `studentno` = 成绩表 `studentno`
			成绩表 `subjectno` = 课程表 `subjectno`
	
*/
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
```

### 4.6.分页和排序

#### 1.排序

order by 某字段 desc/asc(降序/升序)

```sql
-- 思考题（查询参加 数据库结构 考试的同学信息：学号，学生姓名，科目名，分数）
--  根据成绩降序排序
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
RIGHT JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
ORDER BY `studentresult` DESC -- 降序排序
```

#### 2.分页

语法：limit 查询起始位置，页面大小       通用公式：limit (n-1) * pageSize，pageSize

```sql
-- 语法  limit 起始位置，页面大小
-- 每页显示 5 条数据
-- limit 0,5   表示 1~5 条数据
-- limit 1,5   表示 2~6 条数据

SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
RIGHT JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
ORDER BY `studentresult` DESC
LIMIT 0,5 -- 显示第 1~5条数据

-- 第一页 limit 0,5    (1-1) * 5
-- 第二页 limit 5,5    (2-1) * 5
-- 第三页 limit 10,5   (3-1) * 5
    ...                  ...
-- 第n页 limit 15,5    (n-1) * pageSize
-- 页面大小：pageSize
-- 起始值：(n-1) * pageSize
-- 当前页：n
-- 总页数：数据总数/页面大小(向上取整)
```

查询 C语言-3 课程成绩排名前十  并且分数要大于80 的学生信息（学号，姓名，课程名称，分数）

```sql
-- 查询 C语言-3 课程成绩排名前十  并且分数要>=80 的学生信息（学号，姓名，课程名称，分数）
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
inner JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE 'C语言-3' and r.`studentresult` >= 80
ORDER BY `studentresult` DESC
LIMIT 0,10
```

### 4.7.子查询

where（这个值是计算出来的）

本质：==在where语句中在嵌套一个子查询语句==

```sql
-- ============== where 子查询 =============
-- 查询 数据库结构-1 的所有考试结果 学号，科目编号，成绩   降序排序
-- 方式一：使用连接查询
SELECT r.`studentno`,r.`subjectno`,r.`studentresult`
FROM `result` r
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '数据库结构-1' 
ORDER BY `studentresult` DESC

-- 方式二：使用子查询 (由里及外)
SELECT `studentno`,`subjectno`,`studentresult`
FROM `result`
WHERE `subjectno` = (
	SELECT `subjectno` FROM `subject`
	WHERE `subjectname` LIKE '数据库结构-1' 
)
ORDER BY `studentresult` DESC
```

子查询部分

```sql
WHERE `subjectno` = (
	SELECT `subjectno` FROM `subject`
	WHERE `subjectname` LIKE '数据库结构-1' 
)
```

> 案例     高等数学-1   分数不少于80分的学号和姓名

使用子查询

```sql
-- 使用子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
AND `subjectno` = (
	SELECT `subjectno` 
	FROM`subject`
	WHERE `subjectname` LIKE '高等数学-1'
)
```

对比联表查询

```sql
-- 分数不少于80分的学号和姓名
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
-- 在这个基础上 在限制一个科目  高等数学-1
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
INNER JOIN `subject` sub
ON sub.`subjectno` = r.`subjectno`
WHERE r.`studentresult`>=80
AND sub.`subjectname` LIKE '高等数学-1'

-- 使用子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
AND `subjectno` =(
	SELECT `subjectno` 
	FROM`subject`
	WHERE `subjectname` LIKE '高等数学-1'
)
```

> 案例     查询课程为 高等数学-3 的分数不小于75 的同学的学号和姓名

分别使用 联表查询、子查询 实现

```sql
-- 查询课程为 高等数学-3 的分数不小于75 的同学的学号和姓名
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE `studentresult`>=75 
AND `subjectname` LIKE '高等数学-3'

-- 子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
WHERE `studentresult`>=75 
AND `subjectno` = (
	SELECT `subjectno`
	FROM `subject`
	WHERE `subjectname` LIKE '高等数学-3'
)
```

对两个条件都使用子查询

```sql
-- 两个条件都使用子查询
SELECT DISTINCT `studentno`,`studentname`
FROM `student` 
WHERE `studentno` IN (
	SELECT `studentno`
	FROM `result`
	WHERE `studentresult`>=75 
	AND `subjectno` = (
		SELECT `subjectno`
		FROM `subject`
		WHERE `subjectname` LIKE '高等数学-3'
	)
)
```

> 练习    使用子查询 ：   查询 C语言-1 前五名同学的成绩   学号 姓名 分数

```sql
-- 练习 查询 C语言-1 前五名同学的成绩   学号 姓名 分数
-- 使用连接查询加子查询
select	`student`.`studentno`,`student`.`studentname`,`result`.`studentresult`
from `student`
inner join `result`
on `student`.`studentno` = `result`.`studentno`
where `result`.`subjectno` = (
	select `subjectno` from `subject` where `subjectname` = 'C语言-1'
)
order by `result`.`studentresult` desc
limit 0,5
```

### 4.8.分组和过滤

语法 ： GROUP BY 字段

`where` 在分组之前使用

`having` 在分组之后使用(分组过滤)

```sql
-- 查询不同课程的平均分，最高分，最低分，平均分大于80
-- 核心：根据不同的课程分组
SELECT 	`subjectname`,
	AVG(`studentresult`) AS '平均分',
	MAX(`studentresult`) AS '最高分',
	MIN(`studentresult`) AS '最低分'
FROM `result` r
INNER JOIN `subject` sub
ON r.`subjectno`= sub.`subjectno`
GROUP BY r.`subjectno`   -- 通过什么字段分组
-- 再此基础上 要求平均分大于80
HAVING '平均分' > 80	-- 分组之后使用 having 来过滤条件
```

### 4.9.select小结

```sql
select 去重 要查询的字段 from 表 (注意：表和字段可以取别名)
xxx(inner,left,right,full) join 要连接的表 on 等值判断
where 具体的过滤条件 或 子查询语句 注意：where过滤条件中不能包含聚合函数
group by 通过哪个字段进行分组
having 过滤分组后的信息，过滤条件可包含聚合函数
order by 通过哪个字段进行排序 asc/desc(升序/降序)
limit 起始显示的位置, 显示条数 (分页) 如: 0, 5 从第一条数据开始显示，总共显示5条数据

注意顺序

业务层面：
查询：可能跨表，跨数据库...
```



## 5.MySQL函数

官网： https://dev.mysql.com/doc/refman/5.7/en/

### 5.1.常用函数

```sql
-- ========= 常用函数 ========= 

-- 数学运算
SELECT ABS(-8)   -- 绝对值
SELECT CEILING(9.5)  -- 向上取整
SELECT FLOOR(9.5)  -- 向下取整
SELECT RAND()   -- 返回一个0到1的随机数
SELECT SIGN(10)   -- 返回一个数值的符号   输入0 返回0   负数返回 -1  正数返回 1 

-- 字符串函数
SELECT CHAR_LENGTH('伯格曼的假面')  -- 字符串长度
SELECT CONCAT('张三','打','李四')   -- 拼接字符串
SELECT INSERT('我爱编程',1,2,'超级热爱')   -- 替换字符串  从某个位置开始，替换 n 个字符串
					       -- 1,2 代表从 第1个字符开始，替换两个字符
						   -- "我爱"  ==>  "超级热爱"
						   -- '我爱编程' ==> '超级热爱编程'

SELECT UPPER('Gokudu')	-- 转大写
SELECT LOWER('Gokudu')	-- 转小写
SELECT INSTR('gokudu','u')  -- 返回第一次出现的 子串 的索引
SELECT SUBSTR('伯格曼和塔可夫斯基',5,5)   -- 返回子串  第五位开始，截取五个字符，结果：'塔可夫斯基'
SELECT SUBSTR('伯格曼和塔可夫斯基',5)     -- 返回子串  第五位开始，截取到尾部，结果：'塔可夫斯基'
SELECT REPLACE('伯格曼和塔可夫斯基走到一起，伯格曼说','伯格曼','沟口健二')
SELECT REVERSE('abcdefg')  -- 反转

-- 查询姓周的同学  改为  邹
SELECT REPLACE(studentname,'周','邹')
FROM student 
WHERE studentname LIKE '周%'

-- 时间和日期函数 (重点)
SELECT CURRENT_DATE()	-- 获取当前时间
SELECT CURDATE()	-- 获取当前时间
SELECT NOW()	  -- 获取当前时间(毫秒)
SELECT LOCALTIME() -- 本地时间
SELECT SYSDATE()    -- 系统时间

SELECT YEAR(CURRENT_DATE())   -- 获取年
SELECT MONTH(CURRENT_DATE())  -- 获取月
SELECT DAY(CURRENT_DATE())    -- 获取天
SELECT HOUR(NOW())            -- 获取小时
SELECT MINUTE(NOW())          -- 获取分钟
SELECT SECOND(NOW())          -- 获取秒

-- 系统
SELECT SYSTEM_USER()
SELECT USER()
SELECT VERSION()
```



### 5.2.聚合函数(常用)

**注意**：`where`条件中不能有`聚合函数`，`having`条件中可使用`聚合函数`

| 函数名称  | 描述   |
| --------- | ------ |
| count（） | 计数   |
| sum()     | 求和   |
| avg()     | 平均值 |
| max()     | 最大值 |
| min()     | 最小值 |
| ...       | ...    |

```sql
-- ============ 聚合函数 =============
-- 查询一个表中有多少条记录
select count(studentname) from student;  -- count(指定列/字段)  会忽略所有null  
select count(*) from student;  -- count(*) -- 不会忽略null	
SELECT COUNT(1) FROM student;  -- count(1) -- 不会忽略null

select sum(`studentresult`) as '总和' from `result`
select avg(`studentresult`) as '平均数' from `result`
select max(`studentresult`) as '最高分' from `result`
select min(`studentresult`) as '最低分' from `result`
```

```txt
select(*)与select(1) 在InnoDB中性能没有任何区别，处理方式相同。
官方文档描述如下：InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference.
```

博客：MySQL count(*),count(1),count(field)区别、性能差异及优化建议

https://baijiahao.baidu.com/s?id=1660139166311547332&wfr=spider&for=pc

### 5.3.count(*)、count(1)、count(列名)区别

**执行效果上：**

* `count(*)`包括了所有的列，相当于统计行数，在统计结果的时候，不会忽略列值为`null`的行
* `count(1)`会统计表中的所有记录数，包括字段为`null`的记录
* `count(列名)`只包括列名那一列，在统计结果的时候，会忽略列值为空(这里的列值为空不是指`空字符串`或者`0`，而是表示`null`)的计数，即某个字段值为`null`时，不统计

**执行效率上：**

* 列名为主键，`count(列名`)会比`count(1)`快
* 列名不为主键，`count(1)`会比`count(列名)`快
* 如果表多个列没有主键，则`count(1)`的执行效率优于`count(*)`
* 如果有主键，则`count(主键)`的执行效率是最优的
* 如果表只有一个字段，则`count(*)`最优
* 当表数据量大时(大于1w数据量)，对表作分析之后，使用`count(*)`的用时比`count(1)`少
* 当表数据量少时(1w以内数据量)，在做过表分析之后，`count(1)`会比`count(*)`用时少

### 5.4.数据库级别的MD5加密（扩展）

MD5相比其前身，主要增强了算法复杂度和==**不可逆性**==

MD5不可逆，具体的值的md5不变

所以一些常用的数据转为md5不安全，有人会把常见的值做成一个数据字典（md5加密后的值：md5加密前的值），根据该字典对常见值进行破解。

```sql
-- ============ 测试MD5加密 ===============
CREATE TABLE `testmd5`(
    `id` INT(4) NOT NULL,
    `name` VARCHAR(20) NOT NULL,
    `pwd` VARCHAR(50) NOT NULL,
    PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8;

-- 明文密码
INSERT INTO `testmd5` (`id`, `name`, `pwd`)
VALUES (1, 'zhangsan', '123456'),
(2, 'lisi', '123456'),
(3, 'wangwu', '123456');

-- 对id为1的行的'pwd'字段进行加密
UPDATE `testmd5` SET `pwd`=MD5(`pwd`) WHERE `id`=1;

-- 加密所有行
UPDATE `testmd5` SET `pwd`=MD5(`pwd`);

-- 插入的时候加密
INSERT INTO `testmd5` (`id`, `name`, `pwd`) VALUES (4, 'xiaoming', MD5(123456));

-- 校验，查询名字为'xiaoming'，密码为123456的用户
SELECT `id`,`name`,`pwd` FROM `testmd5` WHERE `name`='xiaoming' AND `pwd`=MD5(123456);
```



## 6.事务

### 6.1.什么是事务

==要么都成功，要么都失败==

**概念：**[数据库](https://baike.baidu.com/item/数据库/103728)事务( transaction)是访问并可能操作各种[数据项](https://baike.baidu.com/item/数据项/3227309)的一个数据库操作序列，这些操作要么全部执行,要么全部不执行，是一个不可分割的工作单位。事务由事务开始与事务结束之间执行的全部数据库操作组成。

1.SQL执行  A给B转账         A 1000  --> (200)        B  300

2.SQL执行  B收到A的钱     A  800                          B  500

### 6.2.事务原则

**ACID原则** ：原子性，一致性，隔离性，持久性　　

参考博客：https://blog.csdn.net/dengjili/article/details/82468576

**原子性（Atomicity）**

要么都成功，要么都失败

**一致性（Consistency）**
事务前后数据的完整性必须保持一致。（别人转账中，双方的账户总金额不变）   

**隔离性（Isolation）**
事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。

**持久性（Durability）**
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

### 6.3.隔离所导致的一些问题

**脏读：**

指一个事务读取了另一个事务未提交的数据(更新前的数据)。

 ==例子：==

A  500   B 200   C 200

A --->(200) B  A向B转200元

C --->(100) B  C向B转100元



1、C在 A转给B 200 未提交时 ，自己转给B 100 ，这时他读到的B的值是初始状态的值为200，结果为 B：200+100=300  ，C：200-100=100

2、然后 A转给B 200元 事务提交之后 ， 结果为：A：500-200=300　B：200+200=400 

最终：结果为 A：300　B：400　C：100，总值少了100



|               会话1                |                   会话2                    |
| :--------------------------------: | :----------------------------------------: |
|               begin                |                   begin                    |
|                                    | update tablename set age = 10 where id = 1 |
| select age from table where id = 1 |                                            |
|               commit               |                   commit                   |

会话1得到的`age`的值是会话2更新前的值



**不可重复读：**

在一个事务内读取表中的某一行数据，多次读取结果不同。（不一定是错误的）

原因：在本次事务提交前，某值被其他事务修改并且提交，导致本次事务前后读取的结果不同



|               会话1                |                   会话2                    |
| :--------------------------------: | :----------------------------------------: |
|               begin                |                   begin                    |
| select age from table where id = 1 |                                            |
|                                    | update tablename set age = 10 where id = 1 |
|                                    |                   commit                   |
| select age from table where id = 1 |                                            |
|               commit               |                                            |

由于在读取中间变更了数据，所以会话 1 事务查询期间的得到的结果就不一样了。



**虚读(幻读)：**

指在一个事务内读取到别的事务插入的数据，导致前后不一致。（一般是行影响，多了一行）



|               会话1                |                   会话2                    |
| :--------------------------------: | :----------------------------------------: |
|               begin                |                   begin                    |
| select age from table where id > 2 |                                            |
|                                    | insert into table (id, age) values (5, 10) |
|                                    |                   commit                   |
| select age from table where id > 2 |                                            |
|               commit               |                                            |



### 6.4.MySQL数据隔离级别

**MySQL 里有四个隔离级别：**

1. Read uncommittied (可读取未提交数据)

   (1) 所有事务都可以看到其他未提交事务的执行结果
   (2) 本隔离级别很少用于实际应用，因为它的性能也不比其他级别好多少

   

2. Read committed (可读取已提交数据)

   (1) 这是大多数数据库系统的默认隔离级别（但不是MySQL默认的）
   (2) 它满足了隔离的简单定义：一个事务只能看见已经提交事务所做的改变

   

3. Repeatable read (可重复读，MySQL默认事务隔离级别)

   (1) 这是MySQL的默认事务隔离级别
   (2) 它确保同一事务的多个实例在并发读取数据时，会看到同样的数据行

   

4. Serialization (可串行化)

   (1) 这是最高的隔离级别
   (2) 它通过强制事务排序，使之不可能相互冲突，从而解决幻读问题。简言之,它是在每个读的数据行上加上共享锁。
   (3) 在这个级别，可能导致大量的超时现象和锁竞争



不同事务隔离级别的效果：

| 隔离级别                    | 脏读 | 不可重复读 | 幻读 |
| --------------------------- | ---- | ---------- | ---- |
| 读未提交 (Read uncommitted) | √    | √          | √    |
| 读已提交 (Read committed)   | ×    | √          | √    |
| 可重复读 (Repeatable read)  | ×    | ×          | √    |
| 可串行化 (Serializable)     | ×    | ×          | ×    |

在 `InnoDB` 中，默认为 `Repeatable` 级别，`InnoDB` 中使用一种被称为 `next-key locking` 的策略来避免幻读（phantom）现象的产生。

隔离级别越高，越能保证数据的完整性和一致性，但是对并发性能的影响也越大。



### 6.5.执行事务

事务执行流程：

![事务执行流程](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%BA%8B%E5%8A%A1%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

```sql
-- ========================== 事务 ===========================

-- mysql默认开启事务自动提交
SET autocommit = 0  -- 关闭自动提交
SET autocommit = 1  -- 开启自动提交(默认)

-- 手动开启事务
SET autocommit = 0  -- 关闭自动提交

-- 事务开启
START TRANSACTION -- 标记一个事务的开始，从这个之后的 sql 都在同一个事务内

INSERT ...  -- sql语句
INSERT ...

-- 提交: 持久化（成功）
COMMIT

-- 回滚：回到原来的样子（失败）
ROLLBACK

-- 事务结束
SET autocommit = 1  -- 开启自动提交 (回到默认)

-- 了解
SAVEPOINT 保存点名 -- 设置一个事务的保存点
ROLLBACK TO SAVEPOINT 保存点名 --  回滚到保存点
RELEASE SAVEPOINT 保存点名 -- 撤销保存点
```



**模拟转账**

```sql
-- 创建数据库 shop
CREATE DATABASE shop CHARACTER SET=utf8 COLLATE=utf8_general_ci;

-- 使用 shop 数据库
USE shop; 

CREATE TABLE `account`(
    `id` INT(3) NOT NULL AUTO_INCREMENT,
    `name` VARCHAR(30) NOT NULL,
    `money` DECIMAL(9,2) NOT NULL,  -- decimal() 第1个参数表示这个数的总位数，第二个参数表示小数的位数
    PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8;

-- 插入数据
INSERT INTO `account` (`name`,`money`) VALUES ('A',2000.00),('B',10000.00);

-- 模拟转账：事务
SET autocommit = 0; -- 关闭自动提交
START TRANSACTION; -- 开启一个事务

UPDATE `account` SET `money`=`money`-500 WHERE `name` = 'A'; -- A减500
UPDATE `account` SET `money`=`money`+500 WHERE `name` = 'B'; -- B加500

COMMIT; -- 提交事务，就被持久化了！(无法回滚)
ROLLBACK; -- 回滚

SET autocommit = 1; -- 开启自动提交(恢复默认值)
```



## 7.索引

参考链接：[MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)

> MySQL官方对索引的定义为：索引（Index）是帮助**MySQL高效获取数据**的**数据结构**。
>
> 提取句子主干，就可以得到索引的本质：索引是数据结构。

### 7.1.索引的分类

* 主键索引   (PRIMARY KEY)
  * 唯一的标识。**主键** 不可重复，主键约束可以是一个列或者是列的组合，其值能唯一标识表中的每一行。这样的一列或多列称为表的主键 。它是一种特殊的唯一索引，不允许有空值。一个表只能有一个主键。
* 唯一索引   (UNIQUE KEY)
  * 字段的值不可重复。一张表中可以标识多个**唯一索引**。它与普通索引类似，不同的就是：普通索引允许被索引的数据列包含重复的值。而唯一索引列的值必须唯一，但允许有空值。如果是组合索引，则列值的组合必须唯一。
* 常规索引   (KEY/INDEX)
  * **默认的**。KEY/INDEX 关键字设置，这是最基本的索引，它没有任何限制。普通索引（由关键字KEY或INDEX定义的索引）的唯一任务是加快对数据的访问速度。因此，应该只为那些最经常出现在查询条件(WHERE column = …)或排序条件(ORDER BY column)中的数据列创建索引。
* 全文索引   (FULLTEXT)
  * 在特定的数据库引擎下才有（MyISAM）
  * 快速定位数据
  * 查找的是文本中的关键词，主要用于全文检索

> 基础语法

```sql
 -- 索引的使用
-- 1.在创建表的时候，给字段增加索引
-- 2.在已经创建的表中，增加索引

-- 显示所有索引信息
SHOW INDEX FROM student

-- 第二种添加索引的方式
-- 增加一个全文索引（其他索引同理）     索引名（列名）（索引名可省略）
ALTER TABLE student ADD FULLTEXT `studentname`(`studentname`);

-- 创建一个索引  id_表名_字段名
-- CREATE INDEX 索引名 ON 表(字段); (第三种添加索引的方式)
CREATE INDEX id_app_user_name ON app_user(`name`);

-- explain 分析sql执行的情况

EXPLAIN SELECT * FROM student;  --  非全文索引
EXPLAIN SELECT * FROM student WHERE MATCH(studentname) AGAINST('李');
```

### 7.2 添加删除索引

**添加索引：**

**第一种(创建表的时候添加)：**

```sql
CREATE TABLE `student`(
    `StudentNo` INT(4) NOT NULL COMMENT '学号',
    `LoginPwd` VARCHAR(20) DEFAULT NULL COMMENT '登录密码',
    `StudentName` VARCHAR(20) DEFAULT NULL COMMENT '学生姓名',
    `Sex` TINYINT(1) DEFAULT NULL COMMENT '性别，取0或1',
    `GradeId` INT(11) DEFAULT NULL COMMENT '年级编号',
    `Phone` VARCHAR(50) NOT NULL COMMENT '联系电话，允许为空，即可选输入',
    `Address` VARCHAR(255) NOT NULL COMMENT '地址，允许为空，即可选输入',
    `BornDate` DATETIME DEFAULT NULL COMMENT '出生日期',
    `Email` VARCHAR(50) NOT NULL COMMENT '邮箱账号，允许为空，即可选输入',
    `IdentityCard` VARCHAR(18) DEFAULT NULL COMMENT '身份证号',
    PRIMARY KEY(`StudentNo`),
    UNIQUE KEY `IdentityCard` (`IdentityCard`), -- 第一种添加索引的方式
    KEY `Email` (`Email`),
    CONSTRAINT `FK_GradeId` FOREIGN KEY (`GradeId`) REFERENCES `grade`(`GradeId`) -- 设置外键
)ENGINE=MYISAM DEFAULT CHARSET=utf8;
```



**第二种(表创建后添加，使用alter)：**

```sql
-- 增加一个全文索引（其他索引同理）     索引名（列名）（索引名可省略）
ALTER TABLE student ADD FULLTEXT `studentname`(`studentname`);
```



**第三种(表创建后添加，使用create)：**

```sql
-- CREATE INDEX 索引名 ON 表(字段); (第三种添加索引的方式)
CREATE INDEX id_app_user_name ON app_user(`name`);
```



**删除索引：**

```sql
drop index index_name on table_name;
alter table table_name drop index index_name;
alter table table_name drop primary key;
```



### 7.3.测试索引

* 新建数据库，添加100万条数据进行测试

```sql
CREATE TABLE `app_user` (
	`id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(50) DEFAULT '' COMMENT '用户昵称',
	`email` VARCHAR(50) NOT NULL COMMENT '用户邮箱',
	`phone` VARCHAR(20) DEFAULT '' COMMENT '手机号',
	`gender` TINYINT(4) UNSIGNED DEFAULT '0' COMMENT '性别（0：男，1：女）',
	`password` VARCHAR(100) NOT NULL DEFAULT '' COMMENT '密码',
	`age` TINYINT(4) DEFAULT NULL COMMENT '年龄',
	`create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
	`update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
	PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8 COMMENT='app用户表'


-- 插入100万条数据
-- 写函数之前必须要写，标志
DELIMITER $$
-- set global log_bin_trust_function_creators=TRUE;
CREATE FUNCTION mock_data()
RETURNS INT DETERMINISTIC
BEGIN
    DECLARE num INT DEFAULT 1000000;
    DECLARE i INT DEFAULT 0;
    
    WHILE i<num DO
        INSERT INTO app_user(`name`,`email`,`phone`,`gender`,`password`,`age`)
        VALUES (CONCAT('用户',i),CONCAT(FLOOR(RAND()*(999999999-100000000)+100000000),'@qq.com'),FLOOR(RAND()*9999999999+10000000000),FLOOR(RAND()*2),UUID(),FLOOR(100*RAND()));
        SET i = i + 1;
    END WHILE;
    RETURN 0;
END;

SELECT mock_data(); -- 执行此函数 生成一百万条数据

SELECT COUNT(*) FROM `app_user`; -- 统计数据条数
```

* 查询`用户9999`的信息

```sql
select * from `app_user` where `name` = '用户9999';
```

![查询用户9999所花的时间](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%9F%A5%E8%AF%A2%E7%94%A8%E6%88%B79999%E6%89%80%E8%8A%B1%E7%9A%84%E6%97%B6%E9%97%B4.png)

* 分析上述查询语句，发现期间共查询了992262条数据

```sql
EXPLAIN SELECT * FROM `app_user` WHERE `name` = '用户9999'; 
```

![分析查询语句](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%88%86%E6%9E%90%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5.png)

* 创建一个索引(第三种方式)

```sql
-- 创建一个索引  id_表明_字段名
-- CREATE INDEX 索引名 ON 表(字段);
CREATE INDEX id_app_user_name ON `app_user`(`name`);
```

* 测试

```sql
SELECT * FROM `app_user` WHERE `name` = '用户9999';
```

![添加索引后的查询时间](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B7%BB%E5%8A%A0%E7%B4%A2%E5%BC%95%E5%90%8E%E7%9A%84%E6%9F%A5%E8%AF%A2%E6%97%B6%E9%97%B4.png)

* 分析上述添加索引后的查询语句，发现期间共查询了1条数据

```sql
EXPLAIN SELECT * FROM `app_user` WHERE `name` = '用户9999'; 
```

![分析添加索引后的查询语句](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%88%86%E6%9E%90%E6%B7%BB%E5%8A%A0%E7%B4%A2%E5%BC%95%E5%90%8E%E7%9A%84%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5.png)

==索引在数据量较小的时候，感觉不到差别==

==但是在数据量很大的时候，区别十分明显==

### 7.4.为什么索引能提高查询速度

博客： 
[数据库索引总结](https://github.com/Light-Alex/JavaGuide/blob/master/docs/database/MySQL%20Index.md)
[数据库两大神器【索引和锁】](https://juejin.im/post/5b55b842f265da0f9e589e79)

![MySQL的基本存储结构是页](https://camo.githubusercontent.com/57a746bf254e100c3fd0d2691d172df5c29592eb/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f32383535393432312e6a7067)

![img](https://camo.githubusercontent.com/a0e0c5b1377f6ab52365479c52313f4238550d31/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f38323035333133342e6a7067)

- **各个数据页可以组成一个双向链表**
- 每个数据页中的记录又可以组成一个单向链表
  - 每个数据页都会为存储在它里边儿的记录生成一个页目录，在通过主键查找某条记录的时候可以在页目录中使用二分法快速定位到对应的槽，然后再遍历该槽对应分组中的记录即可快速找到指定的记录
  - 以其他列(非主键)作为搜索条件：只能从最小记录开始依次遍历单链表中的每条记录。

所以说，如果我们写select * from user where indexname = 'xxx'这样没有进行任何优化的sql语句，默认会这样做：

1. **定位到记录所在的页：需要遍历双向链表，找到所在的页**
2. **从所在的页内中查找相应的记录：由于不是根据主键查询，只能遍历所在页的单链表了**

很明显，在数据量很大的情况下这样查找会很慢！这样的时间复杂度为O（n）。

### 7.5.使用索引之后

索引做了些什么可以让我们查询加快速度呢？其实就是**将无序的数据变成有序(相对)**：

![img](https://camo.githubusercontent.com/83e4b2a638e8352a21feafeafe97cbad0fc2a335/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f353337333038322e6a7067)

要找到id为8的记录简要步骤：

![img](https://camo.githubusercontent.com/c63688b141c3562bbf4fb4b719ab027c6dea91e9/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f38393333383034372e6a7067)

很明显的是：没有用索引我们是需要遍历双向链表来定位对应的页，现在通过 **“目录”** 就可以很快地定位到对应的页上了！（二分查找，时间复杂度近似为O(logn)）

其实底层结构就是B+树，B+树作为树的一种实现，能够让我们很快地查找出对应的记录。

### 7.6.索引原则

* 索引不是越多越好
* 不要对经常变动数据加索引
* 小数据量的表不需要加索引
* 索引一般加在查询的字段上



> 索引的数据结构

Hash类型的索引

Btree  ： InnoDB 的默认类型

B+Tree

博客： https://blog.codinglabs.org/articles/theory-of-mysql-index.html

​			 [https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL%20Index.md](https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL Index.md)

## 8.权限管理和数据库备份

### 8.1.用户管理

> SQLyog 可视化管理

![用户管理](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%94%A8%E6%88%B7%E7%AE%A1%E7%90%86.png)

> 命令操作

用户表:  mysql.user

本质：对这张表进行增删改查

```sql
-- 创建用户 create user 用户名 identified by '密码'
CREATE USER gokudu IDENTIFIED BY '123456'
-- create user gokudu identified with mysql_native_password by '123456'; -- mysql:8.0以上要加 with mysql_native_password 可视化工具才能连接该用户

-- 修改当前用户密码
SET PASSWORD=PASSWORD('123456')

-- 修改指定用户密码
SET PASSWORD FOR gokudu = PASSWORD('123456')

-- 重命名 rename user 旧用户名 to 新的用户名
RENAME USER gokudu TO gokufriday

-- 用户授权：授予全部权限   grant 全部的权限 on 全部库.全部表 to gokufriday
-- all privileges 除了给其他人授权没有权限(没有grant权限)  其他都能干 
GRANT ALL PRIVILEGES ON *.* TO gokufriday

-- 查询权限
SHOW GRANTS FOR gokufriday  -- 查看指定用户的权限
SHOW GRANTS FOR root@localhost	

-- root用户多了一个grant权限
-- ROOT用户： GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION

-- 撤销权限  revoke 全部的权限 on 全部库.全部表 from gokufriday
REVOKE ALL PRIVILEGES ON *.* FROM gokufriday

-- 删除用户
DROP USER gokufriday
```

### 8.2.MySQL 备份

需要备份的原因

* 保证数据不丢失
* 数据转移

MySQL数据库备份的方式

* 拷贝物理文件

* 使用可视化工具导出

  + 在想要导出的表或者库中，右键，选择备份/导出，选择备份表作为SQL转储，如下图所示：

  ![SQL转储](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/SQL%E8%BD%AC%E5%82%A8.png)

  * sql文件内容：

  ![sql文件内容](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/sql%E6%96%87%E4%BB%B6%E5%86%85%E5%AE%B9.png)

* 使用命令导出(终端)   mysqldump 

导出：

```bash
# mysqldump -h主机 -u用户 -P端口号 -p密码 数据库 表名 > 物理磁盘位置/文件名
mysqldump -hlocalhost -uroot -P3306 -p123456 school student > d:/student.sql
mysqldump: [Warning] Using a password on the command line interface can be insecure.

# 导出多张表
# mysqldump -h主机 -u用户 -p密码 数据库 表1 表2 表3 ... > 物理磁盘位置/文件名
>mysqldump -hlocalhost -uroot -P3306 -p123456 school student result grade > d:/b.sql

# 导出某个数据库
# mysqldump -h主机 -u用户 -p密码 数据库 > 物理磁盘位置/文件名
mysqldump -hlocalhost -uroot -P3306 -p123456 school > d:/c.sql
```

sql文件内容：

![命令导出sql文件内容](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%91%BD%E4%BB%A4%E5%AF%BC%E5%87%BAsql%E6%96%87%E4%BB%B6%E5%86%85%E5%AE%B9.png)

导入：

```bash
# 在登录的情况下，使用source 物理磁盘位置/文件名(注意路径不能包含中文)
# 若导入数据库，则不用切换数据库
# 若导入表，则需要切换对应的数据库 use 数据库名;
source d:/c.sql

# 方式二(该方式路径中可包含中文)：
mysql -h主机地址 -u用户名 -P端口号 -p密码 数据库名 < 要导入的sql文件
```

## 9.规范数据库设计

### 9.1为什么需要设计

当数据库比较复杂的时候，更需要规范设计

**糟糕的数据库设计：**

* 数据冗余，浪费空间
* 数据库插入和删除比较麻烦、异常【使用物理外键】
* 程序的性能差

**良好的数据库设计：**

* 节省内存空间
* 保证数据库的完整性
* 方便开发

**软件开发中，关于数据库的设计**

* 分析需求：分析业务和需要处理的数据库的需求
* 概要设计：设计关系图 E-R图



**设计数据库的步骤：（个人博客）**

* 收集信息，分析需求
  * 用户表（用户登录注销，用户的个人信息，写博客，创建分类）
  * 分类表（文章分类，谁创建的）
  * 文章表（文章的信息）
  * 评论表（评论人，回复人）
  * 友链表（友链信息）
  * 自定义表（系统信息，某个关键的字，或者一些主题）key: value
  * 说说表（发表心情.. id... content... create_time）
* 标识实体（把需求落地到每个字段）
* 标识实体之间的关系
  * 写博客：user --> blog
  * 创建分类：user --> category
  * 关注：user --> user
  * 友链：links
  * 评论：user(回复) --> user(评论) --> blog

**建数据库，建表：**

```sql
-- 创建数据库myblog
CREATE DATABASE myblog CHARACTER SET=utf8 COLLATE=utf8_general_ci;

USE myblog;

-- 创建user表
CREATE TABLE `user`(
    `id` INT(10) NOT NULL AUTO_INCREMENT COMMENT '用户唯一id',
    `username` VARCHAR(60) NOT NULL COMMENT '用户名',
    `password` VARCHAR(60) NOT NULL COMMENT '用户密码',
    `sex` VARCHAR(2) COMMENT '性别',
    `age` INT(3) COMMENT '年龄',
    `signature` VARCHAR(200) COMMENT '签名',
    PRIMARY KEY (`id`)
)ENGINE=INNODB CHARSET=utf8 COLLATE=utf8_general_ci;

-- 创建分类表
CREATE TABLE `category`(
    `id` INT(10) NOT NULL COMMENT '分类id',
    `category_name` VARCHAR(30) NOT NULL COMMENT '分类标题',
    `create_user_id` INT(10) NOT NULL COMMENT '创建用户id',
    PRIMARY KEY (`id`)
)ENGINE=INNODB CHARSET=utf8 COLLATE=utf8_general_ci;

-- 创建评论表
CREATE TABLE `comment`(
    `id` INT(10) NOT NULL COMMENT '评论id',
    `blog_id` INT(10) NOT NULL COMMENT '评论的文章',
    `user_id` INT(10) NOT NULL COMMENT '评论人',
    `content` VARCHAR(2000) NOT NULL COMMENT '评论内容',
    `create_time` DATETIME NOT NULL COMMENT '评论时间',
    `user_id_parent` INT(10) NOT NULL COMMENT '回复人id',
    PRIMARY KEY (`id`)
)ENGINE=INNODB CHARSET=utf8 COLLATE=utf8_general_ci;

-- 创建友链表
CREATE TABLE `links`(
    `id` INT(10) NOT NULL COMMENT '友链id',
    `links` VARCHAR(50) NOT NULL COMMENT '网站名称',
    `href` VARCHAR(2000) NOT NULL COMMENT '超链接目标的URL，即网站链接',
    `sort` INT(10) NOT NULL COMMENT '排序',
    PRIMARY KEY (`id`)
)ENGINE=INNODB CHARSET=utf8 COLLATE=utf8_general_ci;

-- 添加字段
ALTER TABLE `user` ADD `open_id` VARCHAR(1000) NOT NULL COMMENT '微信id';
ALTER TABLE `user` ADD `avatar` VARCHAR(1000) NOT NULL COMMENT '头像链接地址';

-- 创建关注中间表
CREATE TABLE `user_follow`(
    `id` INT(10) NOT NULL COMMENT '唯一标识',
    `user_id` INT(10) NOT NULL COMMENT '被关注的用户id',
    `follow_id` INT(10) NOT NULL COMMENT '关注人的用户id',
    PRIMARY KEY (`id`)
)ENGINE=INNODB CHARSET=utf8 COLLATE=utf8_general_ci;
```



### 9.2.三大范式

为什么要数据规范化

* 信息重复
* 更新异常
* 插入异常
  * 无法正常显示信息
* 删除异常
  * 丢失有效信息

**三大范式：**

**第一范式（1NF）**

==原子性==：保证数据表的每一列都是不可分割的`原子性`数据项



举例说明：

==在一个字段中，不能存放多个属性的信息==

比如，在一个家庭信息列中，既有家庭人数信息，又有家庭住址信息

例子：

![不满足第一范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%8D%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%B8%80%E8%8C%83%E5%BC%8F.png)

在上面的表中，“家庭信息”和“学校信息”列均不满足原子性的要求，故不满足第一范式，调整如下：

![满足第一范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%B8%80%E8%8C%83%E5%BC%8F.png)

可见，调整后的每一列都是不可再分的，因此满足第一范式（1NF）；



**第二范式（2NF）**

前提：满足第一范式

每张表只描述一件事情

**在1NF的基础上，非码属性必须完全依赖于候选码（在1NF基础上消除非主属性对主码的部分函数依赖）**

**第二范式需要确保数据库表中的每一列都和主键相关，而不能只与主键的某一部分相关（主要针对联合主键而言）。**

例子：

![不满足第二范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%8D%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%BA%8C%E8%8C%83%E5%BC%8F.png)

在上图所示的情况中，`同一个订单中可能包含不同的产品`，因此`主键`必须是`“订单号”和“产品号”联合组成`，但可以发现，`产品数量、产品折扣、产品价格与“订单号”和“产品号”都相关`，但是`订单金额和订单时间`仅与`“订单号”相关`，与`“产品号”无关`，这样就`不满足第二范式`的要求，调整如下，需分成两个表：

![满足第二范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%BA%8C%E8%8C%83%E5%BC%8F.png)



**第三范式（3NF）**

前提：满足第一范式和第二范式

在2NF基础上，`任何非主属性不依赖于其它非主属性`（在2NF基础上`消除传递依赖`）

`第三范式`确保数据表中的每一列数据都`和主键直接相关`，而`不能间接相关`

例子：

![不满足第三范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%8D%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%B8%89%E8%8C%83%E5%BC%8F.png)

上表中，所有属性都完全依赖于学号，所以满足第二范式，但是“班主任性别”和“班主任年龄”直接依赖的是“班主任姓名”，而不是主键“学号”，所以需做如下调整：

![满足第三范式](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%BB%A1%E8%B6%B3%E7%AC%AC%E4%B8%89%E8%8C%83%E5%BC%8F.png) 

这样以来，就满足了第三范式的要求。

**ps：**如果把上表中的班主任姓名改成班主任教工号可能更确切，更符合实际情况，不过只要能理解就行。



（规范数据库设计）

**规范性和性能的问题**

关联查询的表不能超过三张表

* 考虑商业化的需求和目标（成本，用户体验），考虑数据库的性能更重要
* 在规范性能问题的时候，适当考虑一下数据库的规范性
* 故意给某些表增加一些冗余的字段（从多表查询变为单表查询）
* 故意增加一些计算列（从大数据降为小数据量的查询：==索引==）

## 10.JDBC（重点）

### 10.1.数据库驱动

驱动：显卡、声卡、数据库都需要驱动

![数据库驱动](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%95%B0%E6%8D%AE%E5%BA%93%E9%A9%B1%E5%8A%A8.png)

编写的程序通过 数据库 驱动，和数据库打交道

### 10.2.JDBC

SUN公司为了简化开发人员的操作（对数据库的统一），提供一个规范（Java操作数据库规范），也就是JDBC（Java Database Connectivity）

这些规范的实现由具体的厂商去做

而对于开发人员，只需要掌握JDBC接口的操作

![JDBC](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/JDBC.png)

需要的包：

java.sql

javax.sql

导入数据库驱动包： mysql-connector-java-5.1.47.jar

### 10.3.第一个JDBC程序

* 创建测试数据库

```sql
CREATE DATABASE jdbcStudy CHARSET=utf8 COLLATE=utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
    `id` INT PRIMARY KEY,
    `name` VARCHAR(40),
    `password` VARCHAR(40),
    `email` VARCHAR(60),
    `birthday` DATE
);

INSERT INTO `users` (`id`,`name`,`password`,`email`,`birthday`)
VALUES (1,'zhangsan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04');
```



* 创建一个普通项目
* 导入数据库驱动

![导入数据库驱动](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E9%A9%B1%E5%8A%A8.png)

* 编写测试代码

```java
// 我的第一个JDBC程序
public class JdbcFirstDemo {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1. 加载驱动
        Class.forName("com.mysql.jdbc.Driver"); // 固定写法，加载驱动

        // 2. 用户信息和url
        String url = "jdbc:mysql://主机地址:端口号/jdbcStudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";

        // 3. 连接成功，得到数据库对象 Connection 代表数据库
        Connection connection = DriverManager.getConnection(url, username, password);

        // 4. 创建sql对象
        Statement statement = connection.createStatement();

        // 5. 通过sql对象执行SQL语句，查看返回的结果
        String sql = "select * from users";

        ResultSet resultSet = statement.executeQuery(sql); // 返回结果集，结果集中封装了全部查询出来的结果

        while (resultSet.next()){
            System.out.println("id = " + resultSet.getObject("id"));
            System.out.println("name = " + resultSet.getObject("name"));
            System.out.println("password = " + resultSet.getObject("password"));
            System.out.println("email = " + resultSet.getObject("email"));
            System.out.println("birthday = " + resultSet.getObject("birthday"));
            System.out.println("=========================================");
        }

        // 6. 释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```



**步骤总结：**

1.加载驱动

```java
Class.forName("com.mysql.jdbc.Driver");
```



2.连接数据库  （输入用户信息）

```java
Connection connection = DriverManager.getConnection(url, username, password); 
```



3.获取执行sql的对象  Statement

```java
Statement statement = connection.createStatement();
```



4.通过 Statement 对象 来 执行sql ，获得返回的结果集

```java
ResultSet resultSet = statement.executeQuery(sql);
```



5.释放资源

```java
resultSet.close();
statement.close();
connection.close();
```



> DriverManager

```java
// 1.加载驱动
// 没必要注册，源码在静态代码块已经有这一句了，这样写就注册两次了
// DriverManager.registerDriver(new com.mysql.jdbc.Driver());   
Class.forName("com.mysql.jdbc.Driver");

// 3. 连接成功，得到数据库对象 Connection 代表数据库
// connection 代表数据库
Connection connection = DriverManager.getConnection(url, username, password);
// 事务提交 事务回滚 数据库设置自动提交
connection.commit();
connection.rollback();
connection.setAutoCommit(true);
```



> URL

```java
// 2. 用户信息和url
String url="jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";

// mysql（默认端口号） -- 3306
// 协议://主机地址：端口号/数据库名?参数1&参数2&参数3

// oralce（默认端口号） -- 1521
// jdbc:oralce:thin:@localhost:1521:sid
```



> 4. 创建sql对象：Statement statement = connection.createStatement();
>
> Statement：执行SQL的对象 ，另一种：connection.prepareStatement()

```java
String sql="select * from users";	//	编写SQL，尽量先测试成功，在使用

statement.executeQuery(sql);    // 查询  返回ResultSet
statement.execute(sql);     // 执行任何SQL
statement.executeUpdate(sql);  // 插入、更新、删除  返回一个受影响的行数
statement.executeBatch();  // 执行多条SQL语句
```



> ResultSet 查询的结果集：封装了所有的查询结果

```java
// 如果不知道列的类型的情况下
resultSet.getObject("column_01");
// 如果知道列的类型，可以直接指定类型
resultSet.getInt("column_01");
resultSet.getString("column_01");
resultSet.getFloat("column_01");
resultSet.getDate("column_01");  
```

* **遍历，指针**

```java
// 遍历，指针
resultSet.beforeFirst();      // 移动到最前面
resultSet.afterLast();        // 移动到最后面
resultSet.next();             // 移动到下一个数据
resultSet.previous();         // 移动到前一个数据
resultSet.absolute(row); // 移动到指定行
```

* 第一个JDBC程序中的对应代码

```java
// 5. 通过sql对象执行SQL语句，查看返回的结果
String sql="select * from users";
ResultSet resultSet = statement.executeQuery(sql);

while(resultSet.next()){
    System.out.println("id:"+resultSet.getObject("id"));
    System.out.println("name:"+resultSet.getObject("name"));
    System.out.println("pwd:"+resultSet.getObject("password"));
    System.out.println("email:"+resultSet.getObject("email"));
    System.out.println("birth:"+resultSet.getObject("birthday"));
    System.out.println("=========++++++++==========++++++++++===========");
}
```



> 释放资源

```java
// 6. 释放资源
resultSet.close();
statement.close();
connection.close();
```



### 10.4.Statement对象

**JDBC中的statement对象用于向数据库发送SQL语句，想完成对数据库的增删改查，只需要通过这个对象向数据库发送增删改查语句即可。**

Statement对象的excuteUpdate方法，用于向数据库发送增、删、改的SQL语句，excuteUpdate执行完后，将会返回一个整数（即增删改语句导致了数据库几行记录发生了变化）。

Statement.executeQuery方法用于向数据库发送查询语句，excuteQuery方法返回代表查询结果的ResultSet对象。

编写增删改的方法，调用 ==executeUpdate（）==



> CRUD操作-create

使用excuteUpdate(String sql)方法完成数据库的添加操作，示例如下：

```java
Statement st = conn.createStatement();
String sql="INSERT INTO users(`id`,`name`,`password`,`email`,`birthday`)\n" +
        "VALUES(5,'laoli','123456','lao5454@q2q.com','1977-06-13')";

int num = st.executeUpdate(sql);
if(num > 0){
    System.out.println("[DEBUG]insert success");
}
```



> CRUD操作-Retrieve

使用executeQuery(String sql)方法完成数据库的查询操作，示例操作：

```java
Statement st = conn.createStatement();
String sql="select * from users where id=4";

ResultSet resultSet = st.executeQuery(sql);
while(resultSet.next()){
    // 根据数据库的列的数据类型，分别调用resultSet相应的方法获取对应的数据（不知道什么类型，则用resultSet.getObject(字段名)方法）
    System.out.println("id:"+resultSet.getObject("id"));
    System.out.println("name:"+resultSet.getObject("name"));
    System.out.println("pwd:"+resultSet.getObject("password"));
    System.out.println("email:"+resultSet.getObject("email"));
    System.out.println("birth:"+resultSet.getObject("birthday"));
    System.out.println("=========++++++++==========++++++++++===========");
}
```



> CRUD操作-update

使用executeUpdate(String sql)方法完成数据库的修改操作，示例操作：

```java
Statement st = conn.createStatement();
String sql="update users set `name`='gokudu',`password`='112211' where id=5";

int num = st.executeUpdate(sql);
if(num > 0){
    System.err.println("[DEBUG]update success");
}
```



> CRUD操作-delete

使用executeUpdate(String sql)方法完成数据库的删除操作，示例操作：

```java
Statement st = conn.createStatement();
String sql="DELETE FROM users WHERE id=5";

int num = st.executeUpdate(sql);
if(num > 0){
    System.err.println("[DEBUG]delete success");
}
```



#### 1.提取工具类

```java
public class JdbcUtils {

    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;

    static {

        try {
            // 读取配置文件 db.properties
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            // 1. 加载驱动，驱动只用记载一次
            Class.forName(driver);

        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }

    }

    // 2. 获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, username, password);
    }

    // 3.释放连接资源
    public static void release(Connection conn, Statement st, ResultSet rs){
        if(rs != null){
            try {
                rs.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if(st != null){
            try {
                st.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }

        if(conn != null){
            try {
                conn.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```



#### 2.编写增删改的方法

调用 ==st.executeUpdate(sql)方法==

**添加数据：**

```java
package com.yan.lesson02;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestInsert {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接
            st = conn.createStatement();  // 获取SQL的执行对象
            String sql = "insert into `users` (`id`,`name`,`password`,`email`,`birthday`)" +
                    "values (4, 'xiaoming', '123456', 'xiaoming@sina.com', '1979-02-15')";

            int num = st.executeUpdate(sql); // num是数据库受影响的行数
            if(num > 0){
                System.out.println("插入成功!");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}

```

update 和  delete 只需要 在上面这个方法的基础上，更改一下sql语句



**删除数据**

```java
package com.yan.lesson02;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestDelete {
    public static void main(String[] args) {
        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接
            st = conn.createStatement();  // 获取SQL的执行对象
            String sql = "delete from `users` where id = 4";

            int num = st.executeUpdate(sql); // num是数据库受影响的行数
            if(num > 0){
                System.out.println("删除成功!");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}

```

**修改数据**

```java
package com.yan.lesson02;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestUpdate {
    public static void main(String[] args) {
        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接
            st = conn.createStatement();  // 获取SQL的执行对象
            String sql = "update `users` set `name`='xiaoming',`email`='xiaoming@sina.com' where id = 1";

            int num = st.executeUpdate(sql); // num是数据库受影响的行数
            if(num > 0){
                System.out.println("修改成功!");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```

#### 3.编写查询方法

调用 ==st.executeQuery(sql)方法==

```java
package com.yan.lesson02;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class TestSelect {
    public static void main(String[] args) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接
            st = conn.createStatement();  // 创建执行SQL语句的对象

            // SQL语句
            String sql = "select * from `users` where id = 1";

            rs = st.executeQuery(sql); // 执行SQL语句，获取返回的结果集

            while (rs.next()){
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String password = rs.getString("password");
                String email = rs.getString("email");
                Date date = rs.getDate("birthday");

                System.out.println("id: " + id);
                System.out.println("name: " + name);
                System.out.println("password: " + password);
                System.out.println("email: " + email);
                System.out.println("date: " + date);
            }

        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            JdbcUtils.release(conn, st, rs);
        }
    }
}

```



### 10.5. SQL注入问题

SQL存在漏洞，会被攻击导致数据泄露（根本原因是SQL字符串会被拼接（or），使得SQL语句能够成功执行，即使在用户名，密码输错的情况下）。使用 PreparedStatement 可以防止注入，并且效率更高。

**示例代码：**

```java
package com.yan.lesson02;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class SQL注入 {
    public static void main(String[] args) {
//        // 正常登录
//        String username = "xiaoming";
//        String password = "123456";
//        SQL注入.login(username, password);

        // SQL注入
        // SQL注入后执行的SQL语句：select * from `users` where `name`='' or '1=1' and `password`='' or '1=1';
        String username = "' or '1=1";
        String password = "' or '1=1";
        SQL注入.login(username, password);
    }

    // 登录业务
    public static void login(String username, String password) {

        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接
            st = conn.createStatement();  // 创建执行SQL语句的对象

            // 要执行的SQL语句
            // select * from `users` where `name`='xiaoming' and `password`='123456';
            String sql = "select * from `users` where `name`='" + username + "' and " + "`password`='" + password + "'";

            // 执行查询语句，得到返回的结果集
            rs = st.executeQuery(sql);

            while (rs.next()){
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String pwd = rs.getString("password");
                String email = rs.getString("email");
                Date date = rs.getDate("birthday");

                System.out.println("id: " + id);
                System.out.println("name: " + name);
                System.out.println("password: " + pwd);
                System.out.println("email: " + email);
                System.out.println("date: " + date);
                System.out.println("==============================");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}

```

**通过SQL注入，查询出所有用户的信息：**

![通过SQL注入查询出所有用户信息](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%80%9A%E8%BF%87SQL%E6%B3%A8%E5%85%A5%E6%9F%A5%E8%AF%A2%E5%87%BA%E6%89%80%E6%9C%89%E7%94%A8%E6%88%B7%E4%BF%A1%E6%81%AF.png)



```java
package com.guo.jdbc02;

import com.guo.jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestSQLInjection {

    public static void main(String[] args)  {
//        login("yyyy","112211");
        login(" 'or '1=1"," 'or '1=1");
    }

    public static void login(String username,String password){
        Connection conn=null;
        Statement st=null;
        ResultSet res=null;
        try {
            conn = JdbcUtils.getConnection();
            st = conn.createStatement();
            String sql="select * from users where `name`='" + username + "'and `password`='" + password + "'";

            ResultSet resultSet = st.executeQuery(sql);
            while(resultSet.next()){
                System.out.println(resultSet.getObject("name"));
                System.out.println(resultSet.getObject("password"));
                System.out.println("===========================");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, st, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 10.6.PreparedStatement 

使用 PreparedStatement 可以防止SQL注入，并且效率更高！

使用预编译插入数据，update和delete同理，修改一下sql，给相应的占位符设置参数类型、赋值即可。

* 新增

```java
package com.yan.lesson03;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class TestInsert {
    public static void main(String[] args) {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {

            conn = JdbcUtils.getConnection();  // 获取数据库连接

            // 区别，插入的值用占位符?代替
            String sql = "insert into `users` (`id`,`name`,`password`,`email`,`birthday`) values (?,?,?,?,?)";
            st = conn.prepareStatement(sql);  // 预编译SQL，先不执行，获取执行SQL的对象
            // 手动给参数赋值，第一个参数是待插入参数的位置(第几个?)，第二个参数是插入的值
            st.setInt(1, 4); // id
            st.setString(2, "xiaoqiang");
            st.setString(3, "123456");
            st.setString(4, "xiaoqiang@sina.com");
            // 注意点：sql.Date  数据库
            //       util.Date Java
//            st.setDate(5, new Date(1980, 5, 12));
            st.setDate(5, new Date(new java.util.Date().getTime()));

            // 执行SQL语句
            int num = st.executeUpdate();
            if(num > 0){
                System.out.println("插入成功！");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放连接资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}

```



* 删除

```java
package com.yan.lesson03;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class TestDelete {
    public static void main(String[] args) {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {

            conn = JdbcUtils.getConnection();  // 获取数据库连接

            // 区别，传入的值用占位符?代替
            String sql = "delete from `users` where id=?";
            st = conn.prepareStatement(sql);  // 预编译SQL，先不执行，获取执行SQL的对象
            // 手动给参数赋值，第一个参数是待传入参数的位置(第几个?)，第二个参数是传入的值
            st.setInt(1, 4);

            // 执行SQL语句
            int num = st.executeUpdate();
            if(num > 0){
                System.out.println("删除成功！");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放连接资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```



* 修改

```java
package com.yan.lesson03;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestUpdate {
    public static void main(String[] args) {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {

            conn = JdbcUtils.getConnection();  // 获取数据库连接

            // 区别，传入的值用占位符?代替
            String sql = "update `users` set `name`=? where id=?";
            st = conn.prepareStatement(sql);  // 预编译SQL，先不执行，获取执行SQL的对象
            // 手动给参数赋值，第一个参数是待传入参数的位置(第几个?)，第二个参数是传入的值
            st.setString(1, "小强");
            st.setInt(2, 1);

            // 执行SQL语句
            int num = st.executeUpdate();
            if(num > 0){
                System.out.println("修改成功！");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放连接资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```



* 查询

```java
package com.yan.lesson03;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class TestSelect {
    public static void main(String[] args) {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {
            // 获取数据库连接
            conn = JdbcUtils.getConnection();

            // 预编译的sql语句
            String sql = "select * from `users` where id = ?";
            // 获取执行SQL语句的对象
            st = conn.prepareStatement(sql);
            //传递参数
            st.setInt(1, 1);

            // 执行SQL语句
            rs = st.executeQuery();

            while (rs.next()){
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String password = rs.getString("password");
                String email = rs.getString("email");
                Date date = rs.getDate("birthday");

                System.out.println("id: " + id);
                System.out.println("name: " + name);
                System.out.println("password: " + password);
                System.out.println("email: " + email);
                System.out.println("date: " + date);

                System.out.println("============================");
            }

        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```



* 防止SQL注入

**PrepareStatement防止SQL注入**：是把整个参数用引号包起来作为查询字段的值，并把参数中的引号作为转义字符，从而避免了参数也作为条件的一部分。

```java
package com.yan.lesson03;

import com.yan.lesson02.utils.JdbcUtils;

import java.sql.*;

public class SQL注入 {
    public static void main(String[] args) {
//        // 正常登录
//        String username = "小强";
//        String password = "123456";
//        SQL注入.login(username, password);

        // SQL注入
        // SQL注入后执行的SQL语句：select * from `users` where `name`='' or '1=1' and `password`='' or '1=1';
//        String username = "' or '1=1";
//        String password = "' or '1=1";
        String username = "'' or 1=1";
        String password = "'' or 1=1";
        SQL注入.login(username, password); // 返回结果为空
        // PrepareStatement防止SQL注入：是把整个参数用引号包起来作为查询字段的值，并把参数中的引号作为转义字符，从而避免了参数也作为条件的一部分
        // 相当于执行：select * from `users` where `name`='\'\' or 1=1'，当然查询不到数据
    }

    // 登录业务
    public static void login(String username, String password) {

        Connection conn = null;
        PreparedStatement st = null;
        ResultSet rs = null;

        try {
            conn = JdbcUtils.getConnection();  // 获取数据库连接

            // 要执行的SQL语句
            // select * from `users` where `name`='小强' and `password`='123456';
            String sql = "select * from `users` where `name`=? and `password`=?";

            // 预编译SQL语句，获取执行SQL语句的对象
            // PrepareStatement：可防止SQL注入
            st = conn.prepareStatement(sql);

            // 传入参数
            st.setString(1, username);
            st.setString(2, password);

            // 执行查询语句，得到返回的结果集
            rs = st.executeQuery();

            while (rs.next()){
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String pwd = rs.getString("password");
                String email = rs.getString("email");
                Date date = rs.getDate("birthday");

                System.out.println("id: " + id);
                System.out.println("name: " + name);
                System.out.println("password: " + pwd);
                System.out.println("email: " + email);
                System.out.println("date: " + date);
                System.out.println("==============================");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            // 释放资源
            JdbcUtils.release(conn, st, rs);
        }
    }
}
```

### 10.7 使用IDEA连接数据库

**选择要连接的数据库类型：**

![idea连接数据库](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/idea%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93.png)

**填写连接信息：**

![填写连接信息](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A1%AB%E5%86%99%E8%BF%9E%E6%8E%A5%E4%BF%A1%E6%81%AF.png)

**连接成功后，可以选择数据库**：

![选择数据库](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%80%89%E6%8B%A9%E6%95%B0%E6%8D%AE%E5%BA%93.png)

**双击数据库中的表可查看表的信息：**

![双击数据库中的表可查看表的信息](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%8F%8C%E5%87%BB%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E8%A1%A8%E5%8F%AF%E6%9F%A5%E7%9C%8B%E8%A1%A8%E7%9A%84%E4%BF%A1%E6%81%AF.png)

**修改数据后点击DB(commit)，更新数据：**

![更新数据](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%9B%B4%E6%96%B0%E6%95%B0%E6%8D%AE.png)

**打开SQL编写界面：**

![打开SQL编写界面](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%89%93%E5%BC%80SQL%E7%BC%96%E5%86%99%E7%95%8C%E9%9D%A2.png)



### 10.8. 事务

==要么都成功，要么都失败==

**ACID原则：**

原子性：要么全部完成，要么都不完成

一致性：总数不变

隔离性：多个进程互不干扰

持久性：一旦提交不可逆，持久化到数据库了



**隔离性的问题：**

脏读：一个事务读取了另一个事务没有提交的数据

不可重复读：在同一个事务内，重复读取表中的数据，表数据发生了改变

虚读（幻读）：在一个事务内，读取到了别人插入的数据，导致前后读出来的结果不一致



**代码实现：**

1.开启事务  conn.setAutoCommit(false);

2.一组事务执行完毕，提交事务

3.可以在catch语句中显性地定义 回滚 语句，（默认失败也会回滚事务）

```java
package com.guo.Jdbc04;

import com.guo.Jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestTransation {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement pst = null;
        ResultSet res = null;

        try {
            conn = JdbcUtils.getConnection();
            //关闭数据库自动提交,会自动开启事务，它相比直接在数据库写，少了一步操作（START TRANSACTION）
            conn.setAutoCommit(false);      // 即开启事务

            String sql1="update JDBC.account set money = money - 1000 where name = 'AAA'";
            pst  = conn.prepareStatement(sql1);
            pst.executeUpdate();

            String sql2="update JDBC.account set money = money + 1000 where name = 'BBB'";
            pst  = conn.prepareStatement(sql2);
            pst.executeUpdate();

            //业务完成，提交事务
            conn.commit();
            System.out.println("[DEBUG] UPDATE SUCCESS!");

        } catch (SQLException e) {
            // 如果这里不写  它也会回滚
            // 程序以及帮我们做好了
            // 这里只是显示的定义回滚语句，但是默认失败它就会回滚
            try {
                conn.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, pst, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

    }
}
```



### 10.9.数据库连接池

数据库连接--执行完毕--释放

频繁连接--释放十分浪费资源

**池化技术：准备一些预先的资源，过来就能连接到预先准备好的**



最小连接数：10

最大连接数：15

等待超时：100ms



编写连接池，实现一个接口 DataSource

> 开源数据源实现（拿来即用）

DBCP

C3P0

Druid：阿里巴巴

使用了这些数据库连接池之后，在项目中就不需要编写连接数据库的代码了（DriverManager.getConnection(url, username, password)）



示例：**使用DBCP**

需要的jar包（MySQL版本为：8.0.21）

commons-dbcp2-2.7.0.jar

commons-pool2-2.8.1.jar

commons-logging-1.2.jar

**添加到lib文件夹中：**

![添加到lib文件夹中](/images/Mysql%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%B7%BB%E5%8A%A0%E5%88%B0lib%E6%96%87%E4%BB%B6%E5%A4%B9%E4%B8%AD.png)



示例：**使用C3P0**

需要的jar包（MySQL版本为：8.0.21）

c3p0-0.9.5.5.jar、mchange-commons-java-0.2.19



**结论：**

无论使用什么数据源，本质还是一样的，DataSource接口不会变，方法就不会变。