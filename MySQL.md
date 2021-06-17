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
语法：**DELETE FROM表名(where )