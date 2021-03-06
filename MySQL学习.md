---
title: MySQL学习
tags:  数据库
grammar_cjkRuby: true
---
[toc!]

# SQL 
&emsp;SQL是访问和处理关系数据库的标准语言，是结构化查询语言(structured query language)的缩写，用来访问和操作数据库系统，SQL语句可以查询、添加、更新和删除数据库中的数据，同时可对数据库进行管理和维护。
不同的数据库都在标准SQL之外，进行了扩展。
SQL语言定义了几种对于DB的operation：
 - DDL:data definition language 允许用户定义数据，创建表、删除表、修改表结构等操作，通常由DB administrator operate
 - DML:data manipulation language为user 提供添加、删除、更新数据的能力，是application对DB的日常操作
 - DQL:data query language 允许 用户查询数据，最普通频繁的operation

SQL语言的关键字不区分大小写。但是针对不同的DB，对于表名和列名，有的区分大小写，有的不区分大小写

# 运行MySQL
## 安装MySQL
1. 在[MySQL官网](https://dev.mysql.com/downloads/mysql/)上下载与系统版本相对应的MySQL community server版本。
2. 将下载后的压缩文件解压到安装目录下
3. 将bin目录的地址添加到系统path路径中
4. 在系统的安装目录下新建文件**my.ini**,然后写入配置信息
```
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=E:\\software\\mysql\\mysql-8.0.11-winx64 
# 设置mysql数据库的数据的存放目录
datadir=E:\\software\\mysql\\mysql-8.0.11-winx64\\Data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数,防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```
5. 以管理员权限运行cmd,进入命令行模式，首先进入bin目录下进行数据库初始化
```
mysqld --initialize --console
```
执行后，记录终端上显示的mysql的临时密码，以方便修改。
6. 在bin目录下安装MySQL服务
```
mysql --install [服务名称]
```
服务名称可以不写，默认是mysql。
7. 启动MySQL，系统命令行模式下
```
net start mysql
```
8. 关闭MySQL， 系统命令模式下
```
net stop mysql
```
9. 更改登录密码  mysql 命令下
```
mysql -u root -p
```
之后输入初始化密码，即可登录成功，进入MySQL命令模式
在MySQL命令模式下输入
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_	password BY '新密码'；（；是必须的）
```
至此MySQL安装更改密码完成
## 使用MySQL
查看默认安装的数据库
```
show databases;  查询所有数据库
use mysql;   切换数据库
show tables;   显示数据库所有表格
```
查看默认的MySQL用户
```
select user,host,authentication_string from mysql.user;
```
管理员root的host是localhost，表示仅限localhost登陆访问，若是允许其他ip登录，需添加新的host，若允许所有ip访问，直接使用"%"即可。
创建用户：
```
CREATE USER 'xxh'@'%' IDENTIFIED WITH mysql_native_passowrd BY '登录密码'
```
检查用户：
```
select user,host,plugin,authentication_string from user\G;
```
授予所有权限
```
GRANT ALL PRIVILEGES ON *.* TO 'xxh'@'%';
```
授予基本的查询修改权限
```
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON *.* TO 'xxh'@'%';
```
查看用户的权限：
```
show grants for 'xxh'@'%';
```
删除数据库：
```
drop database 数据库名称；
```
创建数据库：
```
create database 数据库名称；
```
## 关系数据库
&emsp;应用程序需要读写用户的数据，随着应用程序的功能复杂化和数据量的增大，数据管理便极为重要。数据库便是专门管理数据的软件，应用程序不需要管理数据，而是通过数据库软件提供的接口读写数据。
&emsp;数据库按照数据结构组织、存储和管理数据，一共有三种模型：
- 层次模型
  - 是一种以上下级的层次关系来组织数据的一种方式，数据结构看起来如一棵树。如：
  <!--![层次模型结构](E:/SQL/层次模型结构.jpg)-->
  <div align="center"><img src="E:/SQL/层次模型结构.jpg" width="200px" height="200px">
</div>
- 网状模型
 - 是把每一个节点和其他许多节点连接起来，如城市之间的路网。如：
<!-- ![网状模型结构](E:/SQL/网状模型结构.jpg)-->
 <div align="center"><img src="E:/SQL/网状模型结构.jpg" width="200px" height="200px">
</div>
- 关系模型
 - 是把数据看做一个二维表格，任何数据都可以通过行号+列号唯一确定。如同一个Excel表。如：
 <!-- ![关系模型结构](E:/SQL/关系模型结构.jpg)-->
<div align="center"><img src="E:/SQL/关系模型结构.jpg" width="200px" height="200px">
</div>

## 关系数据库
关系数据库是基于数学理论建立的，我们把域domain定义为一组具有相同数据类型的值的集合，给定一组域D1,D2,D3...Dn,它们的笛卡尔积定义为D1xD2xD3x......xDn={(d1,d2,d3......dn)|di$\in$Di}  
## 数据类型
对于关系表，除了定义列的名称，还需要定义每一列数据类型，关系数据库支持的标准数据类型包括数值、字符串、时间等：INT、BIGINT、REAL 、DOUBLE、DECIMAL(M,N)、 CHAR(N)、VARCHAR(N)、BOOLEAN、DATE、TIME、DATATIME
通常BIGINT(长整型)和VARCHAR(N)(变长字符串)就可以满足整数存储和字符串存储的需求。
## 主流关系数据库
商用：Oracle、SQL server、DB2
开源：MySQL、PostgreSQL
桌面：Microsoft Access，适合桌面程序
嵌入式：Sqlite,用于手机应用和桌面程序
## 关系模型
关系数据库是建立在关系模型上的。关系模型本质上是若干个存储数据的二维表。
表的每一行称为记录record；
表的每一列称为字段column；
字段定义了数据类型。NULL 表示字段数据不存在，并不表示为0或空字符串。字段应避免为NULL
关系数据库中关系是通过主键和外键维护的。
### 主键
关系数据库中，每一行数据称为一个记录，每一条记录是由多个字段组成。同一个表的所有记录都有相同的字段定义。
对于关系表而言，任意两条记录不能重复，不是指两条记录不完全 相同，而是指能够通过某个字段唯一区分不同的记录，这个字段被称为**主键**。
主键是能够唯一定位记录的，尽量不要修改。
主键最好是完全业务无关的字段，长把它命名为id。
可以作为id字段的类型有：
 - 自增整数类型：数据库会在插入数据时自动为每一条记录分配一个自增整数。主键常定义为`BIGINT NOT NULL AUTO_INCREMENT`类型
 - 全局唯一GUID类型：使用一种全局唯一的字符串作为主键。它是通过网卡MAC地址、时间戳和随机数保证计算机在任意时间生成的字符串都是不同的。
数据库允许两个或多个字段设置为主键，称之为联合主键。对于联合主键允许，允许有一列重复讲，只要不是所有的主键列重复即可。尽量不使用联合主键
### 外键
在某个表中，通过某个字段将数据和另外一张表关联起来，称为**外键**。
#### 添加外键约束
外键并不是通过列名实现的，而是通过定义外键约束实现的。
```
ALTER TABLE student
ADD CONSTRAINT fk_class_id 
FOREIGN KEY (class_id)
REFERENCES classes(id);
```
外键约束名称`fk_class_id` 可以是任意的
`FOREIGN KEY (class_id)`指定了class_id作为外键
`REFERENCES  classes(id)`指定外键关联到classes表的id列，即classes表的主键
#### 删除外键约束
```
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
```
删除外键约束，并没有删除外键这一列
通过中间表定义多对多关系；
外键即可以通过数据库约束，也可以通过应用程序的逻辑来保证。
## 索引
索引是关系数据库对某一列或多个列进行预排序的数据结构，通过使用索引，可以使得数据库直接定位符合条件的记录，而不必扫描整个表
一张表 可创建多个索引，加快查询效率，但在更新插入删除时，需要同时修改索引，会降低效率。
索引的效率取决于索引列的值是否分散，越分散，效率越高；若存在大量相同的值，创建索引无意义
创建索引
```
ALTER TABLE students
ADD INDEX idx_socre(score);
```
对于主键，关系数据库会创建主键索引，因为抓紧的唯一性。
### 唯一索引
对于某些业务列添加唯一性约束：不能两条记录的某个字段相同，可以添加唯一索引
```
ALTER TABLE studnets
ADD UNIQUE INDEX uni_name(name);
```
通过UNIQUE关键字添加唯一索引。
也可以对某一列添加唯一约束而不创建唯一索引。
```
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```
name 列没有索引，但仍具有唯一性。
## 查询数据
### 条件查询
使用`SELECT * FROM <表名>`可以查询一张表的所有记录，我们可以根据条件选择性的获取指定的记录。
SELECT 语句通过WHERE 条件来设定查询条件。如：
```
SELECT * FROM students WHERE score>=80;
```
获取分数在80分以上的学生；
WHERE 后面即为条件，条件查询的语法为：
```
SELECT * FROM <表名> WHERE <条件表达式>
```
- 条件表达式可以为 <条件1>` AND` <条件2> 表示同时满足两个条件；
- 条件表达式可以为<条件1> `OR `<条件2> 表示满足任一个条件即可；
-` NOT` <条件> 表示不符合该条件的记录
`NOT class_id=2` 等价于class_id <> 2
若是有3个以上的条件，使用小括号()表示如何进行条件运算。
运算优先级：NOT>AND>OR;但是加上括号可以改变运算顺序。
#### 常用表达式
条件|表达式举例|说明
----|----|----
=|name='abc'|字符串需要用单引号括起来
<|name<'abc'|字符串比较使用ASCII码，中文字符根据数据库设置
\>|name>'abc'|
<=|小于等于
>=|大于等于
使用<> 不等于  |name<>'abc'  score<>80|空
使用LIKE判断相似|name LIKE 'ab%' name LIKE '%bc%'|%表示任意字符
IN|为字段指定多个可能的值|IN(column1,column2,...)
60~90(包含边界)输入操作
   - WHERE score>=60 AND score <=90
   - WHERE score BETWEEN 60 AND 90
   -  注意：WHERE  60<=score<=90 代表OR 操作。
#### SELECT DISTINCT
`SELECT DISTINCT`  statement  used to return only distinct (different) values.
syntax:
```
SELECT DISTINCT column1,column2,...FROM table_name
```
计算某个字段中不同的值的数目
```
SELECT COUNT(DISTINCT column) FROM table_name
```
### 投影查询
使用SELECT \*可以返回所有列，但是使用`SELECT 列1，列2，列3`等则可以返回指定列,这种操作称之为投影。
如果我们只需要返回某些列的数据，而不是所有的数据，可以使用`SELECT 列1，列2，列3 FROM...`，让结果集 仅包含指定列，称为投影查询。
结果集的列的顺序可以和原表不一致；
在投影查询中可以给每一个列起一个别名，这样结果集的列名和原表的就会不同。如：
```
SELECT 列1 别名1，列2 别名2，列3 别名3 FROM ...
```
同样投影查询可以使用WHERE  条件句实现复杂查询。
### 排序
使用SELECT 查询时，结果集通常以主键进行排序，也可以通过`ORDER BY`对其他条件进行从低到高ASC升序排序。
可以加上DESC 使得结果集从高到低排序
```
SELECT id,name,gender,score FROM students ORDER BY score DESC;
```
如果score 有相同的数据，可以继续添加列名进一步排序，使用
```
SELECT id,name,gender,score FROM students ORDER BY score DESC,gender;
```
首先按照score降序排列，若score 相同，使用gender排序；
也可以升序和降序同时运用
`SELECT * FROM table_name ORDER BY column1 ASC,column2 DESC` 先按照column1升序排列，若column1中的value相同，则再按照column2降序排列
如果需要WHERE 条件句，则ORDER BY 子句要放在WHERE 条件句之后。
### 分页查询
分页实际上就是从结果集中截取M~N条记录，可以通过
`LIMIT <M> OFFSET <N>`字句实现。
分页查询关键在于确定每页要显示的结果数量，pageSize，然后根据当前页的索引pageIndex，确定LIMIT 和OFFSET应设定的值。
`LIMIT M` 表示每页最多M条记录，总是设定为pageSize
`OFFSET `计算公式为pageSize*(pageIndex-1),OFFSET 是可选的，若省略，则相当于OFFSET 0结果集从0开始
MySQL中，`LIMIT 15 OFFSET 30`可写为`LIMIT 30,15`
OFFSET 即表明从哪一条记录开始查询，N越大，查询效率越低。
分页查询需要确定每页的数量和当前的页数。
**SQL记录集的索引是从0开始的**
```
SELECT id,name,gender,score
FROM students
WHERE score>=70  条件语句
ORDER BY score DESC  排序语句
LIMIT 3 OFFSET    分页查询语句
```
### 聚合查询
统计一张表的数据量使用SQL内置的`COUNT()`查询。
```
SELECT COUNT(*) FROM students;
```
COUNT(\*)表示查询所有列的行数，计算结果仍是一个二维列表，列表名为COUNT(\*)；也可设置别名
```
SELECT COUNT(*) num FROM students；
```
SQL提供的聚类函数
函数|说明
---------|---------
SUM()|计算某一列的合计值，必须 为数据类型
AVG()|计算某一类的平均值，必须为数值类型
MAX()|计算某一列的最大值
MIN()|计算某一列的最小值
`MAX()`和 `MIN()`函数可用于数值类型和字符字符类型，若是字符类型，则返回排序最后和最前的值。
若在聚合查询中，没有匹配`WHERE 条件句`，`COUNT()`返回0，`SUM()`,`AVG()`,`MAX()`,`MIN()`返回NULL。
每页3条记录，获取总页数,页数 不是3的倍数
```
SELECT CEILING(COUNT(*)/3) FROM students; 最后一页不足3条记录，页数为FLOOR(COUNT(*)/3)+1
```
#### 分组
SQL提供`GROUP BY`字句指定分组，进行分组聚合
```
SELECT class_id,gender,COUNT(*) num FROM students GROUP BY class_id,gender;
```
### 多表查询
查询多表的语法：
```
SELECT * FROM <表1>，<表2>
```
结果集也是一个二维表，是表1和表2的乘积，也即是表1 的每一行和表2的每一行两两组合在一起返回，结果集的**行数是表1和表2的行数之积，列数是表1和表2列数之和**。这种查询又称之为笛卡尔查询。
可通过**投影查询的设置列的别名的方式**来给两个表自己的列起别名，以便于区分
```
SELECT 
students.id sid,
students.name,
students.gender,
students.score,
classes.id sid,
classes.name cname
FROM students,classes;
```
多表查询时，要使用`表名.列名`的方式引用列和设置别名，避免结果集的的列名重复，SQL还允许给表设置别名
```
SELECT
s.id sid,
s.name,
s.gender,
s.score,
c.id cid,
c,name cname
FROM students s,classes c 分别给表设置别名。
WHERE s.gender='M' AND c.id=1;  WHERE 条件语句
```
### 连接查询
对多个表进行JOIN运算,先确定一个主表，将其他表的行有选择的连接到主表上
内连接 `INNER JOIN` 是选出两张表都存在的记录
<div align="center"><img src=E:/SQL/内连接.jpg  width="100px" height="100px">
</div>
<!--![内连接](E:/SQL/内连接.jpg)-->

`LEFT OUTER JOIN` 选出左表存在的记录
 <div align="center"><img src=E:/SQL/左外连接.jpg  width="100px" height="100px">
</div>

`RIGHT OUTER JOIN` 返回右表存在的记录，若某一行仅在右表出现，则结果集以`NULL`填充剩下的字段
 <div align="center"><img src=E:/SQL/右外连接.jpg  width="100px" height="100px">
</div>

`FULL OUTER JOIN` 左表或右表存在的记录全部选择出来，自动把对方不存在的列填充为`NULL`
 <div align="center"><img src=E:/SQL/全外连接.jpg  width="100px" height="100px">
</div>

`INNER JOIN`是一种常用的JOIN 查询，语法：
```
SELECT ...FROM <表1> INNER JOIN <表2> ON <条件...>;
```

写法：
1. 先确定主表，使用`FROM <表1>`的写法
2. 再确定需要连接的表，使用`INNER JOIN<表2>`语法
3. 确定连接条件，使用`ON <条件...>`,如条件`s.class_id=c.id` 表示`students`表的`class_id`列与`classes`表的`id`列相同行进行连接
4. 也可以使用`WHERE` 和`ORDER BY`排序。
## 修改数据
 关系数据库的基本操作，增删改查，create、retrieve、delete
 - INSERT:插入新记录
 - UPDATE:更新记录
 - DELETE:删除已有记录
### INSERT
基本语法：
```
INSERT INTO <表名>（字段1，字段2......） VALUES(值1，值2，......)
```
插入时，字段顺序不必和数据库表的字段顺序一致，但是**值的顺序必须和字段顺序一致**
可一次性添加多个记录，只需要在VALUES 中指定多个记录值即可，每个记录是由（......）包括的一组值
```
INSERT INTO students (calss_id,name,gender,score) VALUES  (2,"大宝"，"M",80),
(1,"二宝"，"M",81);增加记录
SELECT *  FROM students;查询表单所有记录
```
### UPDATE
用于对表中已存在的数据进行更改
基本语法
```
UPDATE <表名> SET 字段1=值1，字段2=值2，...WHERE  condition(条件语句);
```
`WHERE`条件句中写出需要更新的行筛选条件，如`id=1`
`UPDATE`语句中，更新字段时，也可以使用表达式，如：`SET score=score+10`
`UPDATE`语句若没有`WHERE`条件句，则全表更新
### DELETE
用于删除表中已经存在的记录
基本语法：
```
DELETE FROM <表名> WHERE...;
```
若没有使用`WHERE`条件句，则会删除整个表
## MySQL
命令行提示符中，输入`mysql -u root -p`输入口令后，连接上MySQL server。输入`exit`即断开连接，返回命令提示符
MySQL client中输入的SQL语句是通过 TCP连接发送到MySQL server ，默认端口3306，若发送至本机的MySQL server，地址 为`127.0.0.1:3306`
若连接至远程的MySQL server，即可用-p指定ip地址，如
`mysql -h 10.0.1.99 -u root -p`
 登录MySQL server:
 - 进入命令行提示符，切换至安装目录下
 - 进入安装目录的bin文件下
 - 启动MySQL server `net  start mysql`
 - 连接服务器 `mysql -u root -p`
 - 输入登录口令，即进入mysql命令行
MySQL库命令：
- 列出数据库 `SHOW DATABASES`;
	- `information_schema`,`mysql`,`performance_schema`,`sys`是系统库
- 创建新的数据库 `CREATE DATABASE 数据库名称`
- 删除数据库  `DROP DATABASE 数据库名称`
	- 删除数据库，，会删除该数据库所有的表
- 对数据库进行操作时，首先切换至该数据库 `USE 数据库名称`
### MySQL表
-  列出当前数据库的所有表  `SHOW TABLES`;
- 查看表的结构 `DESC 表的名称`
- 查看创建表的SQL语句 `SHOW CREATE TABLE 表的名称`
- 创建表 `CREATE TABLE`
- 删除表 `DROP TABLE`
- 修改表 若要新增一列
```
ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NULL; 为表students新增一列 birth
```
若要修改`birth`列，改为`birthday`，类型为VARCHAR(20)
```
ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL;
```
- 删除列 
`ALTER TABLE students DROP COLUMN birthday`；删除birthday一列
- 退出MySQL `EXIT`，需要注意的是使用EXIT仅仅断开client 与server的连接，MySQL服务器依然运行
- **替换插入** 插入一条新记录，若记录存在，则删除原记录，再插入新纪录，使用`REPLACE`语句
```
REPLACE INTO students (id, class_id,name,gender,score)	VALUE(1,1,	'小明','F',O99)
```
若id=1记录不存在，直接插入，若存在，删除原来的记录，再插入
- **更新插入** 插入一条新记录，若记录存在，就更新该记录`INSERT INTO ...ON DUPLICATE KEY UPDATE ...`语句
```
INSERT INTO students （id,class_id,name,gender,score）VALUES (1,1,'小明'，‘M’，90) ON DUPLICATE KEY UPDATE name='小李'，gender='M'，score=99；
```
若是为表中所有的字段插入数据，则不需要特别指出字段的名字。
- **忽略性插入** 插入一条记录，过记录存在，则忽略，使用`INSERT IGNORE INTO...`
```
INSERT IGNORE INTO students（id,name,gender,clsss_id,score） VALUES(1,'小明'，'M',1,89);
```
- **快照** 若想讲一份表复制当前数据到另个新表，可以结合使用`CREATE TABLE `和`SELECT`。
```
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;d对class_id=1的记录进行快照，将其存储为新表
```
	- **写入插入结果集**将结果集写入到表格中，使用`INSERT INTO`、 `SELECT`等语法
创建新表
```
CREATE TABLE statistics (
		id BIGINT NOT NULL ATUO_INCREMENT,
		class_id BIGINT NOT NULL,
		average DOUBLE NOT NULL,
		PRIMARY KEY(id)
);
```
写入结果集
```
INSERT INTO statistics (class_id,average) SELECT class_id,AVG(score) FROM students GROUP BY class_id;
```
## SQL NULL values
某个字段的值为NULL ，并不是说为0或space，是指在创建的时候为blank。
测试某个字段的值是否为NULL ，使用`IS NULL` 和`IS NOT NULL`操作符。
格式：
```
SELECT column_name
FROM table_name 
WHERE column_name IS NULL;
```
```
SELECT column_name
FROM table_name 
WHERE column_name IS NOT NULL;
```
SELECT TOP语句用于指定返回的结果的数值。对于不同的数据库系统，也具有不同的语法规则
SQL server/MS Access:
```
SELECT TOP number\PERCENT column_name FROM table_name WHERE condition;
```
`SELECT TOP 3 * FROM customers;`
`SELECT TOP 50 PERCENT * FROM customers;`
MySQL：
```
SELECT column_name FROM table_name WHERE condition LIMIT number;
```
Oracle:
```
SELECT column_name FROM table_name WHERE ROWNUM<=number;
```
#### LIKE operator
有两个通配符wildcard:%,_
% 代表任意数个字符
\_下划线代表单个字符
**MS Access** 中：
使用* 代表任意数个字符
使用？代表单个字符
```
SELECT column1,column2 FROM table_name WHERE columnN LIKE pattern
```
若是不相似的情况下，使用**NOT LIKE**
#### 通配符 wildcard characters
**MS Access**:
符号|描述|示例
----|----|----
*|代表任意个字符|bl* 可以表示为bl,black,blue等
?|代表单个字符|h?t 可表示为hot,hit,hat等
[]|代表括号内的任意字符|h[oa]t 可以表示是hot或者hat,不能是hit
!|代表不在括号内的任意字符|h[!oa]t不能是hot或者hat,可以是hit
_|	代表一系列数值|c[a-b]t 代表cat和cbt
#|代表单个数字字符|2#5，可以表示为205,215,225,235....295

**SQL server**：
符号|描述
----|----
%|代表任意个字符
\_|代表单个字符
\[]|括号内的任意的单个字符
^(shift+6)|代表不在括号内的任意字符
-|代表一系列的字符
#### IN操作符
用于指定WHERE 语句中的多个值 ，是多个OR运算符的简写
```
SELECT column_name(s) FROM table_name WHERE column_name IN (value1,calue2,...);
```
```
SELECT column_name(s) FROM table_name WHERE column_name IN(SELECT  STATEMENT);
```
若不在指定的多个值中，使用`NOT IN`
示例`SELECT STATEMENT`
```
SELECT * FROM Customers WHERE Country IN(SELECT Country FROM Suppliers);在WHERE 条件句中使用另外一个表
```
#### BETWEEN  operator
`BETWEEN` 操作符用于在给定的范围内进行选择,这个值可以是数字、文本、日期，它是包含开始值和结束值
```
SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2;
SELECT * FROM Products WHERE ProductName BETWEEN 'carnarvon'AND 'mozzarella' ORDER BY ProductName; 文本操作
SELECT * FROM Orders WHERE orderDate BETWEEN #01/07/1996# AND #31/07/1996#;或者('1996-07-01' AND '1996-07-31';) 日期操作 
```
#### SQL Aliases 
用于给表或者表中的字段取一个临时别名，使得字段更加易读，仅在查询的时候存在
Alias Column syntax:
```
SELECT column_name AS alias_name FROM table_name;
```
Alias table syntax
```
SELECT column_anme FROM table_name AS alias_name;
```
如果引用的别名中带有空格，则需要`double quotation marks`或者 `square brackets`
```
SELECT customer_name AS Customer,Contact_naem AS [Contact person] FROM table_name;
```
SQL statement
```
SELECT customer_name,adress+','+postalcode+''+city+','+country AS adress
```
MySQL statement：
```
SELECT customer_name,CONCAT(adress,',',postalcode,',',city,',',country) AS Adress FROM customers;
```
表的别名用法
```
SELECT o.orderID,o.orderDate,c.customerName FROM customers AS c,orders AS o WHERE c.customerName='aoround the horn' AND c.customerID=o.customerID;
```
别名的用法：
 - 用于多个表格的查询中
 - 查询中使用了函数
 - 字段的名字很长或者不宜读
 - 多个字段混合使用
 - 定义别名时，关键词`AS`可以省略不写
#### SQL JOIN
SQL 语句基于相同的字段，用来联合多个表格
#### SQL INNER JOIN
用于选择在两个表中都match的记录
```
SELECT o.OrderID, c.CustomerName, s.ShipperName
FROM ((Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID)
INNER JOIN Shippers s ON o.ShipperID = s.ShipperID);别名定义时，关键词AS可以省略掉不写
```
#### SQL LEFT JOIIN(LEFT OUTER JOIN)
the LEFT JOIN keyword returns all records  from the left table(table1),and the matched records from the right table(table2). the result is NULL  from the right table ,if there is no match.
#### SQL RIGHT JOIN(RIGHT OUTER JOIN)
The RIGHT JOIN keyword returns all records from the right table(table2),and the matched records from the left table(table1).The result is NULL from the left side,if there is no match.
#### SQL FULL OUTER JOIN(FULL JOIN)
The FULL OUTER JOIN keyword return all records  when there is a match  in either left or  right table records.
#### SQL Self 	Join
a self join is a regular join,but the table is joined with itself.
```
SELECT column_name FROM table1 T1 ,table1 T2 WHERE CONDITION;
```
T1 T2是同一个表的不同别名
示例：
```
SELECT A.CustomerName AS CustomerName1,B.CustomerName AS CustomerName2,A.City  
FROM Customers A,Customers B
WHERE A.CustomerID <> B.CustomerID
AND A.City=B.City 
ORDER BY A.City;
```
#### SQL UNION 
UNION将两个或多个SELECT语句的结果集组合到一起。
- 每一个SELECT语句必须具有相同数量的字段
- 字段的数据类型必须相同
- 每一个SELECT语句字段顺序必须一致
- 结果集的字段名字通常和UNION字段第一个SELECT语句中表的名字相同。 
UNION默认 筛选不同的值，若是允许相同，可以使用`UNION ALL`。
```
SELECT column_name(s) FROM table1 WHERE CONDITION
UNION (UNION ALL)
SELECT column_name(s) FROM table2 WHERE CONDITION;
```
EXAMPLE:
```
SELECT 'Customer' As Type, ContactName, City, Country
FROM Customers
UNION
SELECT 'Supplier', ContactName, City, Country
FROM Suppliers结果集的名字和第一个SELECT语句的字段名字相一致
```
#### GROUP BY
GROUP BY 语句通常和聚合查询函数(COUNT,SUM,MIN,MAX,AVG)一起使用，通过一个或多个字段对结果集进行组合
```
SELECT column_name(s) FROM table_name
WHERE CONDITON
GROUP BY column_name(s)
ORDER BY column_name(s);
```
`GROUP BY` WITH `JOIN` EXAMPLE
```
SELECT Shippers.ShipperName,COUNT(Orders.OrderID) AS NumberOfOrders FROM Orders
LEFT JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID
GROUP BY ShipperName;
```
#### SQL HAVING
WHERE 关键词不能使用聚合函数，由HAVING 语句
the having clause was added to SQL because the WHERE keyword could not  be used with aggretate functions
```
SELECT column_name(s)
FROM table_name
WHERE CONDITION
GROUP BY column_name(s)
HAVING  CONDITION
ORDER BY column_name(s);
```
示例分析：
```
SELECT Employees.LastName, COUNT(Orders.OrderID) AS NumberOfOrders
FROM (Orders
INNER JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID)
GROUP BY LastName
HAVING COUNT(Orders.OrderID) > 10;
--------------------
SELECT o.EmployeeID, COUNT(o.OrderID), e.LastName AS Name
FROM Orders o
INNER JOIN Employees e ON o.EmployeeID=e.EmployeeID
GROUP BY  Name
HAVING COUNT(o.OrderID)>10
ORDER BY COUNT(o.OrderID);
```
#### SQL EXISTS
EXISTS 用于检测子查询中记录是否存在，若子查询结果集返回一条或多记录，则返回真
```
SELECT column_name(s)
FROM table_name
WHERE EIXTS
(SELECT column_name FROM table_name WHERE CONDITION);
```
#### SQL ANY and ALL
ANY 与ALL 与WHERE ,HAVING 语句中使用
ANY 返回true，如果任一子查询结果符合给定的条件
ALL 返回true，如果所有的子查询结果符合给定的条件
ANY:
```
SELECT column_name(s) FROM table_name
WHERE column_name operator ANY
(SELECT column_name FROM table_name WHERE condition);
```
ALL:
```
SELECT  column_name(s) FROM table_name
WHERE column_name operator ALL
(SELECT column_name FROM table_name WHERE condition);
```
operator 包括=,>,<,>=,<=,<>,!=
__EX__:
```
SELECT ProductName FROM Products
WHERE ProductID =ALL(ANY)
(SELECT ProductID FROM OrderDetails WHERE Quantity>90);
```
#### SQL SELECT INTO
select into statement copies data from one table into a new table.
```
SELECT *(column1,column2...) INTO newtable [IN externaldb]
FROM oldtable 
WHERE condition;
```
newtable将会根据oldtable中的字段名和type创建，当然也可以使用AS 语句创建别名
```
SELECT  * INTO CustomerBckup IN 'Backup.db'
FROM Customers;从其他的数据库中copy数据
```
SELECT  INTO 语句可以用来创建一个新的空表格，只需要 WHERE 语句不返回结果
```
SELECT * INTO newtable FROM oldtable WHERE 1==0;
```
#### SQL INSERT INTO SELECT
INSERT INTO SELECT statement 从一个表格复制数据到另一个表格
		- 要求源表和目的表的数据类型想匹配
		- 目的表已经存在的记录不受影响
```
INSERT INTO table2 (column1,column2,column3...)
SELECT * (column1,column2,column3...) FROM table1
WHERE condition;
```
__EX__:
```
INSERT INTO Customers(CustomerName,City,Country)
SELECT SupplierName,City,Country FROM Suppliers
WHERE Country='German';
```
#### SQL CASE
			- CASE 语句遍历条件，并且当在第一个条件被满足时返回一个值（如IF-THEN-ELSE语句），因此，一旦条件成立，将停止读取并且返回结果，若遍历所有的条件都不满足，则返回ELSE 语句中的值。
			- 若是没有ELSE 语句且所有条件均不成立，返回NULL
```
CASE
		WHEN condition1	THEN result1
		WHEN condition2 THEN result2
		WHEN conditionN THEN resultN
		ELSE result
END;
```
__EX__:
```
SELECT OrderID,Quantity,
CASE
		WHEN Quantity>30 THEN "the quantity is greater than 30"
		WHEN Quantity=30 THEN "the quantity is equal 30	"
		ELSE "the quantity is under 30"
END AS QuantityText
FROM Orderdetails;
```
```
SELECT CustomerName ,City,Country
FROM Customers
ORDER BY
		（CASE
					WHEN City IS NULL THEN Country
					ELSE City
		END）;
```
#### SQL NULL FUNCTION
SQL` IFNULL()`,`ISNULL()`,	`COALESEC()`,`NVL()`functions
MYSQL:当一个表达为NULL 时，MySQL的`IFNULL()`允许返回一个可选的值
```
SELECT ProductName,Unitprice*(UnitsInstock+IFNULL(UnitsOnOrder,0))
FROM Products;其中假定UnitsOnOrder 可以为NULL
```
或者可以用`COALESEC()`替换`IFNULL`
SQL server中，可以使用`ISNULL()` 
MS Access中，可以使用`IsNull()`,当表达为NULL时，返回TRUE(-1),	其他则返回FALSE(0)
```
SELECT ProductName,Unitsprice*(UnitsInStock+IIF(IsNull(UnitsOnOrder),0,UnitsOnOrder))
FROM Products;
```
Oracel中使用`NVL()`可以达到同样的结果
```
SELECT ProductName,Unitsprice*(UnitsInStock+NVL(UnitsOnOrder,0))
FROM Products;
```
#### SQL Stored	Procedures for SQL Server
存储过程(stored procedures)是您可以保存的准备好的SQL代码，因此代码可以反复重用。 
如果您有一个反复编写的SQL查询，请将其另存为存储过程，然后调用它来执行它。 
还可以将参数传递给存储过程，以便存储过程可以根据传递的参数值进行操作。
Stored a Procedure
```
CREATE PROCEDURE procedure_name
AS
sql_statement 这是SQL操作语句
GO;
```
Execute a procedure
```
EXEC procedure_name;
```
存储带有多个参数的stored procedure
```
CREATE PROCEDURE SelectAllCustomers @City nvarchar(30),@Postalcode nvarchar(10)
AS
SELECT * FROM Customers 
WHERE City=@City AND Postalcode=@Postalcode
GO;
```
执行stored procedure
```
EXEC SelectAllCustomers City="London",Postalcode="WA1 1DP";
```
#### SQL Comments
注释用于解释SQL语句的各个部分，或用于防止执行SQL语句。
单行注释:
单行注释开始于"--",结束于这一行的末端，中间的SQL语句不被执行
```
SELECT * FROM Customers -- WHERE City="Berlin";
```
多行注释：
多行注释起始于"/\*"结束于"\*/",中间的所有的文本都不会执行
可以用于多行注释 ，也可以用于单一行中语句的注释

## SQL database
#### SQL CREATE DB 创建数据库
`CREATE DATABASE  databasename;`
查看数据库信息：
`SHOW DATABASES;`
#### SQL DROP DB 删除现有的数据库
`DROP DATABASE databasename；`
删除数据库将会丢失存储在数据库中的完整的信息
#### SQL BACKUP DB for SQL server
在SQL server中使用BACKUP DATABASE语句创建现有数据库的完整备份
`BACKUP DATABASE databasename TO DISK='filepath';`
SQL BACKUP WITH DIFFERENTIAL statement
差异备份仅备份字上次完全数据库备份以来更改的数据库部分
```
BACKUP DATABASE databasename
TO DISK='filepath'
WITH  DIFFERENTIAL;
```
__EX__:
```
BACKUP DATABASE testDB
TO DISK='D:\backups\testDB.bak'
WITH DIFFERENTIAL;
```
#### SQL CREATE TABLE
用于在数据库中创建新表
```
 CREATE TABLE tablename(
 		column1 datatype,
		column2 datatype,
		column3 datatype,
		....
		);
```
使用已有表来创建新表，新表中的值将由原先的表值填充
```
CREATE TABLE new_tablename AS
SELECT column1,column2,...
FROM existing_table_name
WHERE....;
```
####  SQL DROP TABLE
删除已数据库中已经存在的表
`DROP TABLE tablename`
##### SQL TRUNCATE TABLE
用来删除表中的数据，而非表本身
#### SQL ALTER TABLE
ALTER TABLE语句用来add、delete、modify已存在的表格的字段，也用来add、drop已存在表格的各种约束
```
ALTER TABLE tablename
ADD column_name datatype;添加字段
DROP COLUMN  column_name;删除表格中的字段
```
更改表格中字段的数据类型
SQL server/ MS Access:
```
ALTER TABLE table_name
ALTER COLUMN column_name datatype;
```
MySQL/Oracle
```
ALTER TABLE table_name
MODIFY COLUMN column_name datatype;
```
__EX__:
```
ALTER TABLE tablename
ADD BirthOfDate date;日期的数据类型为date
```
SQL Constraints
为表中的数据指定规则,可以在CREATE TABLE时指定约束条件或者在表格创建后ALTER TABLE时指定
```
CREATE TABLE tablename(
		column1 datatype constraint,
		column2 datatype constraint,
		column3 datatype constraint,
		...
	);	
```
#### SQL commonly  constraints常见的约束条件
constraint|explaination
----|----
NOT NULL|确保字段值不为NULL
UNIQUE|确保字段中的所有值是不同的
PRIMARY KEY|NOT NULL 和UNIQUE的组合，唯一标示表中的每一行
FOREIGN KEY|唯一标示另一个表中的行或者记录
CHECK|确保字段中所有值满足约束条件
DEFAULT|为没有明确指明值得字段设定一个默认值
INDEX|用于很快的从数据库中创建和检索数据
#### SQL NOT NULL constraint
默认情况下，默认字段可以为NULL值
NOT NULL 约束强制约定字段不为NULL 值
这个约束会限制字段强制包含一个值，使得无法在不向这个字段添加新值的情况下插入或者更新记录。
```为已经创建的表格字段添加约束
ALTER TABLE Persons
MODIFY Age int NOT NULL;
```
#### SQL UNIQUE constraint
UNIQUE 约束确保字段所有的值都是不同的；
UNIQUE 和PRIMARY 约束可以确保字段的唯一性；
PRIMARY KEY 约束	自动具有UNIQUE 约束；
每个table可以有多个UNIQUE 约束，只能有一个PRIMARY KEY 约束；
SQL	 server/Oracle/MS Access:
```
CREATE  TABLE Persons(
		ID INT NOT NULL UNIQUE,
		LastName varchar(255) NOT NULL,
		FirstName varchar(255),
		Age int
		);
```
MySQL：
```
CREATE TABLE Persons(
		ID INT  NOT NULL,
		LastName varchar(255) NOT NULL,
		FirstName varchar(255),
		Age int,
		UNIQUE(ID)
		);
```
命名UNIQUE 约束并为多个字段定义UNIQUE 约束：
`MySQL/SQL server/Oracle/MS Access`
```
CREATE TABLE Persons（
	ID int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Age int,
    CONSTRAINT UC_Person UNIQUE (ID,LastName)）
```
在已创建的表格上添加约束或者删除约束
`MySQL/SQL server/Oracle/MS Access`
```
ALTER TABLE Persons
ADD CONSTRAINT UC_Person UNIQUE(ID,LastName);
```
MySQL：
```
ALTER TABLE Persons
DROP INDEX UC_Person;
```
`SQL Server / Oracle / MS Access:`
```
ALTER TABLE Persons 
DROP CONSTRAINT UC_Person;
```
#### SQL PRIMARY KEY
PRIMARY KEY 包含UNIQUE 且不为NULL;
一个table 只能有一个PRIMARY KEY;
一个PRIMARY KEY 可以包含一个或多个字段；
PRIMARY KEY 用法和UNIQUE类似，只需要替换即可。
一点不同的是在MySQL中删除PRIMARY KEY 时：
```
ALTER TABLE Persons
DROP PRIMARY KEY;
```
#### SQL FOREIGN KEY
一个FOREIGN KEY 是将两个TABLE 连接起来的键
FOREIGN KEY 是一个表中的字段或字段的集合，它引用自另一个表格中PRIMARY KEY;
包含有外键的表称之为字表，包含有候选键的表称之为引用表或者父表；
FOREIGN KEY 约束用来防止破坏表之间的连接；也可以限制无效数据的嵌入。它必须是指向表中包含的值
MySQL：
```
CREATE TABLE Orders(
	OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    FOREIGN KEY (PersonID) REFERENCES Persons(PersonID)
	);
```
SQL/Oracle/MS Access:
```
	CREATE TABLE Orders(
			OrderID INT NOT NULL PRIMARY KEY,
			OrderNumber INT NOT NULL,
			PersonID int FOREIGN KEY REFENENCES Persons(PersonID)
			);
```
为外键约束别名
```
CREATE TABLE Orders (
    OrderID int NOT NULL,
    OrderNumber int NOT NULL,
    PersonID int,
    PRIMARY KEY (OrderID),
    CONSTRAINT FK_PersonOrder FOREIGN KEY (PersonID)
    REFERENCES Persons(PersonID)
);
```
增加外键约束
MySQL/SQL server/Oracle/MS Access：
```
ALTER TABLE Orders
ADD FOREIGN KEY(PersonID) REFERENCES Persons(PersonID);添加外键约束
ADD CONSTRAINT FK_person FOREIFN KEY(PersonID) REFERENCES Persons(PersonID);添加外键约束并别名
```
删除外键约束
MySQL：
``
ALTER TABLE Orders
DROP FOREIGN KEY FK_person;
```
SQL server/Oracle/MS Access:
```
ALTER TABLE Orders
DROP CONSTRAINT FK_person;
```
