

### MySql基本指令

mysql -u root -h() -p 其中 -u之后（root）为用户名,-p为密码（如果在第一行输入则为明文密码，若-p之后enter则在第二行输入密文密码）

show databases 显示数据库

use (数据库名）切换为选择的数据库

show tables 显示选择的数据库下的表

select * from （表名）查看表名下的所有字段

select (列名）from （表名） 查看表中的特定列的字段

create database (库名） 创建库

#### **create table (表名）**

（

（列名） 数据类型(数据长度) （int/varchar ...)

）                                向表中添加列

#### **insert into **

**插入字段 （两种方式）**

1.省略列名，只提供插入值

INSERT INTO table_name
VALUES (value1,value2,value3,...);

2.指定列名及被插入的值

INSERT INTO table_name (column1,column2,column3,...)
VALUES (value1,value2,value3,...);

#### **where       **

**表示特定条件**

e.g.:select table_name from information_schema.tables where table_schema='()'.

#### **关于information_schema表**

保存关于MySQL服务器所维护的所有其他数据库的信息。如数据库名，数据库的表，表栏的数据类型与访问权 限等。

schemata：获取所有数据库信息

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404091558142.png)

tables:提供了关于数据库中的表的信息（包括视图）。详细表述了某个表属于哪个schema，表类型，表引擎，创建时间等信息。是show tables from schemaname的结果取之此表。

columns：提供了表中的列信息。详细表述了某张表的所有列以及每个列的信息。是show columns from schemaname.tablename的结果取之此表



#需要注意的是information_schema内的均为只读，本质上是视图，因此无法调用有关文件



#### and/or  

与C语言类似，and限制两个条件同时成立,or限制两个条件里只要有一个成立即可输出字段。

举例：

SELECT * FROM (table_name)

WHERE （column_name1)='A'

AND/OR   (column_name2)='B';

*在一个条件语句内嵌套and和or也是可行的

#### order by 

用于对结果集按照一个列或者多个列进行排序（默认升序）

举例：

SELECT * FROM （table_name)

ORDER BY （column_name)   （DESC )     //DESC的作用是降序排列

*SQL注入联合查询中可使用order by N,因为order by为以表的第N列为基准排序，当N大于表的最大列数的时候因为N不存在会报错。通过这一原理可以确定待查表的列数。

文章：https://blog.csdn.net/qq_44159028/article/details/114325805

#### update 

更新已有字段的内容

举例：

UPDATE (table_name)

SET(column_name1)='A',(column_name2)='B',

WHERE(column_name i)='i';

其中WHERE语句是对已有字段的限定；如果省略WHERE则会导致某一列的所有数据变为修改的值。在 MySQL 中可以通过设置 **sql_safe_updates** 这个自带的参数来解决，当该参数开启的情况下，你必须在update 语句后携带 where 条件，否则就会报错。

```sql
set sql_safe_updates=1;
```

#### delete 

删除表内字段

举例：

DELETE FROM （table_name)
WHERE (column_name1)='A';

*若只保留第一行则会删除该表内的所有内容，该过程不可逆，但表的列名及其他性质不会受到影响。

#### limit 

限制条件选取

SELECT */(column_name)

FROM (table_name)

LIMIT   n;

当limit后只有一个数字的时候，表示从表头开始选取n个数据；若有两个数字m,n,则表示从第n+1个数据开始选取m个数据；

有关Limit的sql注入:

http://t.zoukankan.com/wangtanzhi-p-12591529.html

https://blog.csdn.net/m0_46467017/article/details/126242055

#### like

用于在where等子语句中添加更加严格的字段筛选条件

```c
SELECT （column_name）or *
FROM （table_name）
WHERE （column_name） LIKE pattern;
```

Like后面的限定条件需要配合通配符使用，例如'%A'表示以A结尾，'A%'表示以A开头，'%AA%'表示字段内含有AA；

#### 通配符

Mysql只支持通配符'%'和'_'，这两个通配符与Like一起使用；

%：替代一个或多个字符；_：替换一个字符；

也可以通过 REGEXP 或 NOT REGEXP来操控正则表达式；

详细规则：https://www.runoob.com/sql/sql-wildcards.html

#### As

```c
SELECT （column_name）FROM （table_name） AS ’‘;
```

可以通过该指令将一个别名赋给列，也可以通过此方式合并多个列的信息到一个列中。

#### Join

通过选取两个列名将不同的表的字段合在一起。

```c
SELECT （column_name）
FROM （table1）
JOIN （table2）
ON table1.column_name=table2.column_name;
```

Join的类型

1.Inner：在表中存在至少一个匹配时返回行，即语法规定的关系必须有至少一组两个字段符合条件，否则不返回值；

2.Left ：从table1返回所有字段，当table2中没有符合条件的字段时返回null；

3.Right：与Left规则相反；

4.Full：取tableA和B的并集输出，但full join在mysql中并不支持；

#### Union

合并多个SELECT语句的结果。

*需要注意的是：每个语句选取的字段必须有相同数量的列，数据类型必须相似，每个语句中列的顺序需要相同。结果输出不会有重复的值（当两个或多个表中有相同的字段），需要输出重复的值可以使用UNION ALL语句。







