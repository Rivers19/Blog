# SQL学习笔记



## MySQL in 3 hours

## 1.课程素材

#### 1.1视频地址

<iframe width="560" height="315" src="//player.bilibili.com/player.html?aid=61947066&cid=107705286&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

#### 1.2课程所用代码

[代码下载](http://bit.ly/2LNdvCd)



#### 1.3软件安装

- [MySQL官网](https://www.mysql.com/)

- Mac版本下载地址：
    - [MySQL Community Server](https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.18-macos10.14-x86_64.dmg)
    - [MySQL Workbench](https://cdn.mysql.com//Downloads/MySQLGUITools/mysql-workbench-community-8.0.18-macos-x86_64.dmg)



## 2.课程内容

### 2.1创建数据库

- 在MySQL Workbench中打开create_database.sql 并执行该程序



### 2.2简易使用示例

> MySQL中用--作为注释符号

- SELECT * 返回所有记录

``` sql
USE sql_store;

SELECT first_name, last_name, points, points*10 AS 'points * 10' 
FROM customers
```



```sql
SELECT * 
FROM sql_store.customers
WHERE birth_date > '1990-01-01' OR points > '800' 	AND state = 'VA'
-- 等同于
-- WHERE birth_date > '1990-01-01' OR (points > '800' 	AND state = 'VA')
-- AND OR 优先级更高
```



#### NOT

```sql
SELECT * 
FROM sql_store.customers
WHERE NOT birth_date > '1990-01-01' OR points > '800'	
-- 等同于
-- WHERE (birth_date <= '1990-01-01' AND points <= '800')
```



#### IN

```sql
SELECT * 
FROM sql_store.customers
WHERE   state IN ('VA', 'FL', 'CA')

--等价于
SELECT * 
FROM sql_store.customers
WHERE state = 'VA' OR state = 'FL' OR state = 'CA'
```



#### NOT IN 

```sql
SELECT * 
FROM sql_store.customers
WHERE   state NOT IN ('VA', 'FL', 'CA')
```



#### BETWEEN

```sql
SELECT * 
FROM sql_store.customers
WHERE points BETWEEN 1000 AND 3000

SELECT * 
FROM sql_store.customers
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```



#### LIKE

```sql
SELECT * 
FROM sql_store.customers
WHERE first_name LIKE '%ed%'
```

- 通配符：

| 通配符                         | 描述                       |
| ------------------------------ | -------------------------- |
| _                              | 替代一个字符               |
| [*charlist*]                   | 字符列中的任何单一字符     |
| [^*charlist*] 或 [!*charlist*] | 不在字符列中的任何单一字符 |
| %                              | 替代 0 个或多个字符        |

> SQL 中，通配符与 SQL LIKE 操作符一起使用。
>
> 不过，MySQL 、SQLite 只支持 **%** 和 **_** 通配符，**不支持**  **[^charlist]** 或 **[!charlist]** 通配符（ MS Access 支持，微软 office 对通配符一直支持良好，但微软有时候的通配符不支持 **%**，而是 *****，具体看对应软件说明）。通配符和正则不是一回事。
>
> MySQL 和 SQLite 会把 **like '[xxx]yyy'** 的中括号当成普通字符，而不是通配符。
>
> 比如：
>
> ```
> select * from persons WHERE City LIKE '[b]eijing'
> ```
>
> 将查出 **city** 为 **[B]eijing** 的行，而不是 **city** 为 **beijing** 的行。
>
> MySQL 中要完成 **[^charlist]** 或 **[!charlist]** 通配符的查询效果，需要通过 **正则表达式** 来完成。
>
> select * from persons WHERE City REGEXP '[b]eijing' SQLite不支持Regexp正则方法。

#### REGEXP

- 1.正则表达式：包含field

```sql
WHERE last_name REGEXP 'field'
-- 等价于
WHERE last_name LIKE '%field%'
```



- 2.正则表达式：以field开头

```sql
WHERE last_name REGEXP '^field'
-- 等价于
WHERE last_name LIKE 'field%'
```



- 3.正则表达式：以field结尾

```sql
WHERE last_name REGEXP 'field$'
-- 等价于
WHERE last_name LIKE '%field'
```

- 4.正则表达式：包含mac 或 age 或 by

```SQL
WHERE last_name REGEXP 'mac|age|by'

-- 包含以mac开头 或 包含age 或 包含by
-- WHERE last_name REGEXP '^mac|age|by'
```

- 5.正则表达式：包含ge 或 me 或 ie

```sql
WHERE last_name REGEXP '[g,m,i]e'
```

