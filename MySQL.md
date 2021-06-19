### 学习笔记，仅供学习使用  
# MySQL知识点总结
## 一、数据库概述  
&emsp;数据库(DATE BASE)是指长期保存在在计算机的存储设备上，按照一定规则组织起来，可以被各种应用或者用户共享的数据集合。  
&emsp;数据库管理系统(DateBase Mangermant System,DBMS):是指一种操作和管理大型数据库的大型软件，用于建立，使用，维护数据库，对数据库进行统一的管理和控制，保证数据库的完整性和安全性。用户通过数据库管理系统访问数据库的数据。  
&emsp;数据库中存储的数据以二维行列式进行存储。行(row)在sql中称之为一条记录，也叫实体；列(column)在sql称之为字段，在以后的java访问书记库编程中，在java中定义变量来与字段对应。  
## SQL (Structure Query Language) (结构化查询语言)  
### SQL的分类  
|类型            |作用|
|:----------------:|:---:|
|DDL 数据定义语言 |用来定义数据库的对象：库，表，列等。包括创建表，修改表，删除表等。|  
|DML 数据库操作语言| 用来定义数据库记录：是对表中的操作，包括插入，更新，删除数据。|   
|DCL 数据库控制语言|用来定义访问权限和安全级别|  
|DQL 数据查询语言|用来查询数据：只有SELECT语句|  

***SQL语句以英文分号结尾。***  
### DDL(包括CREATE,ALTER,DROP关键词)  
#### DDL操作数据库   
* 创建数据库  
    * **CREATE DATEBASE** 数据库名;--创建数据库  
    * **CREATE DATEBASE** 数据库名 SET GBK; -- 创建数据库并设置为gbk编码格式
    * **CREATE DATEBASE** 数据库名 SET GBK COLLATE gbk_chinede_ci; -- 创建一个使用gbk字符集，并带校对规则的数据库
* 查询数据库  
  *  **SHOW DATEBASE**;-- 查看当前数据库服务器中的所有数据库  
  *  **SHOW CREATE DATEBASE** 数据库名; --查看创建数据库时的定义信息  
* 删除数据库  
  * DROP DATEBASE 数据库名；--删除指定数据库     
* 修改数据库  
  *    ALTER DATABASE 数据库名 CHARACTER SET utf8; -- 将数据库的字符集改为utf8  
  ----
  在创建表格之前要选择表格创建的数据库，所以需要选择数据库：  
  USE 数据库名；--使用数据库   
  SELECT DATABASE(); -- 选中数据库   
#### 常用数据类型  
```

int：整型

double：浮点型，例如double(5,2)表示最多5位，其中必须有2位小数，即最大值为999.99；

char：固定长度字符串类型； char(10)  'abc       '

varchar：可变长度字符串类型；varchar(10) 'abc'

text：字符串类型;

blob：字节类型；

date：日期类型，格式为：yyyy-MM-dd；

time：时间类型，格式为：hh:mm:ss

 timestamp：时间戳类型 yyyy-MM-dd hh:mm:ss  会自动赋值

datetime:日期时间类型 yyyy-MM-dd 
```
### DML(包括INSERT,DELETE,UPDATE关键字)  
  DML是对表中的数据进行增，删，改的操作。  
#### INSERT增加操作   
  插入一条记录的语法：  
  **INSERT INTO 表名(字段1,字段2,...) VALUES(值1,值2,...);**  
  插入操作时要注意：字段要和值一一对应，值数可以少于字段数，但是不可以多。插入空值使用null.插入字符串和日期时需要使用单引号括起来。  
  下面是一个INSER插入的Demo： 

  ```sql  
    --创建emp表格
    CREATE TABLE emp(
    id INT,
    NAME VARCHAR(100),
    gender VARCHAR(10),
    birthday DATE,
    salary FLOAT(10,2),
    entry_date DATE,
    RESUME TEXT
    );
    
    -- 插入3条记录
    INSERT INTO emp(id,NAME,gender,birthday,salary,entry_date,RESUME)
    VALUES(1,'zhangsan','female','1990-5-10',10000,'2015-5-5-','good girl');
    
    INSERT INTO emp(id,NAME,gender,birthday,salary,entry_date,RESUME)
    VALUES(2,'lisi','male','1995-5-10',10000,'2015-5-5','good boy');
    
    INSERT INTO emp(id,NAME,gender,birthday,salary,entry_date,RESUME)
    VALUES(3,'wangwu','male','1995-5-10',10000,'2015-5-5','good boy');
    
    SELECT *FROM emp;
```  
批量插入：
```sql
INSERT INTO emp VALUES
(4,'zs','m','2015-09-01',10000,'2015-09-01',NULL),
(5,'li','m','2015-09-01',10000,'2015-09-01',NULL),
(6,'ww','m','2015-09-01',10000,'2015-09-01',NULL);
```
#### UPDATE更新操作：
***语法： UPDATE 表名 SET 列名1=列值1,列名2=列值2,... WHERE 列名=列值;***  
基本操作：  
|操作|SQL语句|
|:--:|:--:|
|将所有人的薪水都改为100元|UPDATEE emp SET salary = 100 ;| 
|将姓名为“lisi”的人工资修改为300元|UPDATE emp SET salary=300 WHERE name = 'lisi';|  
|将wu的薪水在原有基础上增加1000元(**MySql没有+=运算符**)|UPDATE emp SET salary=salary+1000 WHERE name='ww';|  

#### DELETE删除操作  
语法：**DELETE FROM表名(WHERE 列名=值 )**
|操作|语法|  
|:--:|:--:|  
|删除表中的名称为"zs"的记录|DELETE FROM emp WHERE name='zs';|  
|删除所有记录|DELETE FROM emp；|

### DQL(SELECT关键字)  
数据库执行DQL语句不会对数据进行改变，而是让数据库发送结果给客户端。查询的的结果是一张虚拟表。    
基本语法：**SELECT 列名 FROM 表名；**  
语法：  
  SELECT selection_list /*要查询的列名称*/

  FROM table_list /*要查询的表名称*/

  WHERE condition /*行条件*/

  GROUP BY grouping_columns /*对结果分组*/

  HAVING condition /*分组后的行条件*/

  ORDER BY sorting_columns /*对结果分组*/

  LIMIT offset_start, row_count /*结果限定*/ 
#### 1.基础查询 
|方法|操作| 
|:---:|:---:|  
|查询所有列|SELECT* FORM 表名|  
|查询指定列|SELECT 列名，列名... FROM emp| ; 
#### 2.条件查询  
条件查询就是在查询时给出WHERE子句，在WHERER子句中可以使用如下运算符及其关键字：  
* =、!=、<>、<、<=、>、>=；
* BETWEEN…AND；BETWEEN...AND 还可以查日期的范围
* IN(set)；IN中的参数为查询范围，各值之间用逗号隔开
* IS NULL； IS NOT NULL IS NULL 是进行非空判断，与之相对的是IS NOT NULL  
* AND；
* OR；
* NOT；  
#### 3.模糊查询(LIKE)  
&emsp;模糊查询使用统配字符补充模糊的地方。通配符有两个：“_”表示任意一个字符，‘%’表示任意多个字符。使用模糊查询需要时使用关键字**LIKE**.  
&emsp;举例：(比如我们要找的是s开头后面两位任意，第四位为0后面任意长度的学生姓名~）  
SELECT * FROM emp WHERE sname LIKE 's__0%';  
#### 4.字段控制查询(DISTINCT) 
比如我们要查询emp表中的salary，想要了解每个员工的工资水平，控制字段不要查询重复的薪水。  
**SELECT DISTINCT salary FROM emp;**  
 比如我们要查询每个员工的薪水与奖金之和，因为有的人补贴为null，所以简单加法运算得到的总和为null  
 **SELECT *,salary+IFNULL(comm,0) AS total FROM emp;**  
 #### 5.排序(ORDER BY)
 ASC:升序(默认值)；  
 DESC:降序   
 **SELECT * FROM emp ORDER BY sal DESC,empno;-- sal按照升序排序，sal相同empno按照降序排序**  
 #### 6.分组查询  
 例如我们要根据每一个部门来查询每个部门的工资和，就需要按部门进行分组。  
 **子句：GROUP BY**   
 **SELECT deptno,COUNT(*) num,SUM(sal) SUM FROM emp GROUP BY deptno ;**  

 **子句:HAVING    作用是分组后对数据进行过滤，区别于WHERE**  
 #### 7.聚合函数  
 聚合函数使用来做纵向运算的函数。  
 * 1.COUNT();统计指定列不为NULL的行为记录；
 * 2.MAX();计算指定列的最大值，如果指定列时字符串类型，那么使用字符串排序运算。(字典比较)
 * 3.MIN()：计算指定列的最小值，如果指定列是字符串类型，那么使用字符串排序运算；（字典比较）  
 * 4.SUM():计算指定列的数值和，如果指定列的数据类型不是数值类型，那么计算结果为0；
 * 5.AVG():计算指定列的平均值，如果指定列的数据类型不是数值类型，那么计算结果为0；  
### MySQL——LIMIT  
LIMIT 用来限定查询结果的起始以及总行数。  
与编程语言相同，这里的起始行索引也是从0开始。  
从0行开始查询到第5行记录：
```sql 
SELECT * FROM emp LIMIT 0,5;
```  
### 查询代码的书写顺序和执行顺序   
查询语句的书写顺序：***SELECT-FROM-WHERE-GROUP BY-HAVING-ORDER BY-LIMIT***    
查询语句的执行顺序：***FROM-WHERE-GROUP BY-HAVING-SELECT-ORDER BY-LIMIT***  
## 二、数据的完整性  
实体(entity):代表表中的一行(一条记录)    
主键不能为空或者部分不能为空的约束条件称之为实体的完整性。实体的完整性要求每一个表中的主键字段都不能为空或者时重复的值。实体完整性指的表中行的完整性。要求每一行都有唯一独特的标识符，称之为主关键字。主关键字是否可以修改，取决于主关键字与其他表之前要求的完整性。  
作用：表示每一行的数据不重复。  
分类：主键约束(PRIMARY KEY)唯一约束(UNIQUE)自动增长列(AUTO-INCREMENT)  
一个列表可以有多个约束条件，约束之间用逗号隔开。  
### 主键约束：数据唯一(数据不能重复)，且不是为null  
***一个表中只能有一个主键约束条件，联合主键约束算是一个主键约束条件。***  
主键约束格式：***[CONSTRAINT<约束名>]PRIMARY KEY [字段名]***
```sql
方法一：  
CREATE TABLE student(
sid INT PRIMARY KEY,
sname VARCHAR(20)
);
```   
方式二：这种方式的优点在于可以创建联合主键——同时参照所有主键 都完全相同视为数据不唯一，所有键联合为一个主键。
```sql
-- 创建单主键表
CREATE TABLE student1(
sid INT,
sname VARCHAR(20),
PRIMARY KEY(sid)
);
-- 创建联合主键表
CREATE TABLE student1_1(
sid INT,
sname VARCHAR(20),
score DOUBLE,
PRIMARY KEY(sid,score)
);
```
方式三：这种方式的优点是可以给已经存在的表添加主键且可以添加联合主键
```sql
-- 在表格外部给表添加单主键
CREATE TABLE student2(
sid INT,
classid INT,
sname VARCHAR(20)
);
ALTER TABLE student2 ADD CONSTRAINT PRIMARY KEY(sid);
-- 在表格外部给表格添加联合主键
CREATE TABLE student2_1(
sid INT,
classid INT,
sname VARCHAR(20)
);
ALTER TABLE student2_1 ADD PRIMARY KEY(sid,classid);
--CONSTRAINT 关键字可以省略
```  
###  UNIQUE唯一约束：数据唯一，可以有null  
唯一约束在MySQL中可以有多个null（可以看成bug了），在ORACLE中只能有一个null，否则报错。  
创建UNQUE约束的方法：  
```sql
CREATE TABLE student3(
sid INT ,
sname VARCHAR(20) UNIQUE
);
```
```sql
CREATE TABLE student3_2(
sid INT ,
sname VARCHAR(20),
sage VARCHAR(5)
);
ALTER TABLE student3_2 ADD UNIQUE (sname);
``` 

删除唯一约束的方法：
```sql
ALTER TABLE student3_2 DROP INDEX sname;
 -- 这样可以删除sname作为的唯一约束
```  
### AUTO-INCREMENT自动增长列
自动增长列的约束是给键来自动增加的(***只针对整数***)，如果没有键定义使用AUTO-INCREMENT，会报出以下错误：
```sql
Incorrect table definition; there can be only one 
auto column and it must be defined as a key
```  
```sql 
CREATE TABLE student4(
sid INT UNIQUE AUTO_INCREMENT,
sname VARCHAR(20)
);
CREATE TABLE student4_1(
sid INT PRIMARY KEY AUTO_INCREMENT,
sname VARCHAR(20)
);
```  
我们只需要输入sname，sid会自动增长  
***注：我们在插入数据的时候sid因为被自动增长约束而整数加加，那么，如果在插入记录的时候，如果删除了插入的记录，那么编号会继续随着删除的记录的号加加，不会因为删除记录而退回重新加加。***  

### 域完整性 
&emsp;域完整性是针对具体关系型数据库的约束条件，他保证某些列不能输入无效的值。域完整性指的是列的值域的完整性，如数据类型，格式，值域，是否支持Null   
&emsp;域完整性约束：数据类型，非空约束(NOT NULL),默认约束（DEFAULT） 检查约束（CHECK这个MySQL不支持）。   
### 数据类型  
数值类型，日期类型，字符串类型  
### 非空约束(NOT NULL)
约束添加在一个字段的定义中，约束这个字段不能有null
### 默认值约束(DEFAULT)
约束添加在一个字段的定义中，约定这个字段的默认填充为DEFAULT后面的内容。后面插入数据时，如果插入值与默认值相同，则此处填DEFAULT，否则填入期望值。  
```sql 
ALTER TABLE USER ADD COLUMN gender CHAR(3) DEFAULT '男';
```   
插入值与默认值相同：
```sql
INSERT INTO USER 
VALUES(5,'高七','012','gaoqi@outlook.com','2000-5-1',DEFAULT);
```  
### 引用完整性——外键约束  
***外键约束关键词：FOREIGN KEY ***
&emsp;外键约束用来在两个表之间建立连接，它可以是一列或者多列。一个表可以有一个或者多个外键。外键对应的是参照完整性，一个表的外键为空值，若不为空值，那么每一个外键的值必须等于另一个表中主键的值。