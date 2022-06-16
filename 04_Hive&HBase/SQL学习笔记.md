# SQL学习笔记

### SQL专用名词

课程表 `courses`

```sql
+----+-------------------------+---------------+------------+------------+
| id | name                    | student_count | created_at | teacher_id |
+----+-------------------------+---------------+------------+------------+
|  1 | Advanced Algorithms     |           880 | 2020-06-01 |          4 |
|  2 | System Design           |          1350 | 2020-07-18 |          3 |
|  3 | Django                  |           780 | 2020-02-29 |          3 |
|  4 | Web                     |           340 | 2020-04-22 |          4 |
|  5 | Big Data                |           700 | 2020-09-11 |          1 |
|  6 | Artificial Intelligence |          1660 | 2018-05-13 |          3 |
|  7 | Java P6+                |           780 | 2019-01-19 |          3 |
|  8 | Data Analysis           |           500 | 2019-07-12 |          1 |
| 10 | Object Oriented Design  |           300 | 2020-08-08 |          4 |
| 12 | Dynamic Programming     |          2000 | 2018-08-18 |          1 |
+----+-------------------------+---------------+------------+------------+
```

教师表 `teachers`

```sql
+----+------------------+---------------------------+-----+---------+
| id | name             | email                     | age | country |
+----+------------------+---------------------------+-----+---------+
|  1 | Eastern Heretic  | eastern.heretic@gmail.com |  20 | UK      |
|  2 | Northern Beggar  | northern.beggar@qq.com    |  21 | CN      |
|  3 | Western Venom    | western.venom@163.com     |  28 | USA     |
|  4 | Southern Emperor | southern.emperor@qq.com   |  21 | JP      |
|  5 | Linghu Chong     | NULL                      |  18 | CN      |
+----+------------------+---------------------------+-----+---------+
```

- **数据库**：数据库是一些关联表的集合。LintCode 就是我们用来存放课程表 `courses` 和教师表 `teachers` 的数据库。
- **数据表**：数据表是数据的矩阵。课程表 `courses` 和 教师表 `teachers` 就是数据表，看起来像一个简单的 Excel 表格。
- **列：** 一列（数据元素）包含了相同类型的数据。例如教师表 `teachers` 中的姓名 `name` 就是一个列，这一列中的教师姓名都是用字符方式存储。
- **行：** 一行数据（可以称为元组，或记录）是一组相关的数据。例如教师表 `teachers` 每一行都对应一位教师信息

这个名为 `id` 的列，是我们用来唯一标识每行数据的，称为**主键**。实际上，教师的姓名是不能唯一标识一条记录的（因为教师姓名可能会有相同），所以我们使用额外的一列来唯一标识每行。这个列我们一般取名为 `id`，即 identification 的缩写，当然，我们也可以取别的名称。但需要注意的是，**一个数据表只能有一个主键**。

此外，在教师表 `teachers` 中，我们发现有一列数据 `teacher_id` 和教师表 `teachers` 中的 `id` 相关联，即每个 `teacher_id` 都指向教师表中的某一个教师记录，这种用于关联其它表某一列的列，我们称为**外键**（foreign key）。

### 什么是RDBMS？

+ RDBMS 指的是关系型数据库管理系统，全程为Relational Database Management System。
+ RDBMS 是 SQL 的基础，同样也是所有现代数据库系统的基础，比如 MS SQL Server、IBM DB2、Oracle、MySQL 以及 Microsoft Access。
+ RDBMS 中的数据存储再被称为表的数据库对象中。
+ 表是相关的数据项的集合，它由列和行组成。

### SQL语法

+ ##### 使用 SELECT COLUMN 查询单个列

  + SELECT 语句用于从数据库中选取数据，并将结果存储在一个临时结果表中，这个表称为结果集。结果集实际上也是一种包含行与列的表，只不过是未持久化的，即临时表。
  + 但其实我们在命名字段的时候，字段名可能会与 **SQL 关键字冲突**，这时候要用**反引号**将列名和表名包含一下，避免关键字冲突。因此，在本课程所有小节的学习中，都会更加严谨地加上反引号。

+ ##### 使用 SELECT COLUMN, COLUMN 查询多个列

```sql
SELECT `name`, `student_count` FROM `courses`;
```

+ ##### 使用 SELECT * 查询所有列

```sql
SELECT * FROM `courses`;
```

+ ##### 使用 SELECT DISTINCT 查询不同行

  查询的值都是唯一不重复

```sql
SELECT DISTINCT `column_name` FROM `table_name`;
```

+ ##### 使用 SELECT WHERE 对行进行筛选过滤

```sql
SELECT `column_name1`,`column_name2`… FROM `table_name` WHERE `column_name` operator `value`;
```

其中：

- column_name 对应指定列的名称，或者是多列，用逗号（ `,` ）分隔开

- table_name 对应查询表的名称

- operator 为操作符，常用的有等于 `=` 、小于 `<` 、大于 `>` 、不等于`<>` 或 `!=`。

- ##### 使用 INSERT INTO 在不指定列的情况下插入数据

```sql
INSERT INTO `table_name` VALUES (value1, value2, value3,...);
```

+ ##### 使用 INSERT INTO 在指定的列中插入数据

```sql
INSERT INTO `table_name` (`column1`, `column2`, `column3`,...) VALUES (value1, value2, value3,...);
```

+ ##### 使用 UPDATE 更新数据

```sql
UPDATE `table_name` SET `column1`=value1,`column2`=value2,... WHERE `some_column`=some_value;
```

+ **使用 DELETE 删除数据**

```sql
DELETE FROM `table_name` WHERE `some_column` = some_value;
```

+ ### 比较运算符

```sql
SELECT * 
FROM `courses`
WHERE `student_count` > 800;
```

+ ##### 使用 AND 连接多条件

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE condition1 AND condition2;
```

+ ##### 使用 OR 连接多个条件

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE condition1 or condition2;
```

+ ##### 使用 NOT 过滤不满足条件的数据

```sql
SELECT `column_name` 
FROM `table_name` 
WHERE NOT `condition`;
```

+ **使用 IN 查询多条件**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` IN `value`;
```

+ **使用 NOT IN 排除**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` NOT IN value;
```

+ **使用 BETWEEN AND 查询两值间的数据范围**

```sql
SELECT *
FROM `table_name`
WHERE `column_name` BETWEEN `value` AND `value`;
```

+ ##### 使用 IS NULL 查询空数据

```sql
SELECT *
FROM `table_name`
WHERE `column_name` IS NULL;
```

+ **使用 LIKE 模糊查询**

```sql
SELECT *
FROM `courses`
WHERE `name` LIKE 'D%';
```

| 通配符         | 描述                       |
| :------------- | :------------------------- |
| %              | 替代 0 个或多个字符        |
| _              | 替代一个字符               |
| [charlist]     | 字符列中的任何单一字符     |
| 或 [!charlist] | 不在字符列中的任何单一字符 |

**其中 'D%' 表示以 D 开头的所有单词，% 表示为通配符，可以替代 0 个或多个字符**

+ **使用 ORDER BY 对数据进行排序**

```sql
SELECT `column_name`, `column_name`
FROM `table_name`
ORDER BY `column_name`, `column_name` ASC|DESC;

SELECT `name`,`teacher_id`,`created_at`
FROM `courses`
WHERE `teacher_id` in (1,2,3)
ORDER BY `teacher_id`,`created_at`;
```

+ **使用 LIMIT 限制输出行数**

```sql
SELECT `column_name`, `column_name`
FROM `table_name`
LIMIT `offset` , `count`;

SELECT *
FROM `courses`
ORDER BY `student_count`
LIMIT 3;
```

+ ##### 使用 AVG() 函数求数值列的平均值

```sql
SELECT AVG(`column_name`) 
FROM `table_name`;

SELECT AVG(`student_count`) AS `average_student_count`
FROM `courses`;
```

+ **使用 MAX() 函数返回指定列中的最大值**

```sql
SELECT MAX(`column_name`) 
FROM `table_name`;

SELECT MAX(`student_count`) AS max_student_count
FROM `courses`;
```

+ **使用 MIN() 函数返回指定列中的最小值**

```sql
SELECT MIN(`column_name`) 
FROM `table_name`;

SELECT MIN(`student_count`) AS min_student_count
FROM `courses`;
```

+ **使用 SUM() 函数统计数值列的总数**

```sql
SELECT SUM(`column_name`) 
FROM `table_name`;

SELECT SUM(`student_count`) AS `all_student_count`
FROM `courses`;
```

+ **使用 ROUND() 函数将数值四舍五入**

```sql
SELECT ROUND(`column_name`, `decimals`) 
FROM `table_name`;

mysql> SELECT ROUND(1.388, 1);
+-----------------+
| ROUND(1.388, 1) |
+-----------------+
|             1.4 |
+-----------------+
1 row in set (0.00 sec)
```

+ **使用 NULL() 函数判断空值**

  + ISNULL()

  ```sql
  SELECT ISNULL(`column_name`)
  FROM `table_name`;
  ```

  + IFNULL()

  ```sql
  SELECT IFNULL(`column_name`, `value`)
  FROM `table_name`;
  ```

  ```sql
  SELECT `name`, `email`, ISNULL(`email`), IFNULL(`email`, 0), COALESCE(`email`, 0) 
  FROM `teachers`;
  
  mysql> SELECT `name`, `email`, ISNULL(`email`), IFNULL(`email`, 0), COALESCE(`email`, 0) FROM `teachers`;
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  | name             | email                     | ISNULL(email) | IFNULL(email,0)           | COALESCE(email,0)         |
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  | Eastern Heretic  | eastern.heretic@gmail.com |             0 | eastern.heretic@gmail.com | eastern.heretic@gmail.com |
  | Northern Beggar  | northern.beggar@qq.com    |             0 | northern.beggar@qq.com    | northern.beggar@qq.com    |
  | Western Venom    | western.venom@163.com     |             0 | western.venom@163.com     | western.venom@163.com     |
  | Southern Emperor | southern.emperor@qq.com   |             0 | southern.emperor@qq.com   | southern.emperor@qq.com   |
  | Linghu Chong     | NULL                      |             1 | 0                         | 0                         |
  +------------------+---------------------------+---------------+---------------------------+---------------------------+
  5 rows in set (0.00 sec)
  ```

+ **使用 COUNT() 函数计数**

```sql
SELECT COUNT(`column_name`) 
FROM `table_name`;

SELECT COUNT(*) 
FROM `table_name`;

SELECT COUNT(DISTINCT `column_name`) 
FROM `table_name`;
```

❗ 注意： COUNT(column_name) 与 COUNT(*) 的区别

- COUNT(column_name) 中，如果 `column_name` 字段中的值为 NULL，则计数不会增加，而如果字段值为空字符串`""`，则字段值会加 1
- COUNT(\*) 中，除非整个记录全为 NULL，则计数不会增加，如果存在某一个记录不为 NULL，或者为空字符串`""`，计数值都会加 1。正常来说，表都会有主键，而主键不为空，所以 COUNT(*) 在有主键的表中等同于 COUNT(PRIMARY_KEY)，即查询有多少条记录。

+ **使用 NOW() 、 CURDATE()、CURTIME() 获取当前时间**

  + 在 SQL 中，我们可以通过使用 **NOW()**、**CURDATE()**、**CURTIME()** 来获取当前的时间

    - `NOW()` 可以用来返回当前日期和时间 格式：YYYY-MM-DD hh:mm:ss

    - `CURDATE()` 可以用来返回当前日期 格式：YYYY-MM-DD

    - `CURTIME()` 可以用来返回当前时间 格式：hh:mm:ss
      - 在使用 `NOW()` 和 `CURTIME()` 时，如果要精确的秒以后的时间的话，可以在（）中加数字，加多少，就表示精确到秒后多少位，比如 `NOW(3)` 就是精确到毫秒，表示为： `2021-03-31 15:27:20.645`

```sql
mysql> INSERT INTO `records` VALUES (NOW(3));
Query OK, 1 row affected

mysql> SELECT `now_time` FROM `records`;
+-------------------------+
| now_time                |
+-------------------------+
| 2021-03-31 15:31:49.091 |
+-------------------------+
2 rows in set
```

+ **使用 DATE()、TIME() 函数提取日期和时间**

```sql
SELECT DATE('2021-03-25 16:16:30') AS `date`,TIME('2021-03-25 16:16:30')  AS `time`;
+------------+----------+
| date       | time     |
+------------+----------+
| 2021-03-25 | 16:16:30 |
+------------+----------+
1 row in set

mysql> SELECT DATE(NOW()),CURDATE();
+-------------+------------+
| DATE(NOW()) | CURDATE()  |
+-------------+------------+
| 2021-03-25  | 2021-03-25 |
+-------------+------------+
1 row in set

mysql> SELECT `name`, DATE(`created_at`) AS `created_date`
    -> FROM `courses`;
+-------------------------+--------------+
| name                    | created_date |
+-------------------------+--------------+
| Advanced Algorithms     | 2020-06-01   |
| System Design           | 2020-07-18   |
| Django                  | 2020-02-29   |
| Web                     | 2020-04-22   |
| Big Data                | 2020-09-11   |
| Artificial Intelligence | 2018-05-13   |
| Java P6+                | 2019-01-19   |
| Data Analysis           | 2019-07-12   |
| Object Oriented Design  | 2020-08-08   |
| Dynamic Programming     | 2018-08-18   |
+-------------------------+--------------+
10 rows in set

mysql> SELECT `name`, `created_at`, 
    -> DATE_FORMAT(DATE(`created_at`),"%Y-%m-%d") AS `created_date`,
    -> DATE_FORMAT(TIME(`created_at`),"%H:%i:%s") AS `created_time`
    -> FROM `courses`;
+-------------------------+---------------------+--------------+--------------+
| name                    | created_at          | created_date | created_time |
+-------------------------+---------------------+--------------+--------------+
| Advanced Algorithms     | 2020-06-01 09:10:12 | 2020-06-01   | 09:10:12     |
| System Design           | 2020-07-18 10:11:12 | 2020-07-18   | 10:11:12     |
| Django                  | 2020-02-29 12:10:12 | 2020-02-29   | 12:10:12     |
| Web                     | 2020-04-22 13:01:12 | 2020-04-22   | 13:01:12     |
| Big Data                | 2020-09-11 16:01:12 | 2020-09-11   | 16:01:12     |
| Artificial Intelligence | 2018-05-13 18:12:30 | 2018-05-13   | 18:12:30     |
| Java P6+                | 2019-01-19 13:31:12 | 2019-01-19   | 13:31:12     |
| Data Analysis           | 2019-07-12 13:01:12 | 2019-07-12   | 13:01:12     |
| Object Oriented Design  | 2020-08-08 13:01:12 | 2020-08-08   | 13:01:12     |
| Dynamic Programming     | 2018-08-18 20:01:12 | 2018-08-18   | 20:01:12     |
+-------------------------+---------------------+--------------+--------------+
10 rows in set
```

+ **使用 EXTRACT() 函数提取指定的时间信息**

```sql
SELECT EXTRACT(unit FROM date)
FROM `table`

mysql> SELECT `name`, EXTRACT(HOUR FROM `created_at`) AS `created_hour`
FROM `courses`;
+-------------------------+--------------+
| name                    | created_hour |
+-------------------------+--------------+
| Advanced Algorithms     | 9            |
| System Design           | 10           |
| Django                  | 12           |
| Web                     | 13           |
| Big Data                | 16           |
| Artificial Intelligence | 18           |
| Java P6+                | 13           |
| Data Analysis           | 13           |
| Object Oriented Design  | 13           |
| Dynamic Programming     | 20           |
+-------------------------+--------------+
10 row in set
```

+ **使用 DATE_FORMAT() 格式化输出日期**

```sql
SELECT DATE_FORMAT(date,format);

mysql> SELECT DATE_FORMAT(`created_at`, '%Y %m') AS `DATE_FORMAT`
    -> FROM `courses`;
+-------------+
| DATE_FORMAT |
+-------------+
| 2020 06     |
| 2020 07     |
| 2020 02     |
| 2020 04     |
| 2020 09     |
| 2018 05     |
| 2019 01     |
| 2019 07     |
| 2020 08     |
| 2018 08     |
+-------------+
10 rows in set (0.01 sec)
```

其中 %m 表示月份，%d 表示日期，%Y 表示年份，%w 表示星期。

+ **使用 DATE_ADD() 增加时间**

```sql
SELECT DATE_ADD(date, INTERVAL expr type)
FROM table_name

mysql> SELECT `name`, DATE_ADD(`created_at`, INTERVAL 1 YEAR) AS `new_created`
    -> FROM `courses`;
+-------------------------+-------------+
| name                    | new_created |
+-------------------------+-------------+
| Senior Algorithm        | 2020-06-01  |
| System Design           | 2020-07-18  |
| Django                  | 2020-02-29  |
| Web                     | 2020-04-22  |   
| Big Data                | 2020-09-11  |      
| Artificial Intelligence | 2018-05-13  |  
| Java P6+                | 2019-01-19  |    
| Data Analysis           | 2019-07-12  |   
| Object Oriented Design  | 2020-08-08  |   
| Dynamic Programming     | 2018-08-18  |   
+-------------------------+-------------+
10 rows in set (0.00 sec)
```

其中：`date` 指代希望被操作的有效日期，为起始日期

 `expr` 是希望添加的时间间隔的数值（expr 是一个字符串，对于负值的间隔，可以以 ”-“ 开头）

 `type` 是具体的数据类型，表示加上时间间隔的单位（可以是 MICROSECOND , SECOND , MINUTE , HOUR , DAY , WEEK , MONTH , QUARTER , YEAR 等）

+ **使用 DATE_SUB() 减少时间**

```sql
SELECT DATE_SUB(date, INTERVAL expr type)
FROM table_name

mysql> SELECT `id`,`name`, DATE_SUB(`created_at`, INTERVAL 1 MONTH) AS `new_created`
     > FROM `courses`;
+----+-------------------------+-------------+
| id | name                    | created_at  |
+----+-------------------------+-------------+
|  1 | Senior Algorithm        | 2020-06-01  |      
|  2 | System Design           | 2020-07-18  |      
|  3 | Django                  | 2020-02-29  |      
|  4 | Web                     | 2020-04-22  |     
|  5 | Big Data                | 2020-09-11  |          
|  6 | Artificial Intelligence | 2018-05-13  |         
|  7 | Java P6+                | 2019-01-19  |   
|  8 | Data Analysis           | 2019-07-12  |      
| 10 | Object Oriented Design  | 2020-08-08  |      
| 12 | Dynamic Programming     | 2018-08-18  |   
+----+-------------------------+-------------+
10 rows in set (0.00 sec)
```

其中：`date` 指代希望被操作的有效日期

 `expr` 是希望添加的时间间隔

 `type` 是具体的数据类型（可以是 MICROSECOND , SECOND , MINUTE , HOUR , DAY , WEEK , MONTH , QUARTER , YEAR 等）

+ **使用时间函数 DATEDIFF() 和 TIMESTAMPDIFF() 计算日期差**

```sql
SELECT DATEDIFF(时间1,时间2) AS date_diff FROM courses;

SELECT TIMESTAMPDIFF (类型,时间1,时间2) AS year_diff；
```

+ **非空约束 NOT NULL**

```sql
CREATE TABLE `Persons` (
    `ID` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255) NOT NULL,
    `Age` int
);
```

+ **唯一约束 UNIQUE**

**MySQL**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255),
UNIQUE (`P_Id`)
)
```

**SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL UNIQUE,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255)
)
```

命名 UNIQUE 约束，并定义多个列的 UNIQUE 约束:

**MySQL / SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
`P_Id` int NOT NULL,
`LastName` varchar(255) NOT NULL,
`FirstName` varchar(255),
`Address` varchar(255),
`City` varchar(255),
CONSTRAINT uc_PersonID UNIQUE (`P_Id`,`LastName`)
)
```

+ ##### 撤销 UNIQUE 约束

**MySQL**

```sql
ALTER TABLE `Persons`
DROP INDEX uc_PersonID
```

**SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
DROP CONSTRAINT uc_PersonID
```

+ **主键约束 PRIMARY KEY**

  + PRIMARY KEY 约束唯一标识数据库表中的每条记录 ，简单的说，**PRIMARY KEY = UNIQUE + NOT NULL** ,从技术的角度来看，PRIMARY KEY 和 UNIQUE 有很多相似之处。但还是有以下区别：

    - NOT NULL UNIQUE 可以将表的一列或多列定义为唯一性属性，而 PRIMARY KEY 设为多列时，仅能保证多列之和是唯一的，具体到某一列可能会重复。

    - PRIMARY KEY 可以与外键配合，从而形成主从表的关系，而 NOT NULL UNIQUE 则做不到这一点

**MYSQL**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255),
    PRIMARY KEY (`P_Id`)
);
```

执行输出结果

```sql
mysql> CREATE TABLE `Persons`
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255),
    ->     PRIMARY KEY (`P_Id`)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

> 我们通过 desc table_name 查看建立的数据表 `Persons` 可以发现，`P_Id` 的 Key 属性为 PRI ，即设定为 PRIMARY KEY 。

**SQL Server / Oracle / MS Access**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL PRIMARY KEY,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255)
)
```

> 上述语句中 NOT NULL 为非空约束，即输入值必须不为空否则会报错。

**如需命名并定义多个列的 PRIMARY KEY 约束，请使用下面的 SQL 语法：**

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255),
    CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`)
)
```

> 注释：
> 在上面的实例中，只有一个主键 PRIMARY KEY（pk_PersonID）。然而，pk_PersonID 的值是由两个列（`P_Id` 和 `LastName`）组成的。

+ ##### ALTER TABLE 时添加主键约束

当表已被创建时，如需在 `P_Id` 列创建 PRIMARY KEY 约束：

**MySQL / SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
ADD PRIMARY KEY (`P_Id`)
```

执行输出结果

```sql
mysql> CREATE TABLE Persons
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> ALTER TABLE Persons
    -> ADD PRIMARY KEY (P_Id);
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.01 sec)
```

如需命名并定义多个列的 PRIMARY KEY 约束，可以使用下面的 SQL 语法：

**MySQL / SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
ADD CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`)
```

> 注释：
>
> 如果您使用 ALTER TABLE 语句添加主键，必须把主键列声明为不包含 NULL 值（在表首次创建时）。

执行输出结果

```sql
mysql> CREATE TABLE `Persons`
    -> (
    ->     `P_Id` int NOT NULL,
    ->     `LastName` varchar(255) NOT NULL,
    ->     `FirstName` varchar(255),
    ->     `Address` varchar(255),
    ->     `City` varchar(255)
    -> );
Query OK, 0 rows affected (0.04 sec)

mysql> ALTER TABLE `Persons`
    -> ADD CONSTRAINT pk_PersonID PRIMARY KEY (`P_Id`,`LastName`);
Query OK, 0 rows affected (0.08 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   | PRI | NULL    |       |
| LastName  | varchar(255) | NO   | PRI | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.01 sec)
```

+ ##### 撤销 PRIMARY KEY

**MYSQL**

```sql
ALTER TABLE `Persons`
DROP PRIMARY KEY
```

执行输出结果

```sql
mysql> ALTER TABLE `Persons`
    -> DROP PRIMARY KEY;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc Persons;
+-----------+--------------+------+-----+---------+-------+
| Field     | Type         | Null | Key | Default | Extra |
+-----------+--------------+------+-----+---------+-------+
| P_Id      | int(11)      | NO   |     | NULL    |       |
| LastName  | varchar(255) | NO   |     | NULL    |       |
| FirstName | varchar(255) | YES  |     | NULL    |       |
| Address   | varchar(255) | YES  |     | NULL    |       |
| City      | varchar(255) | YES  |     | NULL    |       |
+-----------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

> 通过DESC table_name我们可以发现，Key 列上的约束已经为空了。

**SQL Server / Oracle / MS Access**

```sql
ALTER TABLE `Persons`
DROP CONSTRAINT pk_PersonID
```

+ **外键约束 FOREIGN KEY**

一个表中的 FOREIGN KEY 指向另一个表中的 UNIQUE KEY 。

**MySQL**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL,
`OrderNo` int NOT NULL,
`P_Id` int,
PRIMARY KEY (O_Id),
FOREIGN KEY (P_Id) REFERENCES Persons(P_Id)
)
```

**SQL Server / Oracle / MS Access**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL PRIMARY KEY,
`OrderNo` int NOT NULL,
P_Id int FOREIGN KEY REFERENCES Persons(P_Id)
)
```

> 其中
>
> NOT NULL 表示该字段不为空
> REFERENCES 表示 引用一个表

如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
CREATE TABLE `Orders`
(
`O_Id` int NOT NULL,
`OrderNo` int NOT NULL,
`P_Id` int,
PRIMARY KEY (O_Id),
CONSTRAINT fk_PerOrders FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
)
```

> 其中
>
> CONSTRAINT 表示约束，后面接约束名称，常用于创建约束和删除约束；

+ ##### ALTER TABLE 时的 SQL FOREIGN KEY 约束

当 "Orders" 表已被创建时，如需在 "P_Id" 列创建 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
ADD FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
```

如需命名 FOREIGN KEY 约束，并定义多个列的 FOREIGN KEY 约束:

**MySQL / SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
ADD CONSTRAINT fk_PerOrders
FOREIGN KEY (P_Id)
REFERENCES Persons(P_Id)
```

+ ##### 撤销 FOREIGN KEY 约束

**MySQL**

```SQL
ALTER TABLE `Orders`
DROP FOREIGN KEY fk_PerOrders
```

**SQL Server / Oracle / MS Access**

```SQL
ALTER TABLE `Orders`
DROP CONSTRAINT fk_PerOrders
```

+ **检查约束 CHECK**

  + `CHECK` 约束既可以用于某一列也可以用于某张表：

    - 如果对单个列定义 CHECK 约束，那么该列只允许特定的值。

    - 如果对一个表定义 CHECK 约束，那么此约束会基于行中其他列的值在特定的列中对值进行限制。

**MYSQL：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CHECK (`student_count` > 0)
)
```

**SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int
CHECK (`student_count` > 0),
`name` varchar(255),`student_count` int,
`created_at` date,
`teacher_id` int
)
```

- 为多个列添加 CHECK 约束

**MySQL / SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CHECK (`student_count` > 0 AND `teacher_id` > 0)
)
```

如果想为一个表中多个字段添加约束，直接在 CHECK 关键字后的括号内添加，两个约束间使用 AND 关键字连接。

- 为 CHECK 约束命名

**MySQL / SQL Server / Oracle / MS Access：**

```sql
CREATE TABLE `courses`
(
`id` int,
`name` varchar(255),
`student_count` int,
`created_at` date,
`teacher_id` int,
CONSTRAINT chk_courses CHECK (`student_count` > 0) ;
```

核心部分的基本语法为：

```sql
[CONSTRAINT <constraint name>] CHECK (<condition>)
```

> 其中：
>
> - 约束关键字 CONSTRAINT：表示其后面接的内容为约束
> - constraint name：为约束名称
> - 关键字 CHECK：表示检查约束
> - condition：被约束内容

+  **表已存在时添加 CHECK 约束**

**MySQL / SQL Server / Oracle / MS Access：**

```sql
ALTER TABLE `courses` 
ADD CHECK ( `student_count` > 0);
```

或

```sql
ALTER TABLE `courses`  
ADD CONSTRAINT chk_courses CHECK ( `student_count` > 0 AND `teacher_id` > 0);
```

> - ALTER TABLE 关键字：表示修改表的定义
> - ADD 关键字：表示增加

+ ##### 撤销 CHECK 约束

**MYSQL：**

```sql
ALTER TABLE `courses` 
DROP CHECK chk_courses
```

**SQL Server / Oracle / MS Access：**

```sql
ALTER TABLE `courses` 
DROP CONSTRAINT chk_courses
```

+ **默认约束 DEFAULT**

  + DEFAULT 约束用法

    - DEFAULT 约束用于向列中插入默认值。

    - 如果没有规定其他的值，那么会将默认值添加到所有的新记录。

    - 例如女同学较多，性别就可以默认为“女”，如果插入一条新的记录时没有为这个字段赋值，那么系统会自动为这个字段赋值为“女”。

MYSQL / SQL Server / Oracle / MS Access

```sql
CREATE TABLE `Persons`
(
    `P_Id` int NOT NULL,
    `LastName` varchar(255) NOT NULL,
    `FirstName` varchar(255),
    `Address` varchar(255),
    `City` varchar(255) DEFAULT 'Sandnes'
)
```

👇通过使用类似 GETDATE() 这样的函数, DEFAULT 约束也可以用于插入系统值：

```sql
CREATE TABLE `Orders`
(
    `O_Id` int NOT NULL,
    `OrderNo` int NOT NULL,
    `P_Id` int,
    `OrderDate` date DEFAULT GETDATE()
)
```

+ ##### ALTER TABLE 时的 DEFAULT 约束

MYSQL

```sql
ALTER TABLE `Persons`
ALTER `City` SET DEFAULT 'SANDNES'
```

SQL Server / MS Access：

```sql
ALTER TABLE `Persons`
ADD CONSTRAINT ab_c DEFAULT 'SANDNES' for `City`
```

+ ##### 撤销 DEFAULT 约束

MYSQL:

```sql
ALTER TABLE `Persons`
ALTER `City` DROP DEFAULT
```

SQL Server / Oracle / MS Access：

```sql
ALTER TABLE `Persons`
ALTER COLUMN `City` DROP DEFAULT
```

+ **联结**

### 什么叫联结？

我们可以举一个生活中的例子来理解“联结”：

👇

在大学每一个新学期即将开始的时候，我们都会遇到一个大难题，那就是选课。

假如所有课程的信息和所有教师的信息都存放在同一张表中，我们会发现这显得很繁琐且不便于信息的更改。因为同一个教师开设的不同课程对应的教师信息都是相同的，当教师信息改变时，需要修改多条课程。

因此，为了同学们在选课时操作更便利，我们根据内容将所有数据分解为了两个表，即教师表和课程表。
我们可以将他们称为**关系表**。关系表就是一类数据一个表，各表通过某些关系互相关联。

🤔
那么问题就来了，数据存储在两个表中，如果我们想用一条 SELECT 语句同时查询课程信息和对应的教师信息该怎么办呢？

答案就是——使用**联结**。

简单地说，联结是一种机制，用于在一条 SELECT 语句中关联多个表，返回一组输出。

这个时候就要说一下联结中的两大主角——**主键（PRIMARY KEY）\**和\**外键（FOREIGN KEY）**。

以我们使用的教师表和课程表为例：

**表1：courses (课程表)**

|     列名      |     类型     |     注释     |
| :-----------: | :----------: | :----------: |
|      id       | int unsigned |     主键     |
|     name      |   varchar    |   课程名称   |
| student_count |     int      |   学生总数   |
|  created_at   |     date     | 创建课程时间 |
|  teacher_id   |     int      |   讲师 id    |

**表2：teachers (教师表)**

|  列名   |  类型   |   注释   |
| :-----: | :-----: | :------: |
|   id    |   int   |   主键   |
|  name   | varchar | 讲师姓名 |
|  email  | varchar | 讲师邮箱 |
|   age   |   int   | 讲师年龄 |
| country | varchar | 讲师国籍 |

教师表 `teachers` 中包含所有教师的信息，每个教师具有唯一的标识，这个标识被称为**主键（PRIMARY KEY）**，可以为 id 或其他唯一值。我们这里选择教师编号（`id`）为教师表的主键。

而在课程表 `courses` 中，除了教师编号 `teacher_id`（即教师表的主键）外不存储任何教师的信息。则教师编号为课程表的**外键（FOREIGN KEY）**。

教师表的主键又叫作课程表的外键，因此这两个表通过教师编号这一列关联了。

```sql
`table1`.`common_field` = `table2`.`common_field`
```

+ SQL JOIN 连接子句用于将数据库中两个或者两个以上表中的记录组合起来。其类型主要分为 INNER JOIN（内连接）、OUTER JOIN（外连接）、全连接（FULL JOIN）和交叉连接（CROSS JOIN），其中 OUTER JOIN 又可以细分为 LEFT JOIN（左连接）和 RIGHT JOIN（右连接）。

+ 因此，我们主要使用的 JOIN 连接类型如下：

  - **INNER JOIN**：如果表中有至少一个匹配，则返回行

  - **LEFT JOIN**：即使右表中没有匹配，也从左表返回所有的行

  - **RIGHT JOIN**：即使左表中没有匹配，也从右表返回所有的行

  - **FULL JOIN**：只要其中一个表中存在匹配，则返回行

  - **CROSS JOIN**：又称**笛卡尔积**，两个表数据一一对应，返回结果的行数等于两个表行数的乘积

+ **内连接 INNER JOIN**

### 基本语法

在对 INNER JOIN（内连接）的概念有基本的了解之后，我们再来学习一下它的基本语法。
基本语法有如下两种写法：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
INNER JOIN `table2`
ON `table1`.`common_field` = `table2`.`common_field`;
```

或

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
JOIN `table2`
ON `table1`.`common_field` = `table2`.`common_field`;
```

> 注：INNER JOIN 中 INNER 可以省略不写

其中，语法的核心部分如下所示：

```sql
FROM table1
INNER JOIN table2
ON table1.common_field = table2.common_field
```



请编写 SQL 语句，将课程表 `courses` 和教师表 `teachers` 进行内连接，查询 “Eastern Heretic” 老师所教的所有课程的课程名和课程编号 , 且结果列名分别以课程编号 `id` 、课程名称 `course_name` 和教师姓名 `teacher_name` 显示。

+ **外连接 OUTER JOIN**

在MySQL中，外连接查询会返回所操作的表中至少一个表的所有数据记录。在MySQL中，数据查询通过SQL语句 “OUTER JOIN…ON” 来实现，外连接查询可以分为以下三类：

- 左外连接
- 右外连接
- 全外连接

外连接数据查询语法如下：

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT | RIGHT | FULL  (OUTER) JOIN table2
        ON CONDITION;
```

+ **左外连接 LEFT JOIN**

外连接查询中的左外连接就是指新关系中执行匹配条件时，以关键字 LEFT JOIN 左边的表为参考表。左外连接的结果包括 **LEFT OUTER 子句**中指定的**左表的所有行**，而不仅仅是连接列所匹配的行，这就意味着，左连接会**返回左表中的所有记录**，加上右表中匹配到的记录。如果左表的某行在右表中没有匹配行，那么在相关联的结果行中，右表的所有选择列表均为空值。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT JOIN table2
        ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.name AS teacher_name
     -> FROM teachers t
          LEFT JOIN courses c ON c.teacher_id = t.id;
+------------------------+--------------------+
|  course_name           | teacher_name       |
+------------------------+--------------------+
| Big Data	         |Eastern Heretic     |
| Data Analysis          |Eastern Heretic     |
| Dynamic Programming	 |Eastern Heretic     | 
| NULL	                 |Northern Beggar     |
| System Design          |Western Venom       |
| Django	         |Western Venom       |
| Artificial Intelligence|Western Venom       |
| Java P6+	         |Western Venom       |
| Senior Algorithm	 |Southern Emperor    |
| Web	                 |Southern Emperor    |
| Object Oriented Design |Southern Emperor    |
| NULL	                 |Linghu Chong        |
+------------------------+--------------------+
12 rows in set (0.01 sec)
```

+ ##### 右外连接 RIGHT JOIN

外连接查询中的右外连接是指新关系中执行匹配条件时，以关键字 RIGHT JOIN 右边的表为参考表，如果右表的某行在左表中没有匹配行，左表就返回空值。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        RIGHT JOIN table2
        ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.name AS teacher_name, t.email AS teacher_email
     -> FROM courses c
	     RIGHT JOIN teachers t ON c.teacher_id = t.id;
+------------------------+----------------------+---------------------------+
|  course_name           | teacher_name         | teacher_email             |
+------------------------+----------------------+---------------------------+
| Dynamic Programming	 | Eastern Heretic	| eastern.heretic@gmail.com |
| Data Analysis   	 | Eastern Heretic	| eastern.heretic@gmail.com |
| Big Data   	         | Eastern Heretic	| eastern.heretic@gmail.com |
| Dynamic Programming    | Northern Beggar	| northern.beggar@qq.com    |
| Java P6+	         | Western Venom	| western.venom@163.com     |
| Artificial Intelligence| Western Venom	| western.venom@163.com     |
| Django	         | Western Venom	| western.venom@163.com     |
| System Design	         | Western Venom	| western.venom@163.com     |
| Object Oriented Design | Southern Emperor	| southern.emperor@qq.com   |
| Web	                 | Southern Emperor	| southern.emperor@qq.com   |
| Advanced Algorithms	 | Southern Emperor	| southern.emperor@qq.com   |
| NULL	                 | Linghu Chong	        | NULL                      |
+------------------------+----------------------+---------------------------+
12 rows in set (0.01 sec)
```

+ ##### 全外连接 FULL (OUTER) JOIN

FULL OUTER JOIN 关键字只要左表（table1）和右表（table2）其中一个表中存在匹配，则返回行。FULL OUTER JOIN 关键字结合了 LEFT JOIN 和 RIGHT JOIN 的结果。

> 注：MySQL 数据库不支持全连接，想要实现全连接可以使用 UNION ALL 来将左连接和右连接结果组合在一起实现全连接。

**UNION** ：联合的意思，即把两次或多次查询结果合并起来

要求：两次查询的列数必须一致，同时，每条 SELECT 语句中的列的顺序必须相同

推荐：列的类型可以不一样，但推荐查询的每一列，相对于的类型应该一样

可以来自多张表的数据：多次sql语句取出的列名可以不一致，此时以第一个sql语句的列名为准，即UNION 结果集中的列名总是等于 UNION 中第一个 SELECT 语句中的列名。
如果不同的语句中取出的行，有完全相同(这里表示的是每个列的值都相同)，那么 UNION 会将相同的行合并，最终只保留一行。也可以这样理解，UNION 会去掉重复的行。
如果不想去掉重复的行，可以使用 UNION ALL 。
如果子句中有 order by,limit，需用括号()包起来。推荐放到所有子句之后，即对最终合并的结果来排序或筛选。

```sql
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        LEFT JOIN table2 ON CONDITION 
UNION
SELECT column_name 1,column_name 2 ... column_name n
    FROM table1
        RIGHT JOIN table2 ON CONDITION ;
```

```sql
mysql-> SELECT c.name AS course_name, t.age AS teacher_age
     -> FROM courses c
	    LEFT JOIN teachers t ON c.teacher_id = t.id
     -> UNION
     -> SELECT c.name AS course_name, t.age AS teacher_age
     -> FROM courses c
	    RIGHT JOIN teachers t ON c.teacher_id = t.id;
+------------------------+---------------+
|  course_name           | teacher_age   | 
+------------------------+---------------+
| Advanced Algorithms	 | 21            | 
| System Design	         | 28            | 
| Django                 | 28            | 
| Web Southern	         | 21            |  
| Big Data	         | 20            |  
| Artificial Intelligence| 28            | 
| Java P6+	         | 28            |  
| Data Analysis Eastern	 | 20            |  
| Object Oriented Design | 21            |  
| Dynamic Programming	 | 20            |  
| Linghu Chong 	         | 18            | 
| NULL	                 | 21            |  
| NULL	                 | 18            |  
+------------------------+----------------------+---------------------------+
13 rows in set (0.01 sec)
```

请编写 SQL 语句，将课程表 `courses` 和教师表 `teachers` 进行全外连接，查询所有课程名称以及与其相互对应的教师名称和教师国籍，结果列名请分别以课程名称 `course_name` 、教师名称 `teacher_name` 、教师国籍 `teacher_country` 显示。

+ **交叉连接 CROSS JOIN**

### 什么是交叉连接

与内连接和外连接相比，交叉连接非常简单，因为它不存在 ON 子句，那怎么理解交叉连接呢？

交叉连接：返回左表中的所有行，左表中的每一行与右表中的所有行组合。即将两个表的数据一一对应，其查询结果的行数为左表中的行数乘以右表中的行数。

CROSS JOIN（交叉连接）的结果也称作笛卡尔积，我们来简单了解一下什么是笛卡尔积：

笛卡尔乘积是指在数学中，两个集合 X 和 Y 的笛卡尓积（Cartesian product），又称直积，表示为**X × Y**，第一个对象是 X 的成员而第二个对象是 Y 的所有可能有序对的其中一个成员。

### 交叉连接的两种定义方式

交叉连接有两种定义方式，分为隐式连接和显式连接。两种定义方式的查询结果是相同的。

- 隐式交叉连接：不需要使用 CROSS JOIN 关键字，只要在 SELECT 语句的 FROM 语句后将要进行交叉连接的表名列出即可，这种方式基本上可以被任意数据库系统支持。

基本语法如下：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`,`table2`;
```

假如我们想将课程表和教师表进行隐式的交叉连接，查询该学期所有开课老师和他们开课的课程，我们可以使用下列 SQL 语句：

```sql
SELECT  `courses`.`name` AS `course_name`, `teachers`.`name` AS `teacher_name`
FROM `courses` ,`teachers`;
```

为了编写的便利和简洁，我们一般会给表取别名，如本题中给教师表 `courses` 取别名为 `c`，给教师表`teachers` 取别名为 `t`：

```sql
SELECT  `c`.`name` AS `course_name`, `t`.`name` AS `teacher_name`
FROM `courses` `c`,`teachers` `t`;
```

- 显式交叉连接：与隐式交叉连接的区别就是它使用 CROSS JOIN 关键字，用法与 INNER JOIN 相似。

基本语法如下：

```sql
SELECT `table1`.`column1`, `table2`.`column2`...
FROM `table1`
CROSS JOIN `table2`;
```

使用显式交叉连接来解决上文相同的问题，我们可以使用下列 SQL 语句：

```sql
SELECT  `courses`.`name` AS `course_name`, `teachers`.`name` AS `teacher_name`
FROM `courses` 
CROSS JOIN `teachers`;
```

或

```sql
SELECT  `c`.`name` AS `course_name`, `t`.`name` AS `teacher_name`
FROM `courses` `c`
CROSS JOIN `teachers` `t`;
```

+ **GROUP BY 子句**

```sql
SELECT `column_name`, aggregate_function(`column_name`)
FROM `table_name`
WHERE `column_name` operator value
GROUP BY `column_name`;
```

+ ---请编写 SQL 语句，查询教师表 `teachers` 和课程表 `courses`,统计每个老师教授课程的数量，并将结果按课程数量降序排列，课程数量相同时按教师姓名升序排列，返回老师姓名和课程数量，分别命名为 `teacher_name` 和 `course_count`。

```sql
SELECT t.name as teacher_name, count(c.name) as course_count
FROM teachers t left join courses c 
on t.id = c.teacher_id
group by t.id
order by course_count desc, teacher_name asc;
```

+ **HAVING 子句**

HAVING 子句对于 GROUP BY 子句设置条件的方式其实与 WHERE 子句与 SELECT 的方式类似，语法也相近，但 WHERE 子句搜索条件是在分组操作之前，而 HAVING 则是在之后。

```sql
SELECT   `column_name`, aggregate_function(`column_name`) 
FROM     `table_name` 
WHERE    `column_name` operator value 
GROUP BY `column_name` 
HAVING   aggregate_function(`column_name`) operator value;
```

**现需要结合教师表与课程表，统计不同教师所开课程的学生总数，对于没有任课的老师，学生总数计为 0 ，最后查询学生总数少于 3000 的教师姓名及学生总数 （别名为 `student_count` ），结果按照学生总数升序排列，如果学生总数相同，则按照教师姓名升序排列。**

使用 SQL 中 HAVING 子句查询的方式如下：

```sql
SELECT `T`.`name` AS `name`, IFNULL(SUM(`C`.`student_count`),0) AS `student_count`
FROM `courses` `C` RIGHT JOIN `teachers` `T`
ON `C`.`teacher_id` = `T`.`id`
GROUP BY `T`.`id`
HAVING `student_count` < 3000
ORDER BY `student_count`, `name`;
```

执行输出结果：

```sql
mysql> SELECT `T`.`name`, IFNULL(SUM(`C`.`student_count`),0) AS `student_count`
    -> FROM `courses` `C` 
    -> RIGHT JOIN `teachers` `T` ON `C`.`teacher_id` = `T`.`id`
    -> GROUP BY `T`.`id`
    -> HAVING `student_count` < 3000
    -> ORDER BY `student_count`, `name`;
+------------------+---------------+
| name             | student_count |
+------------------+---------------+
| Linghu Chong     |             0 |
| Northern Beggar  |             0 |
| Southern Emperor |          1520 |
+------------------+---------------+
3 rows in set (0.02 sec)
```

请编写 SQL 语句，从 `teachers` 表中，筛选出同一国家的教师平均年龄大于所有教师平均年龄的国家，并获取这些国家的所有教师信息。

+ **SELECT 语句中的子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR (
    SELECT `column_name(s)`
    FROM `table_name`
);
```

请编写 SQL 语句，查询 `courses` 表和 `teachers` 表，查询 'Big Data' 课程对应的老师姓名。

+ **INSERT 语句中的子查询**

```sql
INSERT INTO `table_name`
	SELECT `colnum_name(s)`
	FROM `table_name`
	[ WHERE VALUE OPERATOR ]
```

请编写 SQL 语句，将教师表 teachers 中年龄大于 20（不包括 20） 的教师的数据复制到与它结构相同的空表 teachers_bkp 表中。

+ **UPDATE 语句中的子查询**

```sql
UPDATE `table_name` 
SET `column_name` = `new_value`
WHERE `column_name` OPERATOR 
   (SELECT `column_name`
   FROM `table_name`
   [WHERE] )
```

请编写 SQL 语句，从 teachers 表中查询教师名字为 Eastern Heretic 的信息，并根据教师 id 将教师 Eastern Heretic 创建的课程名称全部改为 PHP，并将学生总数设为 300 人。

+ **DELETE 语句中的子查询**

```sql
DELETE FROM `table_name`
WHERE `column_name` OPERATOR 
   (SELECT `column_name`
   FROM `table_name`  
   [WHERE] )
```

请编写 SQL 语句，删除教师表 `teachers` 中在 2020 年前（不包括 2020 年）创建过课程的教师。

+ **内联视图子查询**

```sql
SELECT *
FROM (
	SELECT *
	FROM `teachers`
	WHERE `country` = 'USA'
) `T`
WHERE `age` = (
	SELECT MAX(`age`)
	FROM `teachers`
);
```

请编写 SQL 语句，使用**内连接（INNER JOIN）**，连接 `teachers` 表和 `courses` 表，并将查询的课程名称（起别名为 `course_name`）、学生总数、讲师姓名（起别名为 `teacher_name`）信息作为查询表，并从该查询表中选出学生总数最多的信息。

+ **IN 操作符的多行子查询**

```sql
SELECT `column_name`
FROM `table_name`
WHERE `column_name` IN(
    SELECT `column_name`
    FROM `table_name`
    WHERE `column_name` = VALUE
);
```

请编写 SQL 语句，联合教师表和课程表，查询课程表 `courses` 中所有年龄大于 20 岁的教师所教的所有课程的课程名（name）。

+ **ANY 操作符的多行子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR
   ANY(SELECT column_name
   FROM table_name)
```

请编写 SQL 语句，对于 `Southern Emperor` 教师的**所有**课程，从 `courses` 表和 `teachers` 表中查询课程创建时间晚于其中**任意一门**课程创建时间的课程名称（不包含该教师的课程）。

+ **ALL 操作符的多行子查询**

```sql
SELECT `column_name(s)`
FROM `table_name`
WHERE `column_name` OPERATOR
   ALL(SELECT column_name
   FROM table_name)
```

请编写 SQL 语句，查询教师表 `teachers` 和课程表 `courses`，查询最年长的老师所开课程的学生数，最后返回学生数均超过这些课程的课程信息。

+ **多列子查询**

```sql
SELECT `name`, `age`, `country` 
FROM `teachers` 
WHERE (`country`, `age`) IN ( 
        SELECT `country`, MAX(`age`) 
        FROM `teachers` 
        GROUP BY `country` 
);
```

请编写 SQL 语句，从课程表 `courses` 中查询每个教师授课学生人数最高的课程名称 `name` 和上课人数 `student_count`。

+ **HAVING 子句中的子查询**

```sql
SELECT `name`
FROM `teachers`
WHERE `id` IN (
	SELECT `teacher_id`
	FROM `courses`
	GROUP BY `teacher_id`
	HAVING AVG(`student_count`) > (
		SELECT AVG(`student_count`)
		FROM `courses`
	)
);
```

请编写 SQL 语句，连接 `courses` 与 `teachers` 表，统计不同国籍教师所开课程的学生总数，对于没有任课的老师，学生总人数计为 0 。最后查询教师国籍是 'U' 开头且**这个国籍的所有教师**的学生总数在 **2000** 到 **5000** 之间（包括 2000 和 5000）的教师国籍及学生总数 （别名为 `student_count` ），结果按照学生总数降序排列，如果学生总数相同，则按照教师国籍升序排列。

+ MySQL Transaction(事务)

  + 为了理解什么是 MySQL 中的事务，让我们看一下在我们的样本数据库中添加一个新的销售订单的例子。添加一个销售订单的步骤如下所述：

    - 首先，从 `orders` 表中查询最新的销售订单号，并使用下一个销售订单号作为新的销售订单号。
    - 接下来，在 `orders` 表中插入一个新的销售订单。
    - 然后，获得新插入的销售订单号
    - 之后，将新的销售订单项目与销售订单号一起插入到 `orderdetails` 表中
    - 最后，从 `orders` 和 `orderdetails` 表中选择数据，以确认这些变化。

    现在，想象一下，如果上述一个或多个步骤由于某些原因（如表锁定）而失败，销售订单数据会发生什么？例如，如果将订单的项目添加到 `orderdetails` 表中的步骤失败，你将会有一个空的销售订单。

    这就是为什么事务处理要来拯救你。MySQL 事务允许你执行一组 MySQL 操作，以确保数据库从不包含部分操作的结果。在一组操作中，如果其中一个操作失败，就会发生回滚，将数据库恢复到其原始状态。如果没有发生错误，整个语句集就会提交到数据库中。

### 事务

##### MySQL 事务语句

MySQL 事务**允许**你执行**一组 MySQL 操作**，以确保数据库从不包含部分操作的结果。在一组操作中，如果其中一个操作失败，就会发生回滚，将数据库恢复到其原始状态。如果没有发生错误，整个语句集就会提交到数据库中。

+ MySQL 为我们提供了以下重要语句来控制事务：

  - 为了启动一个事务，你使用 `START TRANSACTION` 语句。`BEGIN` 或 `BEGIN WORK` 是 `START TRANSACTION` 的别名。

  - 要提交当前事务并使其变化永久化，你要使用 `COMMIT` 语句。

  - 要回滚当前事务并取消其变化，你可以使用 `ROLLBACK` 语句。

  - 要禁用或启用当前事务的自动提交模式，你可以使用 `SET autocommit` 语句。

```sql
-- 1. start a new transaction
START TRANSACTION;

-- 2. Get the latest order number
SELECT 
    @orderNumber:=MAX(orderNUmber)+1
FROM
    orders;

-- 3. insert a new order for customer 145
INSERT INTO orders(orderNumber,
                   orderDate,
                   requiredDate,
                   shippedDate,
                   status,
                   customerNumber)
VALUES(@orderNumber,
       '2005-05-31',
       '2005-06-10',
       '2005-06-11',
       'In Process',
        145);
        
-- 4. Insert order line items
INSERT INTO orderdetails(orderNumber,
                         productCode,
                         quantityOrdered,
                         priceEach,
                         orderLineNumber)
VALUES(@orderNumber,'S18_1749', 30, '136', 1),
      (@orderNumber,'S18_2248', 50, '55.09', 2); 
      
-- 5. commit changes    
COMMIT;
```

我们要向 `teachers` 表中插入一条 `Xie Xun` 的信息，其年龄为 49 岁，国籍为 `CN`，请补充 SQL 语句，来实现插入 `Xie Xun` 的信息。

我们要向 `teachers` 表中插入一条 `Kansas` 的信息，其年龄为 41 岁，国籍为 `UK`，请补充 SQL 语句，来实现插入 `Kansas` 的信息。

## 锁

#### 锁的解释

锁：计算机协调多个进程或线程并发访问某一资源的机制。

#### 锁的重要性

在数据库中，除了传统的计算资源（CPU、RAM、I\O等）的争抢，数据也是一种供多用户共享的资源。

因此，如何保证数据并发访问的一致性，有效性，是所有数据库必须要解决的问题。
锁冲突也是影响数据库并发访问性能的一个重要因素，因此锁对数据库尤其重要。

### 锁的缺点

加锁是消耗资源的，锁的各种操作，包括：获得锁、检测锁是否已经解除、释放锁等，都会增加系统的开销。

```sql
SHOW VARIABLES LIKE '%isolation%'
```

### **InnoDB 锁的种类之自增锁**

```sql
show variables like '%innodb_autoinc_lock_mode%';
```

- **0**：`traditonal` 每次 `insert` 语句执行都会产生表锁
- **1**：`consecutive simple insert` 会获得批量的锁，保证一批插入自增序列的连续性，插入之前提前释放锁，在这个模式下你会发现当你 `insert` 多条数据回滚的时候虽然 DB 没有插入数据，但是自增 ID 已经增长了，也是数据库默认的级别
- **2**：`interleaved` 不会锁表，实时插入，并发最高，但是基于主从的复制是不安全的，感兴趣可以去查询 RBR 和 SBR 的优缺点

通过查询知道，我们的数据库是开启 `consecutive` 级别，`simple insert` 表示通过分析 `insert` 语句可以确定插入的数量；如：`insert, insert, insert … valies(), values(), values()；`相应的其他插入方式还有 `Bulk inserts,Mixed-mode inserts` 等。
官方原话如下

> An AUTO-INC lock is a special table-level lock taken by transactions inserting into tables with AUTO_INCREMENT columns. In the simplest case, if one transaction is inserting values into the table, any other transactions must wait to do their own inserts into that table, so that rows inserted by the first transaction receive consecutive primary key values.

自增锁是一种特殊的表级别锁（table-level lock），专门针对事务插入 `AUTO_INCREMENT` 类型的列。最简单的情况，如果一个事务正在往表中插入记录，所有其他事务的插入必须等待，以便第一个事务插入的行，是连续的主键值。
