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
SUM|计算某一列的合计值，必须 为数据类型
AVG|计算某一类的平均值，必须为数值类型
MAX|计算某一列的最大值
MIN|计算某一列的最小值
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
基本语法
```
UPDATE <表名> SET 字段1=值1，字段2=值2，...WHERE ...;
```
`WHERE`条件句中写出需要更新的行筛选条件，如`id=1`
`UPDATE`语句中，更新字段时，也可以使用表达式，如：`SET score=score+10`
`UPDATE`语句若没有`WHERE`条件句，则全表更新
### DELETE
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

