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