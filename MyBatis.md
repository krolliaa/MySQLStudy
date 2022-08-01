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

# 运算符

## 算术运算符

### 加法与减法运算符

- 一个整数类型的值对整数进行加法和减法操作，结果还是一个整数；
-  一个整数类型的值对浮点数进行加法和减法操作，结果是一个浮点数；
- 加法和减法的优先级相同，进行先加后减操作与进行先减后加操作的结果是一样的；
- 在`Java`中，`+`的左右两边如果有字符串，那么表示字符串的拼接。但是在`MySQL`中`+`只表示数值相加。如果遇到非数值类型，先尝试转成数值，如果转失败，就按`0`计算。（补充：`MySQL`中字符串拼接要使用字符串函数`CONCAT()`实现）

### 乘法与除法运算符

- 一个数乘以或者除以整数`1`得到的都是原数；
- 一个数乘以浮点数1和除以浮点数1后变成浮点数，数值与原数相等；

- 一个数除以整数后，不管是否能除尽，结果都为一个浮点数；
- 一个数除以另一个数，除不尽时，结果为一个浮点数，并保留到小数点后4位；
- 乘法和除法的优先级相同，进行先乘后除操作与先除后乘操作，得出的结果相同。 在数学运算中，`0`不能用作除数，在`MySQL`中，一个数除以`0`为`NULL`。

## 比较运算符

### 等号运算符`=`

- 相等返回`1`不相等返回`0`，用于判断等号两边的值、字符串或表达式是否相等。
- 在使用等号运算符时，遵循如下规则：
  - 如果等号两边的值、字符串或表达式都为字符串，则`MySQL`会按照字符串进行比较，其比较的是每个字符串中字符的`ANSI`编码是否相等。
  - 如果等号两边的值都是整数，则`MySQL`会按照整数来比较两个值的大小。
  -  如果等号两边的值一个是整数，另一个是字符串，则`MySQL`会将字符串转化为数字进行比较。
  -  如果等号两边的值、字符串或表达式中有一个为`NULL`，则比较结果为`NULL`。

### 安全等号运算符`<=>`

- 使用安全等号运算符的时候只有一点跟使用等号运算符有差别，那就是如果比较的有一方为`NULL`，则返回的是`0`或者`1`，比如：`'' <=> NULL`返回的就是`1`。

### 不等于运算符`<>`以及`!=`

- 不等于运算符`（<>和!=）`用于判断两边的数字、字符串或者表达式的值是否不相等， 如果不相等则返回`1`，相等则返回`0`。不等于运算符不能判断`NULL`值。如果两边的值有任意一个为`NULL`， 或两边都为`NULL`，则结果为`NULL`。 

## 非符号类运算符

- `IS NULL`：为空运算符，判断一个值、字符串或表达式是否为空
- `IS NOT NULL`：不为空运算符，判断值、字符串或表达式是否不为空
- `LEAST`：最小值运算符，在多个值中返回最小值
- `GREATEST`：最大值运算符，在多个值中返回最大值
- `BETWEEN AND`：两者之间的运算符，判断一个值是否存在两个值之间
- `IN`：属于运算符，判断一个值是否为列表中的任意一个值
- `NOT IN`：不属于运算符，判断一个值是否不是一个列表中的任意一个值
- `LIKE`：模糊匹配运算符，判断一个值是否符合模糊匹配规则
- `REGEXP`：正则表达式运算符，判断一个值是否符合正则表达式的规则
- `RLIKE`：正则表达式运算符，判断一个值是否符合正则表达式的规则

## 逻辑运算符

- `NOT`或者`!`：逻辑非
- `AND`或者`&&`：逻辑与
- `OR`或者`||`：逻辑或
- `XOR`：逻辑异或【相同为`0`相反为`1`】

注意：`OR`可以和`AND`一起使用，但是在使用的时候需要注意两者的优先级，由于`AND`的优先级高于`OR`，因此先对`AND`两边的操作数进行操作，再与`OR`中的操作数结合。

## 位运算符

【二进制数据】

- `&`：按位与`AND`
- `|`：按位或`OR`
- `^`：按位`XOR`
- `~`：按位取反
- `>>`：按位右移
- `<<`：按位左移

扩展：正则表达式运算

1. 查询以特定字符或字符串开头的记录：

   ```sql
   select * from fruits where f_name REGEXP '^b';
   ```

2. 查询以特定字符串或字符串结尾的记录：

   ```sql
   select * from fruits where f_name REGEXP 'y$';
   ```

3. 用符号`.`来替代字符串中的任意一个字符

   ```sql
   select * from fruits where f_namw REGEXP 'a.g';
   ```

4. 使用`*`和`+`来匹配多个字符，前者表示匹配任意多次包括`0`次，后者表示匹配字符至少一次。

   在`fruits`表中，查询`f_name`字段值以字母`b`开头且`b`后面出现字母`a`至少一次的记录：

   ```sql
   select * from fruits where f_name where REGEXP '^ba+';
   ```

5. 匹配指定字符串， 正则表达式可以匹配指定字符串，只要这个字符串在查询文本中即可，如要匹配多个字符串，多个字符串之间使用分隔符`|`隔开。

   `LIKE`运算符也可以匹配指定的字符串，但与`REGEXP`不同，`LIKE`匹配的字符串如果在文本中间出现，则找不到它，相应的行也不会返回。`REGEXP`在文本内进行匹配，如果被匹配的字符串在文本中出现，`REGEXP`将会找到它，相应的行也会被返回。

   ```sql
   select * from fruits where f_name REGEXP 'on|ap';
   ```

6. 匹配指定字符中的任意一个

   在fruits表中，查找f_name字段中包含字母‘o’或者‘t’的记录：

   ```sql
   select * from fruits where f_name REGEXP '[ot]';
   ```

   在fruits表中，查询s_id字段中包含4、5或者6的记录：

   ```sql
   select * from fruits where s_id REGEXP '[456]';
   ```

7. 匹配指定字符以外的字符`[^字符集合]`表示匹配不在指定集合中的任何字符

   ```sql
   select * from fruits where f_id REGEXP '[a-e1-2]';
   ```

8. 使用`字符串{n,}`或者`字符串{n,m}`来指定字符串连续出现的次数。字符串`{n,}`表示至少匹配`n`次前面的字符串，`m`表示不多于也就是最多出现几次。

   在fruits表中，查询`f_name`字段值出现字母`x`至少`2`次的记录：

   ```sql
   select * from fruits where f_name REGEXP 'x{2,}';
   ```

   在`fruits`表中，查询`f_name`字段值出现字符串`ba`最少`1`次、最多`3`次的记录：

   ```sql
   select * from fruits where f_name REGEXP 'ba{1,3}';
   ```

练习题：

```sql
# 1.选择工资不在5000到12000的员工的姓名和工资
# 2.选择在20或50号部门工作的员工姓名和部门号
# 3.选择公司中没有管理者的员工姓名及job_id
# 4.选择公司中有奖金的员工姓名，工资和奖金级别
# 5.选择员工姓名的第三个字母是a的员工姓名
# 6.选择姓名中有字母a和k的员工姓名
# 7.显示出表 employees 表中 first_name 以 'e'结尾的员工信息
# 8.显示出表 employees 部门编号在 80-100 之间的姓名、工种
# 9.显示出表 employees 的 manager_id 是 100,101,110 的员工姓名、工资、管理者id

select last_name, salary from employees where salary < 5000 OR salary > 12000;
select last_name, salary from employees where salary not between 5000 and 12000;

select last_name, department_id from employees where department_id in (20, 50);

select last_name, job_id from employees where manager_id is null;

select last_name, salary, commission_pct from employees where commission_pct is not null;

select last_name from employees where last_name like '__a%';
select last_name from employees where last_name REGEXP '..a';

select last_name from employees where last_name like '%a%k%' or last_name like '%k%a%';

select employee_id,first_name,last_name from employees where first_name like '%e';
select employee_id,first_name,last_name from employees where first_name REGEXP 'e$';

select last_name, job_id from employees where department_id between 80 and 100;

select last_name,salary,manager_id from employees where manager_id in (100, 101, 110);
```

# 排序与分页

- `order by ... asc/desc`

- `limit [位置偏移量，行数]`

  ```sql
  # 显示前 10 条记录：
  select * from table_name limit 0, 10;
  
  # 显示第 11 - 20 条记录：
  select * from table_name limit 10, 10;
  
  # 显示第 21 - 30 条记录：
  select * from table_name limit 20, 30;
  ```

  在`MySQL 8.0`中还可以使用：`LIMIT 3 OFFSET 4`其本质等于`limit 4, 3`从第`5`条记录开始查询查询`3`条记录。

  注：`LIMIT`子句一定要放在整个`SELECT`语句的最后面！

- 分页显示公式：`limit (当前页数 - 1) * 每页条数, 每页条数`

  起始条：`（当前页数 - 1） * 每页条数`

```sql
#1. 查询员工的姓名和部门号和年薪，按年薪降序,按姓名升序显示
#2. 选择工资不在 8000 到 17000 的员工的姓名和工资，按工资降序，显示第21到40位置的数据
#3. 查询邮箱中包含 e 的员工信息，并先按邮箱的字节数降序，再按部门号升序

select last_name,department_id,salary * 12 annual_sal from employees order by annual_sal DESC, last_name ASC;

select last_name, salary from employees where salary not between 8000 and 17000 order by salary desc limit 20, 20;

select last_name, email, department_id from employees where email like '%e%' order by LENGTH(email) DESC, department_id ASC;
select last_name, email, department_id from employees where email REGEXP '[e]' order by LENGTH(email) DESC, department_id ASC;
```

