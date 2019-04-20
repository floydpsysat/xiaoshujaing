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
- 条件表达式可以为 <条件1> AND <条件2> 表示同时满足两个条件；
- 条件表达式可以为<条件1> OR <条件2> 表示满足任一个条件即可；
- NOT <条件> 表示不符合该条件的记录
NOT class_id=2 等价于class_id <> 2
若是有3个以上的条件，使用小括号()表示如何进行条件运算。
运算优先级：NOT>AND>OR;但是加上括号可以改变运算顺序。
#### 常用表达式
条件|表达式举例|说明
----|----|----
=|name='abc'|字符串需要用单引号括起来
<|name<'abc'|字符串比较使用ASCII码，中文字符根据数据库设置
>|name>'abc'|
<=|小于等于
>=|大于等于
使用<>判断不相等|name<>'abc'  score<>80|空
使用LIKE判断相似|name LIKE 'ab%' name LIKE '%bc%'|%表示任意字符