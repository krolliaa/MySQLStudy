# 数据库概述

## 为什么要使用数据库？

- 持久化：将数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到磁盘加以固化，而持久化的实现过程大多数通过各种关系型数据库来完成。

  持久化的主要作用就是：将内存中的数据存储到关系型数据库，当然也可以存储在磁盘文件、`XML`数据文件中。

## 数据库与数据库管理系统

- `DataBase`：存储数据的仓库，其本质是一个文件系统，保存了一系列有组织的数据
- `DBMS`：数据库管理系统，操纵和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。
- `SQL`：结构化查询语言，专门用来与数据库通信的语言。

数据库管理系统可以管理多个数据库，一般开发人员会对每一个应用创建一个数据库。为了保存应用中实体的数据，一般会在数据库中创建多个表，以保存程序中实体用户的数据。`MongoDB`是文档型数据库，`ElasticSearch`是搜索引擎数据库。

## 关系型数据库设计原则

- 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己。表明具有唯一性。
- 表具有一些特性，这些特性定义了数据在表中如何存储，类似`Java`中的类设计。

## 表、记录、字段

- `E-R`实体-联系，模型中主要的三个概念：实体集、属性、联系集

  - 一个实体集`class`对应于数据库中的一个表`table`
  - 一个实体`instance`对应于数据库表中的一个行`row`，也称为一条记录`record`
  - 一个属性`attribue`对应于数据库表中的一个列`column`，也称为一个字段`field`

  即：表、行（记录）、列（字段）

## 表与表之间的关系

- 表与表之间的数据记录有关系`relationship`。现实世界中的各种实体以及实体之间的各种联系均用关系模型来表示。

  表与表之间的关系总的来说有四种：**一对一关联**、**一对多关联**【或者叫多对一】、**多对多关联**、**自我引用**

  - **一对一关联：**

    - 实际开发中应用不多，因为一对一的关联通常可以创建成是一张表
    - 举例：设计 学生表 ：学号、姓名、手机号码、班级、系别、身份证号码、家庭住址、籍贯、紧急 联系人、...，此时可以拆分成两张表
      - 基础信息表 （常用信息）：学号、姓名、手机号码、班级、系别
      - 档案信息表 （不常用信息）：学号、身份证号码、家庭住址、籍贯、紧急联系人、...
    - 一对一关联建表原则：
      - 外键唯一：主表的外键和从表的主键是唯一的，形成主外键关系，外键唯一
      - 外键是主键：主表的外键是从表的主键，形成主外键关系

  - **一对多关联：**

    - 常见实例场景：客户表和订单表、分类表和商品表、部门表和员工表
    - 举例：【一个部门对应着多个员工，多个员工对应着一个部门】
      - 员工表：编号、姓名、...、所属部门
      - 部门表：编号、名称、简介
    - 一对多建表原则：在从表【多方】【这里指员工表】创建一个字段，字段作为外键指向主表【一方】【这里指部门表】的主键

  - **多对多关联：**

    - 要标识多对多关系，必须创建第三个表，该表通常称为：连接表，它将多对多关系划分为两个一对多关系。将这两个表的主键都插入到第三个表中。

      - 举例1：学生-课程【多个学生选择多门课程】

        - 学生信息表：一行代表一个学生的信息（学号、姓名、手机号码、班级、系别...）
        - 选课信息表 ：一个学生可以选多门课，一门课可以被多个学生选择

        所以需要创建连接表将多对多的关系转换成两个一对多的关系。把两张表的主键取出来形成一张新的表：

        ```sql
        学号 课程编号
         1    1001
         2    1001
         1    1002
        ```

      - 举例2：产品-订单

        订单表和产品表有一种多对多的关系，这种关系是通过与订单明细表建立两个一对多关系定义的。一个订单可以有多个产品，每个产品可以出现在多个订单中。

        - 产品表：“产品”表中的每条记录表示一个产品。
        - 订单表：“订单”表中的每条记录表示一个订单。
        - 订单明细表：每个产品可以与“订单”表中的多条记录对应，即出现在多个订单中。一个订单 可以与“产品”表中的多条记录对应，即包含多个产品。

        ![](https://img-blog.csdnimg.cn/46f75da3560d4aabb8253235a839f1e7.png)

      - 举例3：用户-角色

        多对多关系建表原则：需要创建第三张表，中间表中至少两个字段，这两个字段分别作为外键指向 各自一方的主键。

  - **自我引用`Self Reference`：**

    比如这里有一张员工表：员工编号、姓名、部门编号、主管编号

    | 员工编号 | 姓名 | 部门编号 | 主管编号 |
    | :------: | :--: | :------: | :------: |
    |   101    | 张三 |    30    |   NULL   |
    |   103    | 李四 |    30    |   101    |
    |   104    | 王五 |    30    |   103    |
    |   105    | 赵六 |    45    |   103    |
    |   231    | 朱七 |    45    |   210    |

    可以看到这里员工编号`104`和`105`的员工的主管编号均为：`103`而这里的`103`引用的就是`103`员工编号，这就称为自我引用`Self Reference`。

# `MySQL`简单操作

```sql
# 查看当前 MySQL 版本
mysql --version
# 进入 MySQL
mysql -u root -p
# 查看当前 MySQL 版本
select version();
# 查看 MySQL 中所有数据库
show databaes;
# 创建数据库
create database database_name;
# 使用某个数据库
use database_name;
# 查看某个指定数据库中的所有表
show tables from database_name;
# 在当前数据库中创建新的表
create table 表名称 {
	字段名 数据类型，
	字段名 数据类型
};
# 查看某张表的所有数据
select * from table_name;
# 往某张表中添加一条记录
insert into table_name values(...);
# 查看某张表的创建信息
show create table table_name;
# 查看某数据库的创建信息
show create database database_name;
# 删除当前数据库的某张表
drop table table_name;
# 删除数据库
drop database database_name;
# 查看 MySQL 字符编码配置
show variables like 'character%';
```

# 基本的`SELECT`语句

`SQL`分类

- `DDL`（`Data Definition Languages`）：数据定义语言，这些语句定义了不同的数据库、表、视图、索 引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构。`CREATE DROP ALTER`等。
- `DML`（`Data Manipulation Languages`）：数据操作语言，用于添加、删除、更新和查询数据库记 录，并检查数据完整性。`INSERT DELETE UPDATE SELECT`等。
- `DCL`（`Data Control Languages`）：数据控制语言，用于定义数据库、表、字段、用户的访问权限和 安全级别。`GRANT REVOKE COMMIT ROLLBACK SAVEPOINT`等。

因为查询语句使用地非常频繁，所以很多很查询语句单独拎出来，作为数据查询语言`DQL`。还有单独将`COMMIT ROLLBACK`取出来称为`TCL`【**`Transaction Control Language`事务控制语言**】

`MySQL`在`Windows`环境下大小写是不敏感的，在`Linux`环境下大小写是敏感的。推荐统一采用的书写规范：

- 数据库名、表名、表别名、字段名、字段别名都小写。
- `SQL`关键字、函数名、绑定变量等都大写。

数据导入指令：`source d:\mysqldb.sql`

```sql
select 1;
select 9/3;
select * from users;
select uname from users;
select uname AS 'Name' from users;
select uname 'Name' from users;
select distinct * from users;
# 所有运算符或者列值遇到 NULL 其结果都为 NULL，MySQL 中空值是占用空间的
# 如果表名/记录名/字段名跟保留字数据库系统或常用方法冲突，如果真的相同，可以使用着重号引起来。
select * from `order`;
# 显示表建结构
describe employees; / desc employee;
# 条件语句
select * from users where uname = '张三';
```

```sql
#【题目】
# 1.查询员工12个月的工资总和，并起别名为ANNUAL SALARY
# 2.查询employees表中去除重复的job_id以后的数据
# 3.查询工资大于12000的员工姓名和工资
# 4.查询员工号为176的员工的姓名和部门号
# 5.显示表 departments 的结构，并查询其中的全部数据

# commission_pct 若为 NULL：
# 显示 NULL 值
select employee_id, last_name, salary * 12 "ANNUAL SALARY" from employees;
# NULL 值显示为 0
select employee_id, last_name, salary * 12 * (1 + IFNULL(commission_pct, 0)) "ANNUAL SALARY" from employees;

select DISTINCT job_id from employees;

select last_name, salary from employees where salary > 12000;

select last_name, departname_id from employees where employee_id = 176;

describe departments;
select * from departments;
```

