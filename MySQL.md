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
## 数据的完整性  
实体(entity):代表表中的一行(一条记录)  