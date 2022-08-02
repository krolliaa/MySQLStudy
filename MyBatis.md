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

# 多表查询

多表查询，也称为关联查询，指两个或更多个表一起完成查询操作。

前提条件：这些一起查询的表之间是有关系的（一对一、一对多），它们之间一定是有关联字段，这个 关联字段可能建立了外键，也可能没有建立外键。比如：`员工表`和`部门表`，这两个表依靠`部门编号`进行关联。

笛卡尔乘积是一个数学运算。假设我有两个集合`X`和`Y`，那么`X`和`Y`的笛卡尔积就是`X`和`Y`的所有可能 组合，也就是第一个对象来自于`X`，第二个对象来自于`Y`的所有可能。组合的个数即为两个集合中元素个数的乘积数。

`SQL92`中，笛卡尔积也称为交叉连接 ，英文是`CROSS JOIN`。在`SQL99`中也是使用`CROSS JOIN`表示交叉连接。它的作用就是可以把任意表进行连接，即使这两张表不相关。在`MySQL`中如下情况会出现笛卡尔积：

```sql
SELECT last_name,department_name FROM employees,departments;

SELECT last_name,department_name FROM employees CROSS JOIN departments;

SELECT last_name,department_name FROM employees INNER JOIN departments;

SELECT last_name,department_name FROM employees JOIN departments;
```

因此，为了避免产生笛卡尔积，应该加入有效的连接条件。

```sql
select table1.column, table2.column from table1, table2 where table1.column = table2.column;

SELECT last_name, department_name
FROM employees, departments
WHERE employees.department_id = departments.department_id;
```

## 等值连接`vs`非等值连接

```sql
SELECT departments.location_id FROM employees, departments WHERE employees.department_id = departments.department_id;
```

等值连接区分重复列名：

```sql
SELECT employees.last_name, departments.department_name,employees.department_id
FROM employees, departments
WHERE employees.department_id = departments.department_id;
```

等值连接给表取别名：

```sql
SELECT e.employee_id, e.last_name, e.department_id,
d.department_id, d.location_id
FROM employees e , departments d
WHERE e.department_id = d.department_id;
```

需要注意的是，如果我们使用了表的别名，在查询字段中、过滤条件中就只能使用别名进行代替， 不能使用原有的表名，否则就会报错。

`1052 字段名重复异常：Column 'name' in field list is ambiguous`

连接多个表：

```sql
SELECT e.last_name, e.salary, j.grade_level
FROM employees e, job_grades j
WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal;
```

## 自连接`vs`非自连接

```sql
SELECT CONCAT(worker.last_name ,' works for '
, manager.last_name)
FROM employees worker, employees manager
WHERE worker.manager_id = manager.employee_id ;
```

- 内连接: 合并具有同一列的两个以上的表的行, 结果集中不包含一个表与另一个表不匹配的行
- 外连接: 两个表在连接过程中除了返回满足连接条件的行以外还返回左（或右）表中不满足条件的 行 ，这种连接称为左（或右） 外连接。
- 没有匹配的行时, 结果表中相应的列为空(NULL)。 
- 如果是左外连接，则连接条件中左边的表也称为`主表` ，右边的表称为`从表`。
-  如果是右外连接，则连接条件中右边的表也称为`主表`，左边的表称为`从表`。

- 在`SQL92`中采用`（+）`代表从表所在的位置。即左或右外连接中，`(+)`表示哪个是从表。

- `Oracle`对`SQL92`支持较好，而`MySQL` 则不支持`SQL92`的外连接。

- 而且在`SQL92`中，只有左外连接和右外连接，没有满（或全）外连接。

  ```sql
  #左外连接
  SELECT last_name,department_name
  FROM employees ,departments
  WHERE employees.department_id = departments.department_id(+);
  
  #右外连接
  SELECT last_name,department_name
  FROM employees ,departments
  WHERE employees.department_id(+) = departments.department_id;
  ```

`SQL99`也可以做多表查询，而且可读性比`SQL92`要高。

- 可以使用`ON`子句指定额外的连接条件。
- 这个连接条件是与其它条件分开的。
- `ON`子句使语句具有更高的易读性。
- 关键字`JOIN、INNER JOIN、CROSS JOIN`的含义是一样的，都表示内连接

## 内连接和外连接

内连接的实现：`JOIN / INNER JOIN / CROSS JOIN`

```sql
SELECT e.employee_id, e.last_name, e.department_id,
d.department_id, d.location_id
FROM employees e JOIN departments d
ON (e.department_id = d.department_id);
```

左外连接的实现：`LEFT JOIN / LEFT OUTER JOIN`

```sql
SELECT e.last_name, e.department_id, d.department_name
FROM employees e
LEFT OUTER JOIN departments d
ON (e.department_id = d.department_id);
```

右外连接的实现：`RIGHT JOIN / RIGHT OUTER JOIN`

```sql
ELECT e.last_name, e.department_id, d.department_name
FROM employees e
RIGHT OUTER JOIN departments d
ON (e.department_id = d.department_id);
```

需要注意的是，`LEFT JOIN`和`RIGHT JOIN`只存在于`SQL99`及以后的标准中，在`SQL92`中不存在， 只能用` (+)`表示。

满外连接：【`MySLQ`不支持满外连接但是可以使用`LEFT JOIN UNION RIGHT JOIN`代替】

- **满外连接的结果 = 左右表匹配的数据 + 左表没有匹配到的数据 + 右表没有匹配到的数据。**
- `SQL99`是支持满外连接的。使用`FULL JOIN`或`FULL OUTER JOIN`来实现。 需要注意的是，`MySQL`不支持`FULL JOIN`，但是可以用`LEFT JOIN UNION RIGHT JOIN`代替。

## `UNION`

合并查询结果利用`UNION`关键字，可以给出多条`SELECT`语句，并将它们的结果组合成单个结果集。**合并时，两个表对应的列数和数据类型必须相同**，并且相互对应。各个`SELECT`语句之间使用`UNION`或`UNION ALL`关键字分隔。

**`UNION`操作符返回两个查询的结果集的并集，去除重复记录。**

**`UNION ALL`操作符返回两个查询的结果集的并集。对于两个结果集的重复部分，不去重。**

查询部门编号`>90`或邮箱包含`a`的员工信息：

```sql
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;

SELECT * FROM employees WHERE email LIKE '%a%'
UNION
SELECT * FROM employees WHERE department_id>90;
```

查询中国用户中男性的信息以及美国用户中年男性的用户信息：

```sql
SELECT id,cname FROM t_chinamale WHERE csex='男'
UNION ALL
SELECT id,tname FROM t_usmale WHERE tGender='male';
```

## 总结：七种`SQL JOINS`的实现

![](https://img-blog.csdnimg.cn/8b385fa0194b4e22a05b8a3839ee1c17.png)

1. 中图：内连接

   ```sql
   select employee_id, last_name, department_name from employees e INNER JOIN departments d on e.department_id = d.department_id;
   ```

2. 左上图：左外连接

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e LEFT JOIN departments d ON e.`department_id` = d.`department_id`;
   ```

3. 右上图：右外连接

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e RIGHT JOIN departments d ON e.`department_id` = d.`department_id`;
   ```

4. 左中图：左外连接 - 内连接

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e LEFT JOIN departments d ON e.`department_id` = d.`department_id` where d.`department_id` IS NULL;
   ```

5. 右中图：右外连接 - 内连接

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e RIGHT JOIN departments d ON e.`department_id` = d.`department_id` where e.`department_id` IS NULL;
   ```

6. 左下图：左上图 + 右中图

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e LEFT JOIN departments d ON e.`department_id` = d.`department_id`;
   UNION ALL
   SELECT employee_id,last_name,department_name FROM employees e RIGHT JOIN departments d ON e.`department_id` = d.`department_id` where e.`department_id` IS NULL;
   ```

7. 右下图：左中图 + 右中图

   ```sql
   SELECT employee_id,last_name,department_name FROM employees e LEFT JOIN departments d ON e.`department_id` = d.`department_id` where d.`department_id` IS NULL;
   UNION ALL
   SELECT employee_id,last_name,department_name FROM employees e RIGHT JOIN departments d ON e.`department_id` = d.`department_id` where e.`department_id` IS NULL;
   ```

## `SQL99`语法新特性

自然连接：

`SQL99`在`SQL92`的基础上提供了一些特殊语法，比如`NATURAL JOIN`用来表示自然连接。我们可以把自然连接理解为`SQL92`中的等值连接。它会帮你自动查询两张连接表中所有相同的字段 ，然后进行等值连接 。

```sql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
ON e.`department_id` = d.`department_id`
AND e.`manager_id` = d.`manager_id`;
```

可以写成：

```sql
SELECT employee_id,last_name,department_name
FROM employees e NATURAL JOIN departments d;
```

`USING`连接：

当我们进行连接的时候，`SQL99`还支持使用`USING`指定数据表里的同名字段进行等值连接。但是只能配合`JOIN`一起使用。比如：

```sql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
USING (department_id);
```

## 注意事项

我们要控制连接表的数量 。多表连接就相当于嵌套`for`循环一样，非常消耗资源，会让`SQL`查询性能下降得很严重，因此不要连接不必要的表。

在许多`DBMS`中，也都会有最大连接表的限制。 

- 【强制】超过三个表禁止`join`。需要`join`的字段，数据类型保持绝对一致；多表关联查询时， 保证被关联的字段需要有索引。
-  说明：即使双表`join`也要注意表索引、`SQL`性能。

## 练习

```sql
# 1.显示所有员工的姓名，部门号和部门名称。
# 2.查询90号部门员工的job_id和90号部门的location_id
# 3.选择所有有奖金的员工的 last_name , department_name , location_id , city
# 4.选择city在Toronto工作的员工的 last_name , job_id , department_id , department_name
# 5.查询员工所在的部门名称、部门地址、姓名、工作、工资，其中员工所在部门的部门名称为’Executive’
# 6.选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似于下面的格式
	employees Emp# manager Mgr#
	kochhar 101 king 100
# 7.查询哪些部门没有员工
# 8. 查询哪个城市没有部门
# 9. 查询部门名为 Sales 或 IT 的员工信息

select last_name, e.department_id, department_name from employee e left outer join departments d on e.department_id = d.department_id;

SELECT job_id, location_id
FROM employees e, departments d
WHERE e.`department_id` = d.`department_id`
AND e.`department_id` = 90;
----------------------------------------------------------------------------------------SELECT job_id, location_id
FROM employees e
JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE e.`department_id` = 90;

SELECT last_name , department_name , d.location_id , city
FROM employees e
LEFT OUTER JOIN departments d
ON e.`department_id` = d.`department_id`
LEFT OUTER JOIN locations l
ON d.`location_id` = l.`location_id`
WHERE commission_pct IS NOT NULL;

SELECT last_name , job_id , e.department_id , department_name
FROM employees e, departments d, locations l
WHERE e.`department_id` = d.`department_id`
AND d.`location_id` = l.`location_id`
AND city = 'Toronto';
----------------------------------------------------------------------------------------
SELECT last_name , job_id , e.department_id , department_name
FROM employees e
JOIN departments d
ON e.`department_id` = d.`department_id`
JOIN locations l
ON l.`location_id` = d.`location_id`
WHERE l.`city` = 'Toronto';

SELECT department_name, street_address, last_name, job_id, salary
FROM employees e JOIN departments d
ON e.department_id = d.department_id
JOIN locations l
ON d.`location_id` = l.`location_id`
WHERE department_name = 'Executive'

SELECT emp.last_name employees, emp.employee_id "Emp#", mgr.last_name manager,
mgr.employee_id "Mgr#"
FROM employees emp
LEFT OUTER JOIN employees mgr
ON emp.manager_id = mgr.employee_id

SELECT d.department_id
FROM departments d LEFT JOIN employees e
ON e.department_id = d.`department_id`
WHERE e.department_id IS NULL
----------------------------------------------------------------------------------------
SELECT department_id
FROM departments d
WHERE NOT EXISTS (SELECT * FROM employees e WHERE e.`department_id` = d.`department_id`)

SELECT l.location_id,l.city
FROM locations l LEFT JOIN departments d
ON l.`location_id` = d.`location_id`
WHERE d.`location_id` IS NULL

SELECT employee_id,last_name,department_name
FROM employees e,departments d
WHERE e.department_id = d.`department_id`
AND d.`department_name` IN ('Sales','IT');
```

```sql
#1.所有有门派的人员信息
select * from t_emp a, t_dept b where a.deptId = b.id;
select * from t_emp a inner join t_dept b on a.deptId = b.id;
#2.列出所有用户，并显示其机构信息
select * from t_emp a left outer join t_dept b on a.deptId = b.id;
#3.列出所有门派
select * from t_dept b;
#4.所有不入门派的人员
select * from t_emp a left outer join t_dept b on a.deptId = b.id where b.deptName is null;
#5.所有没人入的门派
select * from t_emp a right outer join t_dept b on a.deptId = b.id where a.`deptId` is null;
#6.列出所有人员和机构的对照关系
select * from t_emp a left outer join t_dept b on a.deptId = b.id
union all
select * from t_emp a right outer join t_dept b on a.deptId = b.id where a.`deptId` is null;
#或者
select * from t_emp a left outer join t_dept b on a.deptId = b.id
union
select * from t_emp a right outer join t_dept b on a.deptId = b.id
#7.列出所有没入派的人员和没人入的门派
select * from t_emp a left outer join t_dept b on a.deptId = b.id where b.`id` is null
union all
select * from t_emp a right outer join t_dept b on a.deptId = b.id where a.`deptId` is null
```

# 单行函数

函数的作用是什么呢？它可以把我们经常使用的代码封装起来， 需要的时候直接调用即可。这样既 提高了代码效率 ，又 提高了可维护性 。在`SQL`中我们也可以使用函数 对检索出来的数据进行函数操作。使用这些函数，可以极大地 提高用户对数据库的管理效率 。

从函数定义的角度出发，我们可以将函数分成`内置函数`和`自定义函数`。在`SQL`语言中，同样也包括了内置函数和自定义函数。内置函数是系统内置的通用函数，而自定义函数是我们根据自己的需要编写的。

我们在使用`SQL`语言的时候，不是直接和这门语言打交道，而是通过它使用不同的数据库软件，即`DBMS`。`DBMS`之间的差异性很大，远大于同一个语言不同版本之间的差异。实际上，只有很少的函数是被`DBMS`同时支持的。比如，大多数`DBMS`使用`（||）`或者`（+）`来做拼接符，而在`MySQL`中的字符串拼 接函数为`concat()`。大部分`DBMS`会有自己特定的函数，这就意味着采用`SQL`函数的代码可移植性是很差的，因此在使用函数的时候需要特别注意。

`MySQL`提供的内置函数从 实现的功能角度 可以分为数值函数、字符串函数、日期和时间函数、流程控制函数、加密与解密函数、获取`MySQL`信息函数、聚合函数等。

内置函数可以简单的分为：`单行函数`、`聚合函数【分组函数】`

单行函数：

- 操作数据对象
- 接受参数返回一个结果
- 只对一行进行变换
- 每行返回一个结果
- 可以嵌套
- 参数可以是一列或一个值

| 函数                    | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `ABS(x)`                | 返回x的绝对值                                                |
| `SIGN(X)`               | 返回X的符号。正数返回1，负数返回-1，0返回0                   |
| `PI()`                  | 返回圆周率的值                                               |
| `CEIL(x)，CEILING(x) `  | 返回大于或等于某个值的最小整数                               |
| `FLOOR(x) `             | 返回小于或等于某个值的最大整数                               |
| `LEAST(e1,e2,e3…) `     | 返回列表中的最小值                                           |
| `GREATEST(e1,e2,e3…)  ` | 返回列表中的最大值                                           |
| `MOD(x,y) `             | 返回X除以Y后的余数                                           |
| `RAND() `               | 返回0~1的随机值                                              |
| `RAND(x) `              | 返回0~1的随机值，其中x的值用作种子值，相同的X值会产生相同的随机 数 |
| `ROUND(x) `             | 返回一个对x的值进行四舍五入后，最接近于X的整数               |
| `ROUND(x,y)`            | 返回一个对x的值进行四舍五入后最接近X的值，并保留到小数点后面Y位 |
| `TRUNCATE(x,y) `        | 返回数字x截断为y位小数的结果                                 |
| `SQRT(x) `              | 返回x的平方根。当X的值为负数时，返回NULL                     |
| `RADIANS(x) `           | 将角度转化为弧度，其中，参数x为角度值                        |
| `DEGREES(x)`            | 将弧度转化为角度，其中，参数x为弧度值                        |

等等，内置函数太多了，这里只得省略。

练习：

```sql
# 1.显示系统时间(注：日期+时间)
select now();
select now() from dual;
# 2.查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
select employee_id, last_name, salary, salary * 1.2 "new salary" from employeesl
# 3.将员工的姓名按首字母排序，并写出姓名的长度（length）
select last_name, length(last_name) from employees order by last_name desc;
# 4.查询员工id,last_name,salary，并作为一个列输出，别名为OUT_PUT
select concat(employee_id, ',', last_name, ',', salary) OUT_PUT from employees;
# 5.查询公司各员工工作的年数、工作的天数，并按工作年数的降序排序
select datediff(sysdate(), hire_date) / 365 worked_years, datediff(sysdate(), hire_date) worled_days from employees order by worked_years desc;
# 6.查询员工姓名，hire_date , department_id，满足以下条件：雇用时间在1997年之后，department_id为80 或 90 或110, commission_pct不为空
select last_name, hire_date, department_id from employees where hire_date >= '1997-01-01' and department_id in (80, 90, 110) and commission_pct is not null;
select last_name, hire_date, department_id from employees where hire_date >= str_to_date('1997-01-01', "%Y-%m-%d") and department_id in (80, 90, 110) and commission_pct is not null;
select last_name, hire_date, department_id from employees where DATE_FORMAT(hire_date, %Y) >= '1997' and department_id in (80, 90, 110) and commission_pct is not null;
# 7.查询公司中入职超过10000天的员工姓名、入职时间
select last_name,hire_date from employees where DATE_FORMAT(hire_date, %D) > 10000;
select last_name,hire_date from employees where （TO_DAYS(NOEW()) - to_days(hire_date)） > 1000
select last_name,hire_date from employees where  DATEDIFF(NOEW(), hire_date) > 10000;
# 8.做一个查询，产生下面的结果
<last_name> earns <salary> monthly but wants <salary*3>
select concat(last_name, 'earns', truncate(salary, 0), 'monthly but wants', truncate(salary * 3, 0), 'Dream Salary') from employees;
# 9.使用CASE-WHEN，按照下面的条件：
-- job grade
-- AD_PRES A
-- ST_MAN B
-- IT_PROG C
-- SA_REP D
-- ST_CLERK E
-- 产生下面的结果
-- Last_name Job_id Grade
-- king AD_PRES A
select  last_name Last_name, job_id Job_id,
CASE job_id
WHEN 'AD_PRES' THEN 'A'
WHEN 'ST_MAN' THEN 'B'
WHEN 'IT_PROG' THEN 'C'
WHEN 'SA_REP' THEN 'D'
WHEN 'ST_CLERK' THEN 'E'
END 'grade'
from employees;
```

- 计算天数直接使用：`DATEDIFF(NOW(), hire_date)`也可以使用`TO_DAYS(NOW()) - TO_DAYS(hire_date);`
- `CASE WHEN THEN END`简单使用
- 截取小数点多少位使用：`TRUNCATE(salary, 0)`\
- 格式化日期可以使用：`DATE_FORMAT()`，其中`%i`代表分钟

# 聚合函数

聚合函数作用于一组数据，并对一组数据返回一个值。它是对 一组数据进行汇总的函数，输入的是一组数据的集合，输出的是单个值。

聚合函数类型：`AVG() SUM() MAX() MIN() COUNT()`

## `AVG()`函数

可以对数值类型的数据使用`AVG()`函数，意思是求平均数。

```sql
select AVG(salry) from employees where job_id like '%REP';
```

## `SUM()`函数

可以对数值类型的数据使用`SUM()`函数，意思是求和。

```sql
select SUM(salry) from employees where job_id like '%REP';
```

## `MIN()`函数

可以对任意数据类型的数据使用`MIN()`函数，意思是求最小值。

```sql
select MIN(salry) from employees;
```

## `MAX()`函数

可以对任意数据类型的数据使用`MAX()`函数，意思是求最大值。

```sql
select MAX(salry) from employees;
```

## `COUNT()`函数

可以对任意数据类型的数据使用`COUNT()`函数，意思是求记录总数。

`count(*)`跟`count(1)`的作用是一样的，都是读取行数。不排除有`NULL`字段的记录。

```sql
select COUNT(*) from employees;
```

还可以返回某字段不为空的记录总数：

```sql
select COUNT(commission_pct) from employees;
```

## `GROUP BY`分组

该函数可以将数据分为若干组，可以结合聚合函数使用。`where`一定要放在`from`后面。比如这里按照不同部门进行分组：

```sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id ;
```

- **<font color="red">在`SELECT`列表中所有未包含在组函数中的列都应该包含在`GROUP BY`子句中</font>**

- 使用`WITH ROLLUP`关键字之后，在所有查询出的分组记录之后增加一条记录，该记录计算查询出的所 有记录的总和，即统计记录数量。

  ```sql
  SELECT department_id,AVG(salary)
  FROM employees
  WHERE department_id > 80
  GROUP BY department_id WITH ROLLUP;
  ```

  注意：当使用`ROLLUP`时，不能同时使用`ORDER BY`子句进行结果排序，即`ROLLUP`和`ORDER BY`是互相排斥 的。

## `HAVING`分组条件

- 必须跟`GORUP BY()`一起使用
- 满足`HAVING`子句中条件的分组将被显示
- `HAVING()`函数就是专门为分组并且使用了聚合函数的记录所服务的，没有分组就没有`HAVING()`
- 你的条件中有聚合函数那么你就必须使用`HAVING()`，是不允许在`where`中带聚合函数的条件的，有`HAVING()`你就必须得有分组。

```sql
SELECT department_id, MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary)>10000 ;
```

`HAVING`跟`WHERE`的区别：

1. `HAVING`必须配合`GROUP BY`使用，可以把分组计算的函数和分组字段作为筛选条件，但是`WHERE`不可以。
2. `WHERE`是先筛选后连接而`HAVING`是先连接后筛选。

总的来说：普通条件用`WHERE`，分组条件用`HAVING`

## `SELECT`执行过程

```sql
SELECT ...,....,...
FROM ... JOIN ...
ON 多表的连接条件
JOIN ...
ON ...
WHERE 不包含组函数的过滤条件
AND/OR 不包含组函数的过滤条件
GROUP BY ...,...
HAVING 包含组函数的过滤条件
ORDER BY ... ASC/DESC
LIMIT ...,...

# 1.from:从哪些表中筛选
# 2.on:关联多表查询时，去除笛卡尔积
# 3.where:从表中筛选的条件
# 4.group by:分组依据
# 5.having:在统计结果中再次筛选
# 6.select 的字段
# 7.distinct：去重
# 8.order by:排序
# 9.limit：分页
```

关键字顺序不能改：

```sql
select ... join ... on ... join ... on ... from ... where ... and ... or ... group by ... having ... order by ... desc/asc ... limit ...
```

执行顺序不能改：

```sql
from ---> on ---> where ---> group by ---> having ---> select 的字段 ---> distinct 去重 ---> order by ---> limit 
```

在`SELECT`语句执行这些步骤的时候，每个步骤都会产生一个 虚拟表 ，然后将这个虚拟表传入下一个步骤中作为输入。需要注意的是，这些步骤隐含在 `SQL`的执行过程中，对于我们来说是不可见的。

## `SELECT`执行原理

`SELECT`是先执行`FROM`这一步骤的。在这个阶段如果是多张表联查，还会经历如下几个步骤：

1. 首先通过`JOIN/CORSS JOIN/INNER JOIN`获取笛卡尔积，相当于得到一张虚拟表`vt 1-1`
2. 然后通过`ON`进行筛选，得到虚拟表`vt 1-2`
3. 紧接着添加外部行。如果使用外连接，不管是左连接还是右连接还是满连接，只要涉及到连接就会涉及到外部行，在第二章虚拟表中增添外部行得到虚拟表`vt 1-3`

重复以上三步直到所有表被处理完位置，获取到原始数据。

3. 接着进入到`GROUP BY`阶段在第三张虚拟表的基础上进行分组得到虚拟表`vt 3`

4. 在第四张虚拟表中使用`HAVING`进行条件筛选得到第五张虚拟表`vt 4`

5. 在第四张虚拟表中就可以筛选出提取的字段即进入到`SELECT DISTINCT`阶段得到`vt 5-1`和`vt 5-2`。

6. 接着就可以对获取到的字段数据进行排序进入到`ORDER BY`阶段得到虚拟表`vt 6`

7. 最后在第六张虚拟表的基础上可以取出指定行的记录即`LIMIT`分页阶段，获取到第七张虚拟表`vt 7`

【这里就获取到的表大概率是不止七张的这样讲只不过是便于理解，因为在连接阶段就可能产生更多的虚拟表】

**<FONT COLOR="RED">所谓`SQL`底层运行的原理，就是我们刚才讲到的执行顺序。</FONT>**
