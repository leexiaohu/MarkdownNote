#  MySQL数据库

[TOC]

### MySQL 索引

索引分单列索引和组合索引。单列索引，即一个索引只包含单个列，一个表可以有多个单列索引，但这不是组合索引。组合索引，即一个索包含多个列。

创建索引时，你需要确保该索引是应用在 SQL 查询语句的条件(一般作为 WHERE 子句的条件)。

> 实际上，索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录。

#### 普通索引

###### 创建索引

```sql
CREATE INDEX indexName ON mytable(username(length)); 
```
###### 修改表结构

```sql
ALTER mytable ADD INDEX [indexName] ON (username(length)) 
```
###### 创建表的时候直接指定

```sql
CREATE TABLE mytable(  
 
ID INT NOT NULL,   
 
username VARCHAR(16) NOT NULL,  
 
INDEX [indexName] (username(length))  
 
);  
```
###### 删除索引的语法
```sql
DROP INDEX [indexName] ON mytable;
```

#### 唯一索引

###### 创建唯一索引
```sql
CREATE UNIQUE INDEX indexName ON mytable(username(length)) 
```
### MySQL 复制表

如果你想拷贝数据表的数据你可以使用 INSERT INTO... SELECT 语句来实现。

```sql
SELECT w3cschool_id,w3cschool_title,
w3cschool_author,submission_date
FROM w3cschool_tbl;
```
### MySQL 元数据

#### 获取服务器元数据

| 命令 | 描述 |
|--------|--------|
|     SELECT VERSION( )   |     服务器版本信息   |
|SELECT DATABASE( )     | 当前数据库名 (或者返回空) |
|  SELECT USER( ) |  当前用户名 |
| SHOW STATUS | 服务器状态 |
| SHOW VARIABLES | 服务器配置变量 |