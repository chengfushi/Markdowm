@[toc]
本文首先介绍SQL的基本情况，然后介绍数据定义、数据更新、数据查询和视图。

# 1.SQL的特点
1. 功能齐全
2. 高度非过程化
3. 面向集合的操作方式
4. 同一种语法结构提供多种使用方式
5. 语言简洁
# 2.SQL的组成
1. 数据定义语言(DDL)
2. 数据查询语言(DQL)
3. 数据操纵语言(DQL)
4. 数据控制语言(DML)
# 3SQL语句
## 3.1数据库的基本操作
1. 创建数据库
```sql
CREATE DATABASE database_name;
```
2. 切换数据库
```sql
USE database_name;
```
3. 修改数据库
```sql
ALTER DATABASE database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
4. 删除数据库
```sql
DROP DATABASE database_name;
```
## 3.2 基本表的定义、修改、删除
1. 基本表的定义
```sql
CREATE TABLE table_name (
    column1 datatype CONSTRAINTS,
    column2 datatype CONSTRAINTS,
    ...
);
```

|数据类型|描述|
|---|---|
|`INT`|用于整数值。|
|`VARCHAR(n)`|可变长度的字符串，最大长度为 `n`。|
|`TEXT`|用于长文本字符串。|
|`DATE`|存储日期值（年、月、日）。|
|`TIME`|存储时间值（时、分、秒）。|
|`DATETIME`|存储日期和时间。|
|`FLOAT`|浮点数，用于存储带小数的数值。|
|`DOUBLE`|双精度浮点数，比`FLOAT`更精确。|
|`BOOLEAN`|存储布尔值 (`TRUE` 或 `FALSE`)。|
|`BLOB`|用于存储二进制数据。|
2. 基本表的增加
```sql
ALTER TABLE table_name ADD column_name datatype;
```
3. 基本表的修改
```sql
ALTER TABLE table_name MODIFY COLUMN column_name new_datatype;
```
4. 基本表的删除
```sql
DROP TABLE table_name;
```
## 3.3索引的建立与删除
1. 建立索引
```sql
CREATE INDEX index_name ON table_name (column1, column2, ...);
```

|索引类型|描述|
|---|---|
|`PRIMARY KEY`|一个表中对应唯一记录的索引，不能包含 NULL 值。通常用于标识表中的唯一记录。|
|`UNIQUE`|索引中的所有值都必须是唯一的，类似于主键，但可以有多个，并且可以接受 NULL 值。|
|`INDEX`|普通索引，用来加速查询过程，无唯一性要求。可以为表中一个或多个列创建索引。|
|`FULLTEXT`|专为全文搜索设计的索引，只适用于某些数据库系统如 MySQL。|
|`SPATIAL`|用于地理数据存储的索引，可以快速处理空间数据查询。仅在支持该功能的数据库中可用。|
|`FOREIGN KEY`|用于建立两个表之间的联系，指向另一个表中的主键。|
2. 删除索引
```sql
DROP INDEX index_name ON table_name;
```
## 3.4数据更新
1. 插入数据
```sql
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);
```
2. 更新数据
```sql
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition;
```
3. 删除数据
```sql
DELETE FROM table_name WHERE condition;
```
## 3.5数据查询
### 3.5.1单表查询

单表查询是数据库查询中最基础的形式，它仅涉及单个数据表。以下是单表查询中常用的SQL语句和子句：

1. 基本的SELECT语句
最基本的单表查询使用`SELECT`语句来选择表中的列。例如，选择`employees`表中的`name`和`age`列：
```sql
SELECT name, age FROM employees;
````

2. 使用WHERE子句进行条件过滤
`WHERE`子句允许你根据条件过滤结果集。例如，选择年龄大于30岁的员工：

```sql
SELECT name, age FROM employees WHERE age > 30;
```

 3. 使用ORDER BY子句进行排序

`ORDER BY`子句可以对结果进行排序，可以是升序（ASC）或降序（DESC）。例如，按年龄升序排列员工：

```sql
SELECT name, age FROM employees ORDER BY age ASC;
```

4. 使用GROUP BY子句进行分组

`GROUP BY`子句可以将结果集按一个或多个列的值进行分组。例如，按部门分组并计算每个部门的员工数量：

```sql
SELECT department, COUNT(*) FROM employees GROUP BY department;
```

5. 使用HAVING子句进行分组后的条件过滤
`HAVING`子句与`WHERE`类似，但它是在分组后对结果集进行条件过滤。例如，选择员工数量超过10人的部门：
```sql
SELECT department, COUNT(*) FROM employees GROUP BY department HAVING COUNT(*) > 10;
```

6. 使用LIMIT子句限制结果数量
`LIMIT`子句用于限制查询结果的数量。例如，选择前5名员工：
```sql
SELECT name, age FROM employees LIMIT 5;
```
7. 使用AS给列指定别名

`AS`关键字允许你给列指定别名。例如，给员工年龄列指定别名为`age_years`：
```sql
SELECT name, age AS age_years FROM employees;
```
### 3.5.2连接查询
连接查询（JOIN）用于结合两个或多个表中的行，这些表之间存在某种关系。最常用的连接类型包括内连接（INNER JOIN）、左连接（LEFT JOIN）、右连接（RIGHT JOIN）和全连接（FULL JOIN）。
#### 3.5.2.1内连接（INNER JOIN）

MySQL中的内连接（INNER JOIN）有几种不同的写法，主要取决于你想要如何表达连接的条件。以下是一些常见的写法：

1. **隐式内连接**（使用WHERE子句）:
  ```sql
   SELECT column_name(s)
   FROM table1, table2
   WHERE table1.column_name = table2.column_name;
  ```
2. **显式内连接**（使用INNER JOIN关键字）:
```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;
    ```
3. **使用USING关键字**（当两个表中有相同名称的列时）:
```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2 USING (column_name);
```
4. **使用ON关键字**（指定连接条件）:
```sql
SELECT column_name(s)
FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;
```
5. **使用别名**（给表起别名以简化查询）:
  ```sql
SELECT a.column_name, b.column_name 
FROM table1 AS a
INNER JOIN table2 AS b ON a.id = b.id;
```
6. **多表内连接**（连接多个表）:
  ```sql
SELECT a.column_name, b.column_name, c.column_name
FROM table1 AS a
INNER JOIN table2 AS b ON a.id = b.id
INNER JOIN table3 AS c ON b.id = c.id;
```

7. **交叉连接**（笛卡尔积，虽然不是内连接，但有时候也会用到）:
  ```sql
SELECT *
FROM table1, table2;
```

#### 3.5.2.2左连接（LEFT JOIN）
1. **使用LEFT JOIN关键字**：
```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;
```
2. **使用别名**：
  ```sql
SELECT a.column_name, b.column_name
FROM table1 AS a
LEFT JOIN table2 AS b ON a.id = b.id;
```
3. **使用USING关键字**（当两个表中有相同名称的列时）：
  ```sql
SELECT column_name(s)
FROM table1
LEFT JOIN table2 USING (column_name);
```

#### 3.5.2.3右连接（RIGHT JOIN）
1. **使用RIGHT JOIN关键字**：
  ```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;
 ```
2. **使用别名**：
```sql
SELECT a.column_name, b.column_name
FROM table1 AS a
RIGHT JOIN table2 AS b ON a.id = b.id;
```
3. **使用USING关键字**（当两个表中有相同名称的列时）：
```sql
SELECT column_name(s)
FROM table1
RIGHT JOIN table2 USING (column_name);
```
4. **多表右连接**（连接多个表）：
```sql
SELECT a.column_name, b.column_name, c.column_name
FROM table1 AS a
RIGHT JOIN table2 AS b ON a.id = b.id
RIGHT JOIN table3 AS c ON b.id = c.id;
```
#### 3.5.2.4全连接（FULL JOIN）
全连接返回两个表中的所有行，无论它们是否匹配。如果一个表中没有匹配的行，则结果中该表的部分将为NULL。
```sql
SELECT columns
FROM table1
FULL JOIN table2
ON table1.column_name = table2.column_name;
```
### 3.5.3嵌套查询
嵌套查询，也称为子查询（Subquery），是嵌套在另一个查询中的SQL查询。它可以出现在SELECT、INSERT、UPDATE或DELETE语句中的任何地方。
1. `IN` 操作符
`IN` 操作符用于指定多个值的列表，子查询返回的值必须匹配列表中的一个值。

```sql
SELECT * FROM employees 
WHERE department_id IN (SELECT department_id FROM departments WHERE location_id = 10);
```

这个查询返回所有在位置ID为10的部门工作的员工。

2. 比较操作符

比较操作符包括`=`, `<>`, `>`, `<`, `>=`, `<=`等，用于比较子查询返回的单个值。
```sql
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);
```

这个查询返回所有薪水高于平均薪水的员工。

3. `ANY` 或 `ALL` 操作符

- `ANY`：如果子查询返回的任意一个值满足比较条件，则条件为真。
- `ALL`：如果子查询返回的所有值都满足比较条件，则条件为真。

```sql
-- ANY
SELECT * FROM employees e1 
WHERE e1.salary > ANY (SELECT salary FROM employees e2 WHERE e2.department_id = e1.department_id);

-- ALL
SELECT * FROM employees e1 
WHERE e1.salary > ALL (SELECT salary FROM employees e2 WHERE e2.department_id = e1.department_id AND e2.salary < 5000);
```

`ANY`的查询返回薪水高于同部门任意员工的员工，而`ALL`的查询返回薪水高于同部门所有低薪水员工的员工。
 4. `EXISTS` 操作符
EXISTS代表存在量词∈，`EXISTS` 操作符用于检查子查询是否返回至少一个行。
```sql
SELECT * FROM employees e1 
WHERE EXISTS (SELECT * FROM orders o WHERE o.employee_id = e1.employee_id);
```
这个查询返回至少有一个订单的员工。

### 3.5.4集合查询
在SQL中，集合查询通常指的是使用集合操作符来组合多个查询结果集。这些操作符包括`UNION`、`INTERSECT`和`EXCEPT`（或在某些数据库系统中使用`MINUS`）。以下是这些操作符的详细解释：
1. `UNION`
`UNION`操作符用于合并两个或多个`SELECT`语句的结果集，去除重复的行。
```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

这个查询返回`table1`和`table2`中所有不同的行。
2. `INTERSECT`
`INTERSECT`操作符用于返回两个或多个`SELECT`语句共有的行。
```sql
SELECT column_name(s) FROM table1
INTERSECT
SELECT column_name(s) FROM table2;
```

这个查询返回同时存在于`table1`和`table2`中的行。

 3. `EXCEPT`（或`MINUS`）

`EXCEPT`操作符用于返回第一个查询中存在而第二个查询中不存在的行。在某些数据库系统中，如Oracle，使用`MINUS`来实现相同的功能。
```sql
SELECT column_name(s) FROM table1
EXCEPT
SELECT column_name(s) FROM table2;
```
或者在Oracle中：
```sql
SELECT column_name(s) FROM table1
MINUS
SELECT column_name(s) FROM table2;
```
 **使用集合操作符的注意事项**
1. **选择列表一致性**：在使用`UNION`、`INTERSECT`或`EXCEPT`时，每个查询的选择列表（即SELECT语句中指定的列）必须具有相同数量的列，并且相应的列数据类型必须兼容。
2. **`UNION`与`UNION ALL`**：`UNION`默认去除重复的行，而`UNION ALL`则保留所有行，包括重复的行。如果确定结果集中不会有重复行，或者你想要保留所有行，使用`UNION ALL`可能会有更好的性能。
3. **性能**：集合操作可能会影响数据库性能，尤其是当处理大量数据时。优化查询和索引可以提高性能。
4. **可读性**：合理使用集合操作符可以使查询更加清晰和易于理解。

## 3.6视图
### 3.6.1视图与基本表的区别
**视图（View）**:
- 是一种虚拟存在的表，其内容由查询定义。
- 视图包含一系列带有名称的列和行数据，但并不在数据库中以存储的数据值集形式存在。
- 行和列数据来自由定义视图的查询所引用的表，并且在引用视图时动态生成。
- 视图不占用物理存储空间，它们是查询结果的可视化表示。
- 可以包含表的全部或部分记录，也可以由一个或多个表创建。
**基本表（Base Table）**:
- 是实际存储数据的表。
- 基本表包含实际的数据行和列，它们存在于数据库的物理存储中。
- 基本表的数据是持久化的，即使数据库关闭，数据也会被保留。
- 基本表可以进行各种数据操作，包括插入、更新、删除和查询。
### 3.6.2视图的优点
1. **简化复杂的SQL操作**：通过将复杂的SQL查询封装为视图，可以简化业务逻辑和提高代码的可维护性 。
2. **安全性**：可以为不同用户定制不同的视图，限制对特定数据的访问，提高安全性 。
3. **逻辑数据独立性**：应用程序可以基于视图而不是基础表进行开发，当基础表结构变化时，可以通过修改视图来保持应用程序的稳定 。
4. **重构数据库提供了一定程度的逻辑独立性**：视图可以在不影响应用程序的情况下对底层数据结构进行修改 。
### 3.6.3创建与删除视图
创建视图的基本语法如下：
```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
如果要替换已存在的视图，可以使用 `OR REPLACE` 选项。

删除视图的语法如下：

```sql
DROP VIEW view_name;
```

使用 `IF EXISTS` 可以避免在视图不存在时出现错误。
### 3.6.4查询视图
查询视图与查询普通表相同，使用 `SELECT` 语句：

```sql
SELECT * FROM view_name;
```
### 3.6.5更新视图
更新视图通常指的是通过视图来插入、更新、删除表中的数据。但需要注意的是，并不是所有视图都可以更新。可更新的视图需要满足一定的条件，例如视图和基础表的行列之间存在一一对应关系。
**更新视图的语法示例**：
```sql
UPDATE view_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```
**插入数据到视图**：

```sql
INSERT INTO view_name (column1, column2, ...)
VALUES (value1, value2, ...);
```
**删除视图中的数据**：
```sql
DELETE FROM view_name
WHERE condition;
```

### 3.6.6注意事项

- 视图的更新操作实际上是对基础表的更新。
- 更新视图时，需要确保更新操作在视图的权限范围内。
- 视图的创建和删除只影响视图本身，不影响对应的基本表 。