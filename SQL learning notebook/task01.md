本笔记为[阿里云天池龙珠计划SQL训练营](https://tianchi.aliyun.com/specials/promotion/aicampsql)的学习内容

## 数据库管理系统（DBMS）
层次数据库（Hierarchical Database, HDB）  
关系数据库（Relational Database, RDB）  
> Oracle Database  
> SQL Server  
> DB2  
> PostgreSQL  
> MySQL  

面向对象数据库（Object Oriented Database, OODB）  
XML数据库（Object Oriented Database, OODB）  
键值存储系统（Object Oriented Database, OODB）  
## RDBMS的常见系统结构
使用 RDBMS 时，最常见的系统结构就是客户端 / 服务器类型（C/S类型）这种结构（图 1-3）
![Image](https://user-images.githubusercontent.com/66725315/185875800-70979bc1-be19-4579-b787-fc799955c4f1.png)
## SQL概念介绍
根据对 RDBMS 赋予的指令种类的不同，SQL 语句可以分为以下三类：
1. **DDL（Data Definition Language，数据定义语言）** 用来创建或者删除存储数据用的数据库以及数据库中的表等对象。
> CREATE ： 创建数据库和表等对象  
> DROP ： 删除数据库和表等对象  
> ALTER ： 修改数据库和表等对象的结构  
2. **DML（Data Manipulation Language，数据操纵语言）** 用来查询或者变更表中的记录。
> SELECT ：查询表中的数据  
> INSERT ：向表中插入新数据  
> UPDATE ：更新表中的数据  
> DELETE ：删除表中的数据  
3. **DCL（Data Control Language，数据控制语言）** 用来确认或者取消对数据库中的数据进行的变更。除此之外，还可以对 RDBMS 的用户是否有权限操作数据库中的对象（数据库表等）进行设定。
> COMMIT ： 确认对数据库中的数据进行的变更  
> ROLLBACK ： 取消对数据库中的数据进行的变更  
> GRANT ： 赋予用户操作权限  
> REVOKE ： 取消用户的操作权限  
## SQL基本语句
**四种最基本的数据类型**
> _INTEGER 型：_用来指定存储整数的列的数据类型（数字型），不能存储小数。  
> _CHAR 型：_用来存储定长字符串，当列中存储的字符串长度达不到最大长度的时候，使用半角空格进行补足，由于会浪费存储空间，所以一般不使用。  
> _VARCHAR 型：_用来存储可变长度字符串，定长字符串在字符数未达到最大长度时会用半角空格补足，但可变长字符串不同，即使字符数未达到最大长度，也不会用半角空格补足。  
> _DATE 型：_用来指定存储日期（年月日）的列的数据类型（日期型）。

**约束的设置**  
*NOT NULL*是非空约束，即该列必须输入数据。  
*PRIMARY KEY*是主键约束，代表该列是唯一值，可以通过该列取出特定的行的数据。

### 表的创建删除和更新
```
#数据库的创建:CREATE DATABASE < 数据库名称 >
# CREATE DATABASE shop;

#创建表
create table product (
    product_id char(4) not null,
    product_name varchar(100) not null,
    product_type varchar(32) not null,
    sale_price integer,
    purchase_price integer,
    regist_data date,
    primary key(product_id)
)
```





















