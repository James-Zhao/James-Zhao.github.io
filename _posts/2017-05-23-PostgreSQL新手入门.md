---
layout: post
title:  PostgreSQL新手入门
date:   2017-05-23 22:17:00
---
这篇博客主要对PostgreSQL进行总结，内容偏基础。

这里先附上一个PostgreSQL的中文资源：[PostgreSQL 8.1 中文文档](http://www.kuqin.com/postgreSQL8.1_doc/)。英文不好的同学可以看看这个。

## 安装PostgreSQL
##### 安装PostgreSQL客户端
```
sudo apt-get install postgresql-client
```
##### 安装PostgreSQL服务器
```
sudo apt-get install postgresql
```

##### 安装图形管理界面
```
sudo apt-get install pgadmin3
```

## PostgreSQL添加新用户和新数据库
### 方法1：PostgreSQL控制台
##### 连接数据库
```
sudo su - postgres
psql -U user -d dbname
```
第一步是切换到postgres用户，第二步是登录到PostgreSQL控制台。当你直接敲psql登录时，实际上是以同名数据库用户的身份登录数据库。

如果一切正常，系统提示符会变为"postgres=#"，表示这时已经进入了数据库控制台。以下的命令都在控制台内完成。

##### 设置系统用户密码
```
\password postgres
```
为postgres用户设置密码，敲下这个命令后，控制台会提示你让你输入新的密码。注意，这里设置的是Linux系统用户postgresql的密码，并非数据库用户的密码。


##### 创建数据库用户
```
CREATE USER dbuser WITH PASSWORD 'password';
```

##### 创建数据库
```
CREATE DATABASE exampledb OWNER dbuser;
```
创建用户数据库，这里为exampledb，并指定所有者为dbuser。

##### 所有权赋予用户
```
GRANT ALL PRIVILEGES ON DATABASE exampledb to dbuser;
```

##### 退出控制台
```
\q
```
### 方法2：使用shell命令行
添加新用户和新数据库，除了在PostgreSQL控制台内，还可以在shell命令行下完成。这是因为PostgreSQL提供了命令行程序createuser和createdb。还是以新建用户dbuser和数据库exampledb为例。

##### 创建数据库用户dbuser，并指定其为超级用户。
```
sudo -u postgres createuser --superuser dbuser
```
##### 登录数据库控制台，设置dbuser用户的密码，完成后退出控制台。
```
sudo -u postgres psql
\password dbuser
\q
```
##### 创建数据库exampledb，并指定所有者为dbuser
```
sudo -u postgres createdb -O dbuser exampledb
```

## 登录数据库
添加新用户和新数据库以后，就要以新用户的名义登录数据库，这时使用的是psql命令。
```
psql -U dbuser -d exampledb -h 127.0.0.1 -p 5432
```
上面命令的参数含义如下：-U指定用户，-d指定数据库，-h指定服务器，-p指定端口。

输入上面命令以后，系统会提示输入dbuser用户的密码。输入正确，就可以登录控制台了。

## 控制台命令
除了前面已经用到的\password命令（设置密码）和\q命令（退出）以外，控制台还提供一系列其他命令。
```
\h：查看SQL命令的解释，比如\h select。
\?：查看psql命令列表。
\l：列出所有数据库。
\c [database_name]：连接其他数据库。
\d：列出当前数据库的所有表格。
\d [table_name]：列出某一张表格的结构。
\du：列出所有用户。
\e：打开文本编辑器。
\conninfo：列出当前数据库和连接的信息。
```
## 数据库操作
基本的数据库操作，就是使用一般的SQL语言。
```
# 创建新表
CREATE TABLE user_tbl(name VARCHAR(20), signup_date DATE);
# 插入数据
INSERT INTO user_tbl(name, signup_date) VALUES('张三', '2013-12-22');
# 选择记录
SELECT * FROM user_tbl;
# 更新数据
UPDATE user_tbl set name = '李四' WHERE name = '张三';
# 删除记录
DELETE FROM user_tbl WHERE name = '李四' ;
# 添加栏位
ALTER TABLE user_tbl ADD email VARCHAR(40);
# 更新结构
ALTER TABLE user_tbl ALTER COLUMN signup_date SET NOT NULL;
# 更名栏位
ALTER TABLE user_tbl RENAME COLUMN signup_date TO signup;
# 删除栏位
ALTER TABLE user_tbl DROP COLUMN email;
# 表格更名
ALTER TABLE user_tbl RENAME TO backup_tbl;
# 删除表格
DROP TABLE IF EXISTS backup_tbl;
# 给一个字段设置缺省值
ALTER TABLE [表名] ALTER COLUMN [字段名] SET DEFAULT [新的默认值];
# 去除缺省值
ALTER TABLE [表名] ALTER COLUMN [字段名] DROP DEFAULT;
# 更改字段类型
ALTER TABLE ALTER COLUMN TYPE;
```

## 处理导出的数据中文显示乱码问题
PostgreSQL本身提供的copy命令可以将数据导成csv格式。但是在某些Linux环境下导出的csv文件，传输到windows环境下打开中文却显示乱码。

### 解决方法1
一般PostgreSQL建库都是用的UTF8字符集，在UTF8字符集情况下如果中文不能正常显示，可以设置客户端字符集，修改成"GBK" ，命令如下
```
--修改客户端字符集
postgres=# show client_encoding;
 client_encoding
-----------------
 UTF8
(1 row)

postgres=# set client_encoding='GBK';
SET

--copy 导出数据到 GBK 编码类型的 csv 文件
skytf=# copy skytf.test_2 to '/home/postgres/script/tf/skytf.test_2.csv' with csv header;
COPY 1000000
```

### 解决方法2
iconv是linux命令，用来转换文件的编码的，手册解释如下"Convert encoding of given files from one encoding to another"，我们可以使用iconv命令转换文件的编码，如果 utf8编码的文件中文显示为乱码，可以使用iconv命令将UTF8格式文件转换成gb18030。
```
--导出数据到 utf8 编码类型文件。
skytf=# set client_encoding='UTF8';
SET

skytf=# show client_encoding;
 client_encoding
-----------------
 UTF8
(1 row)

skytf=# copy skytf.test_2 to '/home/postgres/script/tf/skytf.test_2.csv' with csv header;
COPY 1000000

--将文件编码由 utf8 转换成 gb18030
iconv -f utf-8 -t gb18030 skytf.test_2.csv -o skytf.test_2_gbk.csv
```

## 文件导入导出
##### 文件导出
```
\copy table to data.csv  delimiter as ‘,’ csv quote as ‘”‘
```

##### 文件导入
```
\copy  table from data.csv delimiter as ‘,’ csv quote as ‘”‘
```



## 参考博客
[PostgreSQL新手入门](http://www.ruanyifeng.com/blog/2013/12/getting_started_with_postgresql.html)

[PostgreSQL: 如何处理导出的数据中文显示乱码问题？  ](http://francs3.blog.163.com/blog/static/4057672720120694224195/)
