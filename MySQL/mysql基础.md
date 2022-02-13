## 第01章_数据库概述

### 1.为什么要使用数据库

* 持久化(persistence)：把数据保存到可掉电式存储设备中以供之后使用，数据持久化意味着将内存中的数据保存到硬盘上加以固化，而持久化的实现过程大多通过各种关系数据库来完成。
* 持久化的主要作用是将内存中的数据存储到关系型的数据库当中，当然也可以存到磁盘文件、xml数据文件中。

### 2.各种术语

#### 2.1数据库(DB)

数据库是长期存储在计算机内、有组织的、可共享的大量数据的集合（文件）。

#### 实例：

MySQL数据库有后台线程以及一个共享内存区组成。共享内存可以被运行的后台线程所共享。需要牢记的是，数据库实例才是真正用于操作数据库文件的。

MySQL被设计为一个单进程多线程架构的数据库，也就是说，MySQL数据库实例在系统上表现就是一个进程

#### 2.2数据库管理系统(DBMS)

是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。

DBMS和操作系统一样是计算机的基础软件，它的主要功能有：数据定义功能(DDL)、数据组织、存储和管理、数据操作功能(DML)、数据库的事务管理和运行管理、数据库的建立和维护等。

#### 2.3数据库系统(DBS)

数据库**系统**是一个大的系统，是由数据库、DBMS、应用程序和数据库管理员(DBA)组成的存储、管理、处理和维护数据的系统。

#### 2.4DBMS和DB的关系

数据库管理系统(DBMS)可以管理多个数据库，一般开发人员会针对每一个应用创建一个数据库。为保存应用中实体的数据，一般会在数据库创建多个表，以保存程序中实体用户的数据。

### 3.MySQL

#### 3.5为什么选择MySQL

1. 开放源代码，使用成本低。

2. 性能卓越，服务稳定。

3. 软件体积小，使用简单，并且易于维护。

4. 历史悠久，社区用户非常活跃，遇到问题可以寻求帮助。

5. 许多互联网公司在用，经过了时间的验证。
6. 支持多种操作系统，提供多个API接口，支持多个开发语言，特别对流行的PHP语言有很好的支持。

### 4.RDBMS和非RDBMS

#### 4.1RDBMS

关系型数据库模型是把复杂的数据结构归结为简单的二元关系（即二维表格形式）。

关系型数据库的优势：

* 复杂查询：可以用SQL语句方便的在一个表以及多个表之间做非常复杂的数据查询。
* 事务支持：使得对于安全性能很高的数据访问要求得以实现。

#### 4.2非RDBMS

非关系型数据库基于键值对存储，不需要经过SQL层的解析，性能非常高，还减少了不常用的功能，进一步提升性能。

##### 有哪些非关系型数据库：

**键值型数据库：**

键值型数据库通过 Key-Value 键值的方式来存储数据，其中 Key 和 Value 可以是简单的对象，也可以是复的对象。Key 作为唯一的标识符，**优点是查找速度快，在这方面明显优于关系型数据库，缺点是无法像关系型数据库一样使用条件过滤（比如 WHERE）**，如果你不知道去哪里找数据，就要遍历所有的键，这就会消耗大量的计算。

键值型数据库典型的应用场景是**内存缓存**，**Redis**是最流行的键值型数据库。

**文档型数据库：**

此类数据库可存放并获取文档，可以是XML、JSON等格式。在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录。文档数据库所存放的文档，就相当于键值数据库所存放的“值”。

**MongoDB**是最流行的文档型数据库。

**搜索引擎数据库：**

虽然关系型数据库采用了**索引提升检索效率**，但是针对**全文索引效率却较低**。搜索引擎数据库是应用在搜索引擎领域的数据存储形式，由于搜索引擎会爬取大量的数据，并以特定的格式进行存储，这样在检索的时候才能保证性能最优。核心原理是“倒排索引”。

典型产品：**Elasticsearch**

**列式数据库：HBase**

**图形数据库**

## 第03章_基本的Select语句

### 1.SQL概述

#### 1.3 SQL 分类

SQL在功能上主要分为三大类：

* DDL(Data Defination Languages 数据定义语言)：这些语句定义了不同的数据库、表、视图、索引等数据库对象，还可以用来创建、删除、修改数据库和数据表的结构。
  * 主要语句关键字：create、drop、alter
* DML(Data Manipulation Language 数据操作语言)：用于添加、删除、更新和查询数据库记录，**并检查数据完整性。**
  * 主要语句关键字：insert、delete、update、select
  * select是SQL语言的基础，最为重要。
* DCL(Data Control Language 数据控制语言)：用于定义数据库、表、字段、用户的访问权限和安全级别。
  * 主要语句的关键字：grant、revoke、commit、rollback、savepoint等

> 因为查询语句使用的非常的频繁，所以很多人把查询语句单拎出来一类：**DQL（数据查询语言）。**
>
> 还有单独将 COMMIT 、 ROLLBACK 取出来称为**TCL** （Transaction Control Language，事务控制语言）。

### 2.SQL语言的规则和规范

#### 2.1基本规则

* 每条命令可以使用；或者\g或\G结束，\g的效果和；一样。\G可以在字段很多的时候更好的显示。

![image-20211119192529855](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119192529855.png)

![image-20211119192542730](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119192542730.png)

* 关于标点符号：
  * 字符串和日期时间类型的数据可以使用单引号（‘’）表示
  * 列的别名，尽量使用双引号("")，而且不建议省略as

#### 2.2 SQL大小写规范（建议）

* MySQL在Windows环境下是大小写不敏感的
* **MySQL在Linux环境下大小写敏感**
  * 数据库名、表名、表的别名、变量名是严格区分大小写
  * 关键字、函数名、列名（别名）是忽略大小写的

#### 2.3 注释

```sql
单行注释：#注释文字(MySQL特有的方式) 
单行注释：-- 注释文字(--后面必须包含一个空格。) 
多行注释：/* 注释文字 */
```

#### 2.4 命名规则

- 数据库、表名不得超过30个字符，变量名限制为29个
- 必须只能包含 A–Z, a–z, 0–9, _共63个字符（空格也算字符的话，好像可以有空格，使用``符号引起来，表文件的文件名使用字符的十六进制表示）

![image-20211119194202207](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119194202207.png)

![image-20211119194149400](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119194149400.png)

- 数据库名、表名、字段名等对象名中间不要包含空格（建议，但是可以有空格）

![image-20211119193617793](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119193617793.png)

- 同一个MySQL软件中，数据库不能同名；同一个库中，表不能重名；同一个表中，字段不能重名
- 必须保证你的字段没有和保留字、数据库系统或常用方法冲突。如果坚持使用，请在SQL语句中使用`（着重号）引起来
- 保持字段名和类型的一致性，在命名字段并为其指定数据类型的时候一定要保证一致性。假如数据类型在一个表里是整数，那在另一个表里可就别变成字符型了

#### 2.5 数据导出、导入指令

使用mysqldump命令可以导出mysql数据库。

![image-20211119200357436](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119200357436.png)

![image-20211119200517897](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119200517897.png)

使用source导入数据库文件。

![image-20211119200320378](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119200320378.png)

### 3.基本的SELECT语句

#### 3.1 select。。。from

```sql
select 1

select `column_name`|*
from `table_name`
```

> 一般情况下，除非需要使用表中所有的字段数据，最好不要使用通配符‘*’。使用通配符虽然可以节省输入查询语句的时间，但是获取不需要的列数据通常会降低查询和所使用的应用程序的效率。**通配符的优势是，当不知道所需要的列的名称时，可以通过它获取它们。**
>
> 在生产环境下，不推荐你直接使用 SELECT * 进行查询。

#### 3.2 列的别名

紧跟列名，也可以在列名和别名之间加入关键字AS，别名使用双引号，以便在别名中包含空格或特殊的字符并区分大小写。

AS可以省略。

#### 3.3 去除重复行

默认情况下，查询会返回全部行，包括重复行。

![image-20211119201607317](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119201607317.png)

```sql 
# distinct 一列
select distinct name from account;
select count(distinct name) from account;
select distinct count(name) from account;
```

![image-20211119201700472](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119201700472.png)

![image-20211120103313542](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120103313542.png)

```sql
# distinct 多列
select distinct name,id from account;
```

![image-20211119201921507](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119201921507.png)

**在这里要注意：**

1. DISTINCT 需要放到所有列名的前面

![image-20211119202022299](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119202022299.png)

2. distinct其实是对后面所有列名的组合去重

#### 3.4 空值参与运算

所有的运算符或者列值遇到null值，运算的结果都为null。

**注意**：在Mysql中空值不等于空字符串，就像Java中null和""不一样一样。而且在MySQL中空值是占用空间的。

#### 3.5 MySQL中的空值和NULL

空值是不占用空间的，MySQL中的NULL其实是占用空间的。空值就是空字符串。NULL就是没有任何的设置。

MySQL在做统计的时候会忽略NULL，但是不会忽略空值"";

![image-20211119204528479](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119204528479.png)

判断是否为NULL需要使用is null 或者 is not null，不能使用逻辑判断，因为NULL的所有比较运算都为UNknow，算术运算为null。where和having子句只会把结果为true的数据选出来。

![image-20211119211318890](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119211318890.png)

![image-20211119204747666](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119204747666.png)

#### 3.6 NULL和索引影响效率

B树索引时不会存储NULL值的，所以如果索引的字段可以为NULL，索引的效率会下降很多。

虽然MySQL可以在含有NULL的列上使用索引，但NULL和其他数据还是有区别的，不建议列上允许为NULL，最好设置为NOT NULL，并给一个默认值，0，""，或者当前时间，或者时间全0。

MYSQL难以优化可空列的查询,它会使索引,索引统计和值更加复杂.可空列需要更多的存储空间,还需要在MYSQL内部进行特殊处理.当可空列被索引的时候,每条记录都需要额外的一个字节,还能导致MYISAM(引擎)中固定大小的索引变成可变大小的索引.可以考虑使用0,特殊值来代替。

> NULL columns require additional space in the row to record whether their values are NULL. For MyISAM tables, each NULL column takes one bit extra, rounded up to the nearest byte.
>
> NULL类需要增加额外空间来记录其值是否为NULL，对于MyISAM表，每一个空列额外占用一位，四舍五入到最接近的字节。

但把NULL列改为NOT NULL带来的性能提示很小,除非确定它带来了问题,否则不要把它当成优先的优化措施,最重要的是使用的列的类型的适当性。

#### 3.7 查询常数

![image-20211119205742515](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119205742515.png)

应用场景：当整合不同的数据源时，可以使用这个常数来作为这个表的标记。

### 4 显示表结构

```sql
DESCRIBE | DESC `table_name`
```

![image-20211119210012726](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119210012726.png)

Extra：表示可以获取的与给定列有关的附加信息，例如AUTO_INCREMENT等。

### 5 过滤数据（where子句）

## 第04章_运算符

### 1.算术运算符

![image-20211119212643853](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119212643853.png)

![image-20211119212901333](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119212901333.png)

![image-20211119212934024](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119212934024.png)

加减结论：

> 一个整数类型的值对整数进行加法和减法操作，结果还是一个整数；
>
> 一个整数类型的值对浮点数进行加法和减法操作，结果是一个浮点数；
>
> 加法和减法的优先级相同，进行先加后减操作与进行先减后加操作的结果是一样的；
>
> 在Java中，+的左右两边如果有字符串，那么表示字符串的拼接。但是在MySQL中+只表示数值相加。如果遇到非数值类型，先尝试转成数值，如果转失败，就按0计算。（补充：MySQL中字符串拼接要使用字符串函数CONCAT()实现）

![image-20211119213335255](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211119213335255.png)

乘除结论：

> 一个数除以整数后，不管是否能除尽，结果都为一个浮点数；
>
> 一个数除以另一个数，除不尽时，结果为一个浮点数，并保留到小数点后4位；
>
> 在数学运算中，0不能用作除数，在MySQL中，一个数除以0为NULL。 

### 2.比较运算符

比较运算符用来对表达式左边的操作数和右边的操作数进行比较，**比较的结果为真则返回1，比较的结果为假则返回0，其他情况则返回NULL（与NULL比较返回的也是NULL）。**

#### 2.1 安全等于运算符（这一类的是符号类型运算符）

| 运算符 | 作用       |
| ------ | ---------- |
| =      | 等于       |
| <=>    | 安全的等于 |

1. 等号运算符

在使用等号运算符时，遵循如下规则：

- 如果等号两边的值、字符串或表达式都为字符串，则MySQL会按照字符串进行比较，其比较的是每个字符串中字符的**ANSI编码**是否相等。

  > “ANSI编码”确实只存在于Windows系统。
  >
  > 其实ANSI并不是某一种特定的字符编码，而是在不同的系统中，ANSI表示不同的编码。你的美国同事Bob的系统中ANSI编码其实是ASCII编码（ASCII编码不能表示汉字，所以汉字为乱码），而你的系统中（“汉字”正常显示）ANSI编码其实是GBK编码，而韩文系统中（“한국어”正常显示）ANSI编码其实是EUC-KR编码。

- 如果等号两边的值都是整数，则MySQL会按照整数来比较两个值的大小。

- 如果等号两边的值一个是整数，另一个是字符串，则MySQL会将字符串转化为数字进行比较。

- 如果等号两边的值、字符串或表达式中有一个为NULL，则比较结果为NULL。

2. 安全等于运算符

安全等于运算符（<=>）与等于运算符（=）的作用是相似的， 唯一的区别 是‘<=>’可以用来对NULL进行判断。在两个操作数均为NULL时，其返回值为1，而不为NULL；当一个操作数为NULL时，其返回值为0，而不为NULL。

#### 2.2 非符号类型的运算符

| 运算符      | 名称         | 作用 |
| ----------- | ------------ | ---- |
| is null     | 为空运算符   |      |
| is not null | 不为空运算符 |      |
| least       | 最小值运算符 |      |
| greatest    | 最大值运算符 |      |
| between and |              |      |
| isnull()    | 为空         |      |
| in          | 属于         |      |
| not in      | 不属于       |      |
| like        | 模糊匹配     |      |
| regexp      | 正则表达式   |      |
| rlike       | 正则表达式   |      |

##### **like运算符：**

"%"：匹配0个或多个字符。

"_"：只能匹配一个字符。

##### regexp运算符（正则匹配）

https://www.runoob.com/mysql/mysql-regexp.html

## 第05章_排序和分页

### 1.排序数据

#### 1.1 规则

使用order by。asc：升序；desc：降序；

![image-20211120110844674](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120110844674.png)

![image-20211120110903461](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120110903461.png)

null 和 空默认最小。

#### 1.2 单列排序

#### 1.3 多列排序

- 可以使用不在SELECT列表中的列排序。
- 在对多列进行排序的时候，首先排序的第一列必须有相同的列值，才会对第二列进行排序。如果第一列数据中所有值都是唯一的，将不再对第二列进行排序。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120111059299.png" alt="image-20211120111059299" style="zoom:50%;" />

### 2.分页

**MySQL中使用LIMIT** **实现分页**

格式：

```sql
limit [位置偏移量,] 行数;
```

第一个“位置偏移量”参数指示MySQL从哪一行开始显示，是一个可选参数，如果不指定“位置偏移量”，将会从表中的第一条记录开始（第一条记录的位置偏移量是0，第二条记录的位置偏移量是1，以此类推）；第二个参数“行数”指示返回的记录条数。

> MySQL 8.0中可以使用“LIMIT 3 OFFSET 4”，意思是获取从第5条记录开始后面的3条记录，和“LIMIT 4,3;”返回的结果相同。

![image-20211120112812727](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120112812727.png)

约束返回结果的数量可以 **减少数据表的网络传输量** ，**也可以 提升查询效率** 。如果我们知道返回结果只有1 条，就可以使用 LIMIT 1 ，告诉 SELECT 语句只需要返回一条记录即可。这样的好处就是 SELECT 不需要扫描完整的表，只需要检索到一条符合条件的记录即可返回。

## 第06章_多表查询（关联查询）

#### 1.2笛卡尔积（交叉连接）的理解

SQL92中，**笛卡尔积也称为 交叉连接** ，英文是 CROSS JOIN 。在 SQL99 中也是使用 CROSS JOIN表示交叉连接。它的作用就是可以把任意表进行连接，即使这两张表不相关。在MySQL中如下情况会出现笛卡尔积：

```sql
#查询员工姓名和所在部门名称
SELECT last_name,department_name FROM employees,departments; SELECT last_name,department_name FROM employees CROSS JOIN departments; 
SELECT last_name,department_name FROM employees INNER JOIN departments; 
SELECT last_name,department_name FROM employees JOIN departments;
```

还没有搞清楚的是：，、cross join、inner join 和 join 加连接条件返回的都是笛卡尔积的结果，但是过程现在还没有搞清楚，等待以后的探索。

![image-20211120120933345](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120120933345.png)

![image-20211120121108944](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120121108944.png)

![image-20211120121421102](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120121421102.png)

![image-20211120212225528](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211120212225528.png)

### 2.多表查询分类

#### 分类一：等值连接 VS 非等值连接

等值连接在两个表连接的时候条件为某两个字段的值相等。而非等值连接在两个表连接时不是根据字段相等的条件去连接的，例如可以根据一个表中的字段是大于另一个表中的字段或者位于另一个表中两个字段取值之间这种方式去连接两个表进行查询。

#### 分类二：自连接 VS 非自连接

自己和自己连接的表方式为自连接，两个不同的表连接为非自连接

#### 分类三：内连接 VS 外连接

内连接：合并具有同一列（字段）的两个以上的表的行, **结果集中不包含一个表与另一个表不匹配的行**

外连接：两个表在连接过程中除了返回满足连接条件的行以外**还返回左（或右）表中不满足条件的行 ，这种连接称为左（或右） 外连接**。没有匹配的行时, 结果表中相应的列为空(NULL)。

外连接的分类：

- 左外连接：
- 右外连接：
- 全外连接：

#### SQL92语法实现外连接：

（+）MySQL并不支持这种语法，所以所SQL92中是有关于外连接的支持的，只是MySQL不支持。

#### SQL99语法实现内、外连接：

使用JOIN。。。ON 的方式实现多表的查询。这种方式可以解决外连接的问题，MySQL是使用这种方式解决外连接的，这个语法也可以实现内连接。到这里我就明白了。

实现内连接的时候使用的就是 `[inner] JOIN...ON`的方式。inner可选（join，inner join ，cross join 的含义是一样的，都表示内连接）

左外连接：`left [outer] join...on `。outer可选

右外连接：`right [outer] join...on`。outer可选

MySQL不支持full outer join。

#### SQL99语法新特性

##### 自然连接

SQL99在SQL92的基础上提供了一些特殊的语法，比如natural join用来表示自然连接，我们可以把自然连接理解为SQL92中的等值连接，他会帮你自动查询两张表中的所有相同字段，然后进行等值连接。

##### using连接

当我们进行连接的时候，SQL99还支持使用using指定数据表里的同名字段进行等值连接，并且会删除重复的列。但是只能配合JOIN一起使用。

### 3.UNION的使用

union操作去除两个集合中重复的元素；

union all 操作不去除两个集合中的重复元素；

MySQL可以使用union all来解决不支持全外连接的操作。

> **注意：**执行UNION ALL语句时所需要的资源比UNION语句少。如果明确知道合并数据后的结果数据不存在重复数据，或者不需要去除重复的数据，则尽量使用UNION ALL语句，以提高数据查询的效率。

### 4.总结

表连接的约束条件可以有三种方式：**WHERE, ON, USING** 

- WHERE：适用于所有关联查询
- ON ：只能和JOIN一起使用，只能写关联条件。虽然关联条件可以并到WHERE中和其他条件一起写，但分开写可读性更好。**使用on查询出来的数据不会删除重复的列。**
- USING：只能和JOIN一起使用，而且要求**两个**关联字段在关联表中名称一致，而且只能表示关联字段值相等。**使用USING查询出来的数据会删除重复的列，和自然连接的结果一样。**

## 第07章_单输入单输出函数

### 1.函数的理解

在SQL语言中包含了内置函数和自定义函数。

#### 1.2 不同DBMS函数的差异

我们在使用 SQL 语言的时候，不是直接和这门语言打交道，而是通过它使用不同的数据库软件，即DBMS。DBMS之间的差异远远大于一个语言的不同版本。实际上，只有很少的函数是被 DBMS 同时支持的。比如，大多数 DBMS 使用（||）或者（+）来做拼接符，而在 MySQL 中的字符串拼接函数为concat()。大部分 DBMS 会有自己特定的函数，这就意味着**采用** **SQL** **函数的代码可移植性是很差的**，因此在使用函数的时候需要特别注意。

#### 1.3 MySQL的内置函数和分类

MySQL提供的内置函数从 实现的功能角度 可以分为数值函数、字符串函数、日期和时间函数、流程控制

函数、加密与解密函数、获取MySQL信息函数、聚合函数等。这里，我将这些丰富的内置函数再分为两

类： **单行函数 、 聚合函数（或分组函数）** 。

### 2.数值函数

#### 2.1 基本函数

![image-20211121102103843](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121102103843.png)

#### 2.2 角度和弧度互换函数

![image-20211121102701876](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121102701876.png)

#### 2.3 三角函数

![image-20211121102732502](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121102732502.png)

#### 2.4 指数与对数函数

![image-20211121102815690](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121102815690.png)

#### 2.5 进制间转换函数

![image-20211121102827797](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121102827797.png)

### 3.字符串函数

### 4.日期函数

### 5.流程控制函数

### 6.加密解密函数

### 7.MySQL信息函数

### 8.其他函数

## 第08章_聚合函数

输入的是一组数据的集合，输出的是单个值。

### 1.聚合函数简介

**什么是聚合函数**：聚合函数作用于一组数据，并对一组数据返回一个值。

**聚合函数类型**：AVG、sum、max、min、count

聚合函数语法：

```sql
select [column,] group function(column),...
from table
[where condition]
[group by column]
[order by column]
```

**注意：聚合函数不能嵌套调用**。比如不能出现类似“AVG(SUM(字段名称))”形式的调用。

#### 1.1 AVG和SUM函数（数值类型）

可以对**数值类型**使用这两个函数。

#### 1.2 MIN和MAX函数（任意类型）

可以对**任意数据类型**的数据使用 MIN 和 MAX 函数。

#### 1.3 COUNT函数

COUNT(*)返回表中记录总数，适用于**任意数据类型**。

COUNT(column_name) 返回column_name中不为null的记录总数。

问题：用count(*)、count(1)、count(column_name)那个好？

其实对于MyISAM引擎的表是没有区别的。这种引擎内部有一个计数器在维护这行数。Innodb引擎表用count(*)和count(1)直接读行数，复杂度是o(n),因为innodb真的要去数一遍，但是好于使用具体的列名。

问题：能不能使用count(column_name)替换count(*)？

不能使用count(列名)来替换count(*)，count(列名)不会统计值为null的数据，而那个会统计行数，跟null和非null无关。

### 2.GROUP by

#### 2.1 基本使用

可以使用GROUP BY子句将表中的数据分成若干组

在SELECT列表中所有未包含在组函数中的列都应该包含在GROUP BY子句中。

包含在 GROUP BY 子句中的列不必包含在SELECT 列表中

#### 2.2 使用多个列分组

group by 的列可以为多个。

#### 2.3 group by 中使用 with rollup

使用with rollup关键字之后，在所有查询出的分组之后增加一条记录，该记录计算查询出的所有记录综合，即统计记录数量。

```sql
select name ,sum(id)
from account
group by name with rollup;
```

![image-20211121105414361](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121105414361.png)

> **注意：**
>
> 当使用ROLLUP时，不能同时使用ORDER BY子句进行结果排序，即ROLLUP和ORDER BY是互相排斥的。

### 3.HAVING

#### 3.1 基本使用

1. 行已经被分组
2. 使用聚合函数
3. 满足having字句中的条件的分组将被选择显示
4. having不能单独使用，必须要跟group by一起使用。
5. 不可以在where子句中使用聚合函数。

#### 3.2 where和having的对比

区别1：where可以直接使用表中的字段作为筛选条件，但是不能使用分组中的计算函数作为筛选条件；having必须要与group by配合使用，可以把分组计算的函数和分组字段作为筛选条件。这决定了，在需要对数据进行分组统计的时候，HAVING 可以完成 WHERE 不能完成的任务。这是因为，在**查询语法结构**中，**WHERE 在 GROUP BY 之前**，所以无法对分组结果进行筛选。HAVING 在 GROUP BY 之后，可以使用分组字段和分组中的计算函数，对分组的结果集进行筛选，这个功能是 WHERE 无法完成的。另外，WHERE排除的记录不再包括在分组中。

区别2：如果需要通过连接从关联表中获取需要的数据，**where是先筛选后连接，**而having是线连接后筛选。 这一点，就决定了在关联查询中，WHERE 比 HAVING 更高效。因为 WHERE 可以先筛选，用一个筛选后的较小数据集和关联表进行连接，这样占用的资源比较少，执行效率也比较高。HAVING 则需要先把结果集准备好，也就是用未被筛选的数据集进行关联，然后对这个大的数据集进行筛选，这样占用的资源就比较多，执行效率也较低。

![image-20211121110508047](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121110508047.png)

**开发中的选择：**

WHERE 和 HAVING 也不是互相排斥的，我们可以在一个查询里面同时使用 WHERE 和 HAVING。包含分组统计函数的条件用 HAVING，普通条件用 WHERE。这样，我们就既利用了 WHERE 条件的高效快速，又发挥了 HAVING 可以使用包含分组统计函数的查询条件的优点。当数据量特别大的时候，运行效率会有很大的差别。

### 4.SELECT的执行过程

#### 4.1 查询的结构

```sql
# 方式1
select [distinct]...
from ...
[where ... and...|or...] 
[group by...]
[having...]
[order by...asc|desc]
[limit ...,...]

# 方式2：
select [distinct]...
from ...[join...
on...
join...
on...]
[where ... and...|or...] 
[group by...]
[having...]
[order by...asc|desc]
[limit ...,...]
```

#### 4.2 select 的执行顺序

1. **关键字的顺序是不能颠倒的**

```sql
select...from...where...group by...having...order by...limit...
```

2. select 语句的执行顺序（在MySQL和Oracle中，顺序基本相同）

```sql
from...where...group by...having...select...distinct...order by...limit...
```

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121112256286.png" alt="image-20211121112256286" style="zoom:50%;" />

比如你写了一个 SQL 语句，那么它的关键字顺序和执行顺序是下面这样的：

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211121112413376.png" alt="image-20211121112413376" style="zoom:80%;" />

在select语句执行这些步骤的时候，没个步骤都会产生一个虚拟表，然后将这个虚拟表传入下一个步骤中作为输入。需要注意的是，这些步骤隐含在SQL的执行过程中，对于我们来说是不可见的。

#### 4.3 SQL的执行原理

select语句是先执行from这一步的。在这个阶段，如果是多张表联查，还会经历下面几个步骤：

1. 首先先通过cross join 求笛卡尔积，相当于得到虚拟表vt1-1.
2. 通过on进行筛选，在虚拟表vt1-1的基础上进行筛选，得到虚拟表vt1-2
3. 添加外部行。如果我们使用的是左连接、右连接或者全外连接，就会涉及到外部行，也就是在虚拟表vt1-2的基础上添加外部行，得到虚拟表vt1-3

如果操作的是两张以上的表，还会重复以上步骤，知道所有的表都被处理完为止，这个过程得到的使我们的原始数据。

当我们拿到了原始数据之后，也就是vt-1，就可以在这个基础上进行where阶段，在这个阶段中会根据vt1表中的结果进行筛选过滤，得到虚拟表vt2.

然后进入第三步和第四步，也就是group 和having阶段，在这个阶段中实际上是在虚拟表vt2的基础上进行分组和过滤，得到中间的虚拟表vt3和vt4

当完成条件筛选之后，就可以筛选表中提取的字段，也就是进入到select 和 distinct阶段。首先select 阶段会提取想要的字段，然后distinct阶段过滤掉重复的行，分别得到中间的虚拟表vt5-1和vt5-2

当提取完想要的字段之后，就可以按照指定的字段进行排序，也就是order by阶段，得到虚拟表vt6

最后在vt6的基础上，选取指定行的记录，也就是limit阶段，得到最终的结果，对应的是虚拟表vt7.

## 第09章_子查询

### 1.子查询

#### 1.1 基本使用

#### 1.2 子查询分类

方式一：按照子查询返回的结果是一条还是多条记录，将子查询分为单行子查询、多行子查询。

方式二：按照子查询是否被执行多次，将子查询划分为**相关（或关联）子查询**和**不相关（或非关联）子查询**。

子查询从数据表中查询了数据结果，如果这个数据结果只执行一次，然后这个数据结果作为主查询的条件进行执行，那么这样的子查询叫做**不相关子查询**。

同样，如果子查询需要执行多次，即采用循环的方式，先从外部查询开始，每次都传入子查询进行查询，然后再将结果反馈给外部，这种嵌套的执行方式就称为**相关子查询。**

### 2.单行子查询

#### 2.1 单行比较操作符

![image-20211122102615848](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122102615848.png)

#### 2.2 having中的子查询

首先会执行子查询，然后向主查询中的HAVING子句返回结果。

#### 2.3 case中的子查询

可以在case中的when字句中进行子查询。

#### 2.4 子查询中的空值问题

当子查询没有查询到任何值时（为空），父查询肯定也查询不到任何值。

#### 2.5 非法使用子查询

![image-20211122104559197](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122104559197.png)

![image-20211122104607220](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122104607220.png)

多行子查询使用单行比较符进行比较。

#### 2.6 MySQL中的 case when

case可以在自定义排序的时候使用

MySQL中的case when的语法有两种：

* case [col_name] when [value] then [result1]...else [default] end
* case when [expr] then [result]...else [default] end

![image-20211122103908767](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122103908767.png)

![image-20211122104146621](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122104146621.png)

### 3.多行子查询

#### 3.1 多行比较操作符

![image-20211122104642693](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122104642693.png)

#### 3.2 空值问题

如果not in 子查询查询出来的结果中存在null值，那么父查询将查询不到任何值。

![image-20211122113201931](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122113201931.png)

如果in子查询查出来的结果中只有null，那么这样是选不出父查询中满足is null 的数据的。

![image-20211122113702200](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122113702200.png)

### 4.相关子查询

#### 4.1 相关子查询执行流程

![image-20211122130237862](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122130237862.png)

**from型的子查询**：子查询是作为from的一部分，子查询要用()引起来，并且**要给这个子查询取别名**， 把它当成一张“临时的虚拟的表”来使用。

#### 4.2 exists 和 not exists关键字

关联子查询通常也会和exists操作符一起来使用，用来检查子查询中是否存在满足条件的行。

如果子查询中没有查询到任何满足条件的行：

* 条件返回false
* 继续在子查询中查找

如果子查询中存在满足条件的行：

* 不在子查询中继续查找
* 条件返回true

not exists关键字表示如果不存在某种条件，则返回true，否则返回false。

#### 4.3 相关更新

可以使用相关子查询查询数据，然后根据查询出来的数据更新一张表中的数据。

```sql
UPDATE table1 alias1 
SET column = (SELECT expression 
              FROM table2 alias2 
              WHERE alias1.column = alias2.column);
```

#### 4.4 相关删除

使用相关子查询依据一个表中的数据删除另一个表的数据

```sql
DELETE FROM employees e 
WHERE employee_id in (SELECT employee_id 
                      FROM emp_history 
                      WHERE employee_id = e.employee_id);
```

### 5.思考题(没懂)

问题：谁的工资比Abel高？

有使用自连接或者子查询两种方式解决这个问题。

```sql
# 方式1：自连接 
SELECT e2.last_name,e2.salary 
FROM employees e1,employees e2 
WHERE e1.last_name = 'Abel' AND e1.`salary` < e2.`salary`

#方式2：子查询 
SELECT last_name,salary 
FROM employees 
WHERE salary > ( SELECT salary 
                FROM employees 
                WHERE last_name = 'Abel' );
```

这两种方式相比较来说自连接的方式更好。

因为在许多DBMS的处理过程中，对于自连接的处理速度比子查询的快的多。可以这样理解：子查询实际上是通过未知表进行查询后的条件判断，而自连接是通过已知的自身数据表进行条件判断，因此大部分DBMS中都对自连接处理进行了优化。

如果MySQL优化器通过对子查询的方式进行优化过后发现可以变为多表查询的方式实现，那么就会把这个SQL优化为多表查询的方式。

# 第10章_创建和管理表

## 1.基础知识

#### 1.1 一条数据存储的过程

在MySQL中，一个完整的数据存储的步骤总共有4步，分别是创建数据库、确认字段、创建数据表、插入数据。

从系统架构的层次上看，MySQL数据库系统从大到小依次是数据库服务器、数据库、数据表、数据表的行和列。

#### 1.2 标识符命名规则

* 数据库名、表名不得超过30个字符，变量名限制为29个。但是实际验证了一下是不能超过64个字符。

![image-20211122132919974](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122132919974.png)

* 必须只能包含 A–Z, a–z, 0–9, _共63个字符，实际验证了一下，加上着重号可以创建包含别的字符的数据库名。

![image-20211122133149381](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211122133149381.png)

* 数据库名、表名、字段名等对象名中间不要包含空格。实际验证，这个只能说是建议，其实加上着重号还是可以的。

#### 1.3 MySQL中的数据类型

| 类型             | 举例                                                         |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | tinyInt、simallInt、MediumInt、int(Integer)、bigInt          |
| 浮点类型         | FLOAT、DOUBLE                                                |
| **定点数类型**   | decimal                                                      |
| 位类型           | bit                                                          |
| 日期时间类型     | YEAR、TIME、**DATE**、DATETIME、TIMESTAMP                    |
| 文本字符串类型   | CHAR、**VARCHAR**、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT      |
| 枚举类型         | enum                                                         |
| 集合类型         | set                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB      |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值：GEOMETRY、POINT、LINESTRING、POLYGON；           集合：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION |

其中，常用的几类类型介绍如下：

| 数据类型      | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| int           | 存储大小为4字节                                              |
| char(size)    | 定长字符串数据。**若未指定，默认为1个字符**，最大长度255     |
| varchar(size) | 可变长字符数据，根据字符串实际长度保存，**必须指定长度**     |
| float(M,D)    | 单精度，占用4个字节，M=整数位+小数位，D=小数位。 D<=M<=255,0<=D<=30，默认M+D<=6 |
| double(M,D)   | 双精度，占用8个字节，D<=M<=255,0<=D<=30，默认M+D<=15         |
| decimal(M,D)  | 高精度小数，占用M+2个字节，D<=M<=65，0<=D<=30，最大取值范围与DOUBLE相同 |
| date          | 日期型数据，格式'YYYY-MM-DD'                                 |
| blob          | 二进制形式的长文本数据，最大可达4G                           |
| text          | 长文本数据，最大可达4G                                       |

#### char和varchar

char：定长字符串类型。

特点：

* 存储空间固定，最大为255个字符，由于编码格式的不同，存储的最大字节数并不一定。（utf8一个中文字符3个字节，一个英文字符1个字节）
* 长度不够时内部存储使用**空格**填充
* 如果字段本身末尾存在空格，检索出来的自动截断末尾空格(因为是使用空格作为填充，所以分不清末尾的空格是填充的还是原来就有的)
* 字段前的空格是不会截断的。
* 当输入的字符串超出长度时，char会截取超出的字符。

使用情况：

* 适合存储很短的或长度接近同一个长度的字符串
* char比varchar在存取上更具效率，因为它是固定长度。
* 对于非常短的列，char比varchar在存储空间上也更有效率。

![image-20211123173617553](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123173617553.png)

varchar：可变长字符串

特点：

* 存储空间不固定，根据字段长度决定。
* 需要额外的1个或2个字节记录字符串的长度，字符串长度小于255字节使用1个字节记录，否则使用两个字节。
* 最大长度为65535字节（这里的单位是字节而非字符），字符集的不同对于varchar存储的字符的个数是有影响的。
* 如果列可以为null，则需要额外的一个字节作为标志。
* 最大长度=字段长度+[长度记录：1或2字节]+[null标志为：1字节]

我们在定义的时候定义varchar能存储多少字符，他就能存储多少字符，最大长度指的是字节数。

例如： 当定义一个字段类型为 varchar(10)，插入的值为"abc"这一字符串时，那么实际存储大小为3个字节，除此之外，varchar还需要使用1个额外字节（因为‘abc’字节数＜255）来记录字符串的长度。

适用情况：

* 字符串列的最大长度比平局长度大很多。
* 使用了像utf-8这样复杂的字符集，每个字符都是使用不同

char和varchar的区别：

* 在时间和空间上：char的缺点是浪费空间，优点是处理速度比varchar块。varchar的缺点是处速度慢，但是空间利用率高。
* 存储方式：
* like：由于char会忽略数据后面的空格，所以使用like的时候要注意查找的时候把数据后的空格删除。

#### 字节和字符的区别

字符(Character)计算机中使用的字母、数字、字和符号，比如'A'、'B'、'$'、'&'等。

一般在英文状态下一个字母或字符占用一个字节，一个汉字用两个字节表示。

![image-20211123174913339](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123174913339.png)

## 2.创建数据库

### 2.1 创建数据库

```sql
create database name;
create database name character set 字符集
create database if not exists name
```

> **注意**：DATABASE 不能改名。一些可视化工具可以改名，它是建新库，把所有表复制到新库，再删旧库完成的。

### 2.2 使用数据库

```sql
show databases # 查看所有数据库
select database() # 使用mysql的一个全局函数查看正在使用的数据库
show tables from 数据库名 # 查看指定数据库下的所有表
show create database 数据库名 # 查看数据库的创建信息
use 数据库名 # 使用/切换数据库
```

### 2.3 修改数据库

更改数据库字符集

```sql
alter database 数据库名 character set 字符集;
```

使用show character set查看mysql支持那些charset；

![image-20211123180732124](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123180732124.png)

### 2.4 删除数据库

```sql
drop database 数据库名
drop database if exists 数据库名 # 推荐
```

![image-20211123180852353](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123180852353.png)

## 3.创建表

### 3.1 创建方式1

**必备条件**：

* create table 权限
* 存储空间

语法：

```sql
create table [if not exists] 表名(
	字段1，数据类型 [约束条件] [默认值],
    字段2，数据类型 [约束条件] [默认值],
    [表约束条件]
)
```

> 加上了IF NOT EXISTS关键字，则表示：如果当前数据库中不存在要创建的数据表，则创建数据表；如果当前数据库中已经存在要创建的数据表，则忽略建表语句，不再创建数据表。

**必须指定：**

* 表名
* 列名(字段名)，数据类型，**长度**

**可选指定**：

* 约束条件
* 默认值

> 在MySQL 8.x版本中，不再推荐为INT类型指定显示长度，并在未来的版本中可能去掉这样的语法。

### 3.2 创建方式2

使用 AS subquery 选项，**将创建表和插入数据结合起来**

```sql
CREATE TABLE emp1 AS SELECT * FROM employees; 
CREATE TABLE emp2 AS SELECT * FROM employees WHERE 1=2; -- 创建的emp2是空表
```

### 3.3 查看数据表结构

```sql
show create table 表名\G
describe/desc 表名
```

使用show create table语句不仅可以查看表创建时的详细语句，还可以查看存储引擎和字符编码。

![image-20211123193417883](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123193417883.png)

![image-20211123193606518](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123193606518.png)

### int类型的显示长度

定义int类型的时候，初始化的显示长度并不控制数据的存储大小，长度之类的，只是规定了当数据位数小于指定位数的时候，怎么去显示数据。也就是说，int的长度并不影响数据的存储精度，长度只和显示有关。

显示宽度(例如，INT(4))。该可选显示宽度规定用于显示宽度小于指定的列宽度的值时从左侧填满宽度。

显示宽度并不限制可以在列内保存的值的范围，也不限制超过列的指定宽度的值的显示。

当结合可选扩展属性ZEROFILL使用时， 默认补充的空格用零代替。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123192239242.png" alt="image-20211123192239242" style="zoom:50%;" />

## 4.修改表(alter table)

修改表指的是修改数据库中已经存在的数据表的结构。

使用alter table 语句可以实现：

* 添加列
* 修改列
* 删除列
* 重命名列

### 4.1 添加列

```sql
alter table table_name [column] 字段名 字段类型 [字段约束] [first|after 字段名]
```

如果存在关键字first，那么新加入的字段将会显示在第一列，after可以规定加入的字段位于那个列后面。

![image-20211123194153473](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123194153473.png)

### 4.2 修改一个列（修改约束）

可以修改列的数据类型、长度、默认值和位置

```sql
alter table 表名 modify [column] 字段名1 字段类型 
```

对默认值的修改只能影响今后对表的修改

此外，还可以通过这种方式**修改列的约束**。

### 4.3 重命名一个列

```sql
alter table table_name change [column] 列名 新列名 数据类型
```

### 4.4 删除一个列

```sql
alter table 表名 drop [column] 字段名
```

## 5.重命名表

方式一：使用rename

```sql
rename table table_name to new_table_name
```

方式二：

```sql
alter table table_name rename [to] new_table_name
```

![image-20211123195241475](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123195241475.png)

![image-20211123195248084](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211123195248084.png)

## 6.删除表

```sql
drop table [if exists] table_name1,table_name2...
```

- 表被删除时，用户在该表上的权限不会自动删除。

* 在MySQL中，当一张表没有与其他任何数据表形成关联关系时，可以将当前数据表直接删除
* 数据和结构都被删除
* 所有正在运行的相关事务被提交
* 所有相关索引被删除
* drop table 语句不能回滚（）

## 7 清空表

truncate table 语句：

* 删除表中所有的数据
* 释放表的存储空间

truncate语句不能回滚，使用delete语句删除数据可以回滚

> 【参考】TRUNCATE TABLE 比 DELETE 速度快，且使用的系统和事务日志资源少，但 TRUNCATE 无事务且不触发 TRIGGER，有可能造成事故，故不建议在开发代码中使用此语句。

## 8 扩展

### MySQL8新特性（DDL事务，没有操作成功）

在MySQL 8.0版本中，InnoDB表的DDL支持事务完整性，即 DDL操作要么成功要么回滚 。DDL操作回滚日志写入到data dictionary数据字典表mysql.innodb_ddl_log（该表是隐藏的表，通过show tables无法看到）中，用于回滚操作。通过设置参数，可将DDL操作日志打印输出到MySQL错误日志中。

# 第11章_数据处理之增删改

## 1.插入数据

### 1.1 使用values的方式

```sql
INSERT INTO table_name(column1 [, column2, …, columnn]) VALUES (value1 [,value2, …, valuen]),
(value1 [,value2, …, valuen]), 
……(value1 [,value2, …, valuen]);
```

使用INSERT同时插入多条记录时，MySQL会返回一些在执行单行插入时没有的额外信息，这些信息的含义如下： ●　Records：表明插入的记录条数。 ●　Duplicates：表明插入时被忽略的记录，原因可能是这些记录包含了重复的主键值。 ●　Warnings：表明有问题的数据值，例如发生数据类型转换。

![image-20211124103720485](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211124103720485.png)

> 一个同时插入多行记录的INSERT语句等同于多个单行插入的INSERT语句，但是多行的INSERT语句在处理过程中 效率更高 。因为MySQL执行单条INSERT语句插入多行数据比使用多条INSERT语句快，所以在插入多条记录时最好选择使用单条INSERT语句的方式插入。

这里为什么多行插入比单行插入效率高还是没有弄清楚。

### 1.2 使用insert into ... select from ...的方式

```sql
INSERT INTO 目标表名 
(tar_column1[,tar_column2…,tar_columnn])
SELECT 
(src_column1 [, src_colun2, …, src_columnn]) 
FROM 源表名 [WHERE condition]
```

使用这种方式不用写values子句，子查询中的值列表应该和insert子句中的列名对应。

## 2.更新数据

```sql
UPDATE table_name 
SET column1=value1, column2=value2, … , column=valuen 
[WHERE condition]
```

* 可以一次更新多条数据
* 如果需要回滚数据，需要保证在DML之前，进行设置：set autocommit= false

* 如果更新语句没有使用where选择，那么所有的数据都将被更新

### **更新中的数据完整性错误**

如果更新的数据存在完整性约束，那么如果设置的数据违反了完整性约束，那么数据将不会被设置并且抛出错误。

## 3.删除数据

```sql
DELETE FROM table_name [WHERE <condition>];
```

如果没有指定where子句，那么将删除表中所有数据。

### 删除中的数据完整性错误

> 说明：You cannot delete a row that contains a primary key that is used as a foreign key in another  table. 

## 4.MySQL8新特性：计算列

MySQL的Generated Column又称为虚拟列或计算列。Generated Column列的值是在列定义时包含了一个计算表达式计算得到的。

```sql
列名 类型
[GENERATED ALWAYS] 
AS (expr) # AS（expr）用于生成计算列值的表达式。
[VIRTUAL | STORED] # 表示是否存储计算列的值
[NOT NULL | NULL] 
[UNIQUE [KEY]] 
[[PRIMARY] KEY]
[COMMENT 'string']
```

- virtual：列值不存储，虚拟列不占用存储空间，默认设置为VIRTUAL。
- stored：在添加或更新行时计算并存储列值。存储列需要存储空间，并且可以创建索引。

## 5.MySQL的完整性约束

1. 主键约束
2. 唯一约束
3. 外键约束
4. 非空约束
5. 检查约束

# 第12章_MySQL数据类型

## 1.MySQL中的数据类型

| 类型             | 举例                                                         |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | tinyInt、simallInt、MediumInt、int(Integer)、bigInt          |
| 浮点类型         | FLOAT、DOUBLE                                                |
| **定点数类型**   | decimal                                                      |
| 位类型           | bit                                                          |
| 日期时间类型     | YEAR、TIME、**DATE**、DATETIME、TIMESTAMP                    |
| 文本字符串类型   | CHAR、**VARCHAR**、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT      |
| 枚举类型         | enum                                                         |
| 集合类型         | set                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB      |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值：GEOMETRY、POINT、LINESTRING、POLYGON；           集合：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION |

常见数据类型的属性：

| MySQL关键字          | 含义                     |
| -------------------- | ------------------------ |
| null                 | 数据列可包含null         |
| not null             | 数据列不可包含null       |
| default (值)         | 默认值                   |
| primary key          | 主键                     |
| auto_increment       | 自动递增，使用于整数类型 |
| unsigned             | 无符号                   |
| character set (name) | 指定一个字符集           |

## 2.整数类型

### 2.1 类型介绍

整数类型一共有5中，tinyint（1字节）,smallint（2字节）,mediumint（三字节）,int(integer)（4字节）,bigint（8字节）。

整数类型可以定义为unsigned，表示无符号，这样的话他们的取值范围都为非负数，并且正数可以取到的值乘2。

### 2.2 可选属性

#### 2.2.1 M 显示宽度

M : 表示显示宽度，M的取值范围是(0, 255)。例如，int(5)：当数据宽度小于5位的时候在数字前面需要用字符填满宽度。该项功能需要配合“ ZEROFILL ”使用，表示用“0”填满宽度，否则指定显示宽度无效。

如果设置了显示宽度，那么插入的数据宽度超过显示宽度限制，会不会截断或插入失败？

答案：不会对插入的数据有任何影响，还是按照类型的实际宽度进行保存，即 显示宽度与类型可以存储的 值范围无关 。**从MySQL 8.0.17开始，整数数据类型不推荐使用显示宽度属性。**

整型数据类型可以在定义表结构时指定所需要的显示宽度，如果不指定，则系统为每一种类型指定默认的宽度值。默认宽度在MySQL5中的设置如下：

![image-20211124113130729](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211124113130729.png)

TINYINT有符号数和无符号数的取值范围分别为-128~127和0~255，由于负号占了一个数字位，因此TINYINT默认的显示宽度为4。同理，其他整数类型的默认显示宽度与其有符号数的最小值的宽度相同。

#### 2.2.2 unsigned

unsigned：无符号类型（非负），所有的整数类型都有一个可选的属性UNSIGNED（无符号属性），无符号整数类型的最小取值为0。所以，如果需要在MySQL数据库中保存非负整数值时，可以将整数类型设置为无符号类型。

int类型默认显示宽度为int(11)，无符号int类型默认显示宽度为int(10)。 

![image-20211124113354134](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211124113354134.png)

#### 2.2.3 zerofill

**zerofill**： 0填充,（如果某列是ZEROFILL，那么MySQL会自动为当前列添加UNSIGNED属性），如果指定了ZEROFILL只是表示不够M位时，用0在左边填充，如果超过M位，只要不超过数据存储范围即可。

原来，在 int(M) 中，M 的值跟 int(M) 所占多少存储空间并无任何关系。 int(3)、int(4)、int(8) 在磁盘上都是占用 4 bytes 的存储空间。也就是说，**int(M)，必须和UNSIGNED ZEROFILL一起使用才有意义。**如果整数值超过M位，就按照实际位数存储。只是无须再用字符 0 进行填充。

### 2.3 使用场景

tinyint：一般用于枚举数据，比如系统设定值范围很小且固定的场景。

smallint：可以用于较小范围的统计数据，比如统计工厂的固定资产库存数量等。

mediumint：用于交大整数的计算，比如车站每日的客流量。

int，integer：取值范围足够大，一般情况下不用考虑超限问题，用的最多，比如商品编号。

bigint：只有当你处理特别巨大的整数时才会用到。比如双十一的交易量、大型门户网站点击量、证券公司衍生产品持仓等。

## 3.浮点类型

### 单精度和双精度

单精度是这样的格式，1位符号，8位指数，23位小数。

![preview](https://pic2.zhimg.com/v2-749cc641eb4d5dafd085e8c23f8826aa_r.jpg?source=1940ef5c)

双精度是1位符号，11位指数，52位小数。

![img](https://pic1.zhimg.com/80/v2-48240f0e1e0dd33ec89100cbe2d30707_1440w.jpg?source=1940ef5c)

单精度和双精度精确的范围不一样，单精度float一般在计算机中存储占用4字节，32位，分别为1位符号位，8位指数为，23位尾数位，小数点后的有效位为7位。双精度double储存8个字节，64位，分别为1位符号位，11位指数位，52位尾数位，有效位为16位。

float的最大值大约为为2^128次方。

### 3.1 类型介绍

浮点数和定点数(decimal)类型可以用来处理小数。MySQL 支持的浮点数类型分别为float、double、real。

![image-20211126100830079](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211126100830079.png)

real默认就是double，如果把SQL模式设置为启动“REAL_AS_FLOAT”，那么MySQL就认为REAL是FLOAT。

```sql
set sql_mode = "REAL_AS_FLOAT"
```

为什么浮点数类型的无符号取值范围只相当于有符号数取值范围的一半，也就是有符号数非负数的部分？

MySQL存储浮点数的格式为：符号、尾数、阶码。因此无论有没有符号，MySQL的浮点数都会存储表示符号的部分，因此，所谓的无符号数取值范围其实就是有符号数取值范围大于等于0的部分。

### 3.2 数据精度说明

对于浮点类型，MySQL中单精度使用4字节，双精度使用8字节。

MySQL中允许使用非标准语法：float(M,D)或double(M,D)。这里M称为精度，D称为标度。(M,D)中M=整数位数+小数位数，D = 小数位数。D<=M<=255，0<=D<=30。例如float(5,2)可以显示为-999.99到999.99范围，如果超出这个范围就会报错。

当我们没有指定float和double的精度时，默认会按照实际的精度来显示。

浮点类型如果加上unsigned不会改变数据的范围。

对于数据超出范围的处理：

* 如果整数部分超出了范围，MySQL直接报错
* 若果小数部分超出了范围，分为一下情况：
  * 若四舍五入后，整数部分没有超出范围，则只是警告，但是能成功操作并四舍五入删除多余的小数位后保存。例如在FLOAT(5,2)列内插入999.009，近似结果是999.01。
  * 若四舍五入后，整数部分超出范围，MySQL会报错，并拒绝处理。

在MySQL8开始，**FLOAT(M,D)** 和DOUBLE(M,D)用法在官方文档中已经明确不推荐使用，将来可能被移除。另外，关于浮点型FLOAT和DOUBLE的**UNSIGNED**也不推荐使用了，将来也可能被移除。

### 3.3 精度误差说明

浮点数类型有一个缺陷，就是不精准。有些小数部分不能准确的使用2的负数次方来表示，所以就会四舍五入，造成数据的不精确。

MySQL 用 4 个字节存储 FLOAT 类型数据，用 8 个字节来存储 DOUBLE 类型数据。无论哪个，都是采用二进制的方式来进行存储的。比如 9.625，用二进制来表达，就是 1001.101，或者表达成 1.001101×2^3。如果尾数不是 0 或 5（比如 9.624），你就无法用一个二进制数来精确表达。进而，就只好在取值允许的范围内进行四舍五入。

MySQL中的定点数是精准的类型。

## 4.定点数类型decimal

### 4.1 类型介绍

MySQL中顶点数类型只有decimal一种类型。

| 数据类型                 | 字节数  | 含义               |
| ------------------------ | ------- | ------------------ |
| decimal(M,D),dec,numeric | M+2字节 | 有效范围由M和D决定 |

使用decimal（M,D）的方式表示高精度小数。其中M称为精度，D称为标度。0<=M<=65，0<=D<=30，D<M。例如，定义DECIMAL（5,2）的类型，表示该列取值范围是-999.99~999.99。

decimal（M,D）的最大取值范围与double类型一样，但是有效的数据范围是由M和D决定的。DECIMAL 的存储空间并不是固定的，由精度值M决定，总共占用的存储空间为M+2个字节。也就是说，在一些对精度要求不高的场景下，比起占用同样字节长度的定点数，浮点数表达的数值范围可以更大一些。

定点数在MySQL内部是以字符串的形式进行存储的，这就决定了他一定是精准的。

当DECIMAL类型不指定精度和标度时，其默认为DECIMAL(10,0)。当数据的精度超出了定点数类型的精度范围时，则MySQL同样会进行四舍五入处理。

浮点数和定点数对比：

* 浮点数相对于定点数的优点是在长度一定的情况下，浮点类型取值范围大，但是不精准，适用于需要取值范围大，又可以容忍微小误差的科学计算场景（比如计算化学、分子建模、流体动力学等）
* 定点数类型取值范围相对小，但是精准，没有误差，适合于对精度要求极高的场景 （比如涉及金额计算的场景）

在项目中除了极少数用到整数类型外，其他的数值都是使用decimal。

## 5.位类型：BIT

bit类型中存储的是二进制的值。类似0001110

| 二进制字符串类型 | 长度 | 长度范围 | 占用空间        |
| ---------------- | ---- | -------- | --------------- |
| bit(M)           | M    | 1<=M<=64 | 约为(M+7)/8字节 |

为什么占用的空间是(M+7)/8字节呢？因为存储空间肯定是按照整字节来分，使用这个方式计算出来的字节的位长度大于等于指定的位长度。

使用bit+0可以直接查询出来十进制的数。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211126110435521.png" alt="image-20211126110435521" style="zoom:50%;" />

## 6.[日期和时间类型](https://blog.csdn.net/qq_45869707/article/details/120137229?spm=1001.2014.3001.5501)

MySQL8.0版本支持的日期和时间类型主要有：YEAR类型、TIME类型、DATE类型、DATETIME类型和TIMESTAMP类型。

* year类型通常用来表示年
* date类型通常用来表示年月日
* time类型通常用来表示时分秒
* datetime类型通常用来表示年月日时分秒
* timestamp类型通常用来**表示带时区**的年月日时分秒

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211126110800048.png" alt="image-20211126110800048"  />

year类型一个字节，我认为：总共8位可以表示255个数，其中一个数表示最小值1902，其他254个数表示增加量。

MySQL中的time类型不仅可以表示一天之内的时间，也可以表示一个时间间隔，这个时间间隔可以超过24个小时，也就解释了为什么MySQL中time的取值范围为以上的。

time的赋值格式有很多种，例如"HH：MM：SS"、"D HH：MM：SS"，如果赋值的时候前面有D，就相当于D*24.

### 6.1 year类型

year有一下几种赋值格式：

* 以4位字符串或数字格式表示YEAR类型，其格式为YYYY，最小值为1901，最大值为2155
* 以两位字符串格式表示year类型，最小值00，最大值99
  * 当取值为01到69时，表示2001到2069；
  * 当取值为70到99时，表示1970到1999；
  * 当取值整数的0或00添加的话，那么是0000年；
  * 当取值是日期/字符串的'0'添加的话，是2000年。
* 使用 **current_timestamp()、CURRENT_DATE()或 CURRENT_TIME()或者 NOW() 函数**，会插入当前系统的年份。

从MySQL5开始2位格式的year不推荐使用，year默认格式为“yyyy”，从MySQL 8.0.19开始，不推荐使用指定显示宽度的YEAR(4)数据类型。

### 6.2 date类型

DATE类型表示日期，没有时间部分，格式为 YYYY-MM-DD ，其中，YYYY表示年份，MM表示月份，DD表示日期。需要 3个字节 的存储空间。在向DATE类型的字段插入数据时，同样需要满足一定的格式条件。

* 以 YYYY-MM-DD 格式或者 YYYYMMDD 格式表示的字符串日期，其最小取值为1000-01-01，最大取值为9999-12-03。YYYYMMDD格式会被转化为YYYY-MM-DD格式。

* 以 YY-MM-DD 格式或者 YYMMDD 格式表示的字符串日期，此格式中，年份为两位数值或字符串满足YEAR类型的格式条件为：当年份取值为00到69时，会被转化为2000到2069；当年份取值为70到99时，会被转化为1970到1999。

* 使用 CURRENT_DATE() 或者 NOW() 函数，会插入当前系统的日期。

### 6.3 time类型

TIME类型用来表示时间，不包含日期部分。在MySQL中，需要 3个字节 的存储空间来存储TIME类型的数据，可以使用“HH:MM:SS”格式来表示TIME类型，其中，HH表示小时，MM表示分钟，SS表示秒。

在MySQL中，向TIME类型的字段插入数据时，也可以使用几种不同的格式。 

* 可以使用带有冒号的字符串，比如' D HH:MM:SS' 、' HH:MM:SS '、' HH:MM '、' D HH:MM '、' D HH '或' SS '格式，都能被正确地插入TIME类型的字段中。其中D表示天，其最小值为0，最大值为34。如果使用带有D格式的字符串插入TIME类型的字段时，D会被转化为小时，计算格式为D*24+HH。当使用带有冒号并且不带D的字符串表示时间时，表示当天的时间，比如12:10表示12:10:00，而不是00:12:10。

* 可以使用不带有冒号的字符串或者数字，格式为' HHMMSS '或者 HHMMSS 。如果插入一个不合法的字符串或者数字，MySQL在存储数据时，会将其自动转化为00:00:00进行存储。比如1210，MySQL会将最右边的两位解析成秒，表示00:12:10，而不是12:10:00。

* 使用 CURRENT_TIME() 或者 NOW() ，会插入当前系统的时间。

### 6.4 datetime类型

DATETIME类型在所有的日期时间类型中占用的存储空间最大，总共需要 8 个字节的存储空间。在格式上为DATE类型和TIME类型的组合，可以表示为 YYYY-MM-DD HH:MM:SS ，其中YYYY表示年份，MM表示月份，DD表示日期，HH表示小时，MM表示分钟，SS表示秒。

在向DATETIME类型的字段插入数据时，同样需要满足一定的格式条件：

* 以 YYYY-MM-DD HH:MM:SS 格式或者 YYYYMMDDHHMMSS 格式的字符串插入DATETIME类型的字段时，最小值为1000-01-01 00:00:00，最大值为9999-12-03 23:59:59。 
  * 以YYYYMMDDHHMMSS格式的数字插入DATETIME类型的字段时，会被转化为YYYY-MM-DD HH:MM:SS格式。
* 以 YY-MM-DD HH:MM:SS 格式或者 YYMMDDHHMMSS 格式的字符串插入DATETIME类型的字段时，两位数的年份规则符合YEAR类型的规则，00到69表示2000到2069；70到99表示1970到1999。
* 使用函数 CURRENT_TIMESTAMP() 和 NOW() 、current_time，可以向DATETIME类型的字段插入系统的当前日期和时间。
* 使用函数current_date 插入的datetime 只有date没有time

### 6.5 timestamp类型

timestamp类型也可以表示日期时间，其显示格式与DATETIME类型相同，都是YYYY-MM-DD HH:MM:SS，需要4个字节的存储空间。但是timestamp存储的时间范围比datatime要小很多，只能存储“1970-01-01 00:00:01 UTC”到“2038-01-19 03:14:07 UTC”之间的时间。

协调世界时，又称世界统一时间、世界标准时间、国际协调时间。由于英文（CUT）和法文（TUC）的缩写不同，作为妥协，简称UTC。

存储数据的时候需要对当前时间所在的时区进行转换，查询数据的时候再将时间转回到当前的时区。因此使用TimeStamp存储的同一个时间值在不的时区会显示不同的时间。

timestamp和datetime的区别：

* TIMESTAMP存储空间比较小（4字节），表示的日期时间范围也比较小。
* 底层存储方式不同，TIMESTAMP底层存储的是毫秒值，距离1970-1-1 0:0:0 0毫秒的毫秒值。
* 两个日期比较大小或日期计算时，**TIMESTAMP更方便、更快**。为什么
* TIMESTAMP和时区有关。TIMESTAMP会根据用户的时区不同，显示不同的结果。而DATETIME则只能反映出插入时当地的时区，其他时区的人查看数据必然会有误差的。

### 6.6 开发中的经验

**用得最多的日期时间类型，就是 DATETIME 。**虽然 MySQL 也支持 YEAR（年）、 TIME（时间）、DATE（日期），以及 TIMESTAMP 类型，但是在实际项目中，尽量用 DATETIME 类型。因为这个数据类型包括了完整的日期和时间信息，取值范围也最大，使用起来比较方便。毕竟，如果日期时间信息分散在好几个字段，很不容易记，而且查询的时候，SQL 语句也会更加复杂。

此外，一般存注册时间、商品发布时间等，不建议使用DATETIME存储，而是使用 时间戳 ，因为DATETIME虽然直观，但不便于计算。

## 7.文本字符串类型

MySQL中，文本字符串总体上分为：char、varchar、tinytext、text、mediumtext、longtext、enum、set。

![image-20211127102843003](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127102843003.png)

### 7.1 char和varchar类型

char和varchar类型都可以存储比较短的字符串。下面的图有误，char和varchar中指定的长度是**字符的长度**。在MySQL8的UTF8编码中一个中文字符占3个字节，一个英文字符占1个字节。

![image-20211127103103380](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127103103380.png)

**char类型：**

* CHAR(M) 类型一般需要预先定义字符串长度。如果不指定(M)，则表示长度**默认是1个字符。**
* 如果保存时，数据的实际长度比CHAR类型声明的长度小，则会在 右侧填充 空格以达到指定的长度。当MySQL检索CHAR类型的数据时，CHAR类型的字段会去除尾部的空格。
* 定义char类型字段时，声明的字段长度即为char类型字段所占的存储空间的字节数。

**varchar类型：**

* varchar定义时，必须指定长度M，否则会报错。
* MySQL4.0版本以下，varchar(20)：指的是20字节，如果存放UTF8汉字时，只能存6个（每个汉字3字节） ；MySQL5.0版本以上，varchar(20)：指的是20字符。
* 检索VARCHAR类型的字段数据时，会保留数据尾部的空格。VARCHAR类型的字段所占用的存储空间为字符串**实际长度加1个字节。需要额外的1个或2个字节记录字符串的长度，字符串长度小于255字节使用1个字节记录，否则使用两个字节。**

**char和varchar如何选择：**

![image-20211127104840963](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127104840963.png)

存储信息很短、长度固定的时候使用char更合适。

十分频繁改变的column，因为varchar每次存储都要有额外的计算，得到长度等工作，如果一个非常频繁改变的，那就要有很多的精力用于计算，而这些对于char来说是不需要的。

**对于不同的存储引擎来说：**

* **MyISAM** 数据存储引擎和数据列：MyISAM数据表，最好使用固定长度(CHAR)的数据列代替可变长度(VARCHAR)的数据列。这样使得整个表静态化，从而使 数据检索更快 ，用空间换时间。
* **MEMORY** 存储引擎和数据列：MEMORY数据表目前都使用固定长度的数据行存储，因此无论使用CHAR或VARCHAR列都没有关系，两者都是作为CHAR类型处理的。
* InnoDB 存储引擎，建议使用VARCHAR类型。因为对于InnoDB数据表，内部的行存储格式并没有区分固定长度和可变长度列（所有数据行都使用指向数据列值的头指针），而且**主要影响性能的因素**是数据行使用的存储总量，由于char平均占用的空间多于varchar，所以除了简短并且固定长度的，其他考虑varchar。这样节省空间，对磁盘I/O和数据存储总量比较好。

### 7.2 text类型

在MySQL中，TEXT用来保存文本类型的字符串，总共包含4种类型，分别为TINYTEXT、TEXT、 MEDIUMTEXT 和 LONGTEXT 类型。

**在向TEXT类型的字段保存和查询数据时，系统自动按照实际长度存储，不需要预先定义长度。这一点和VARCHAR类型相同。**

![image-20211127105446665](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127105446665.png)

我目前感觉上图中所写的占用空间的单位应该是字符。但是后来又验证了一下确实是字节数。

![image-20211127105820320](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127105820320.png)

由于实际存储的长度不确定，MySQL不允许Text类型的字段做主键，遇到这种情况就只能使用char和varchar。

![image-20211127110023382](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127110023382.png)

使用char_length函数可以查看数据的字符数，使用length函数可以查看数据的字节数。

![image-20211127110240051](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127110240051.png)

text类型并不会删除数据尾部的空格。

**开发中的经验：**

TEXT文本类型，可以存比较大的文本段，搜索速度稍慢，因此如果不是特别大的内容，建议使用CHAR， VARCHAR来代替。还有TEXT类型不用加默认值，加了也没用。而且text和blob类型的数据删除后容易导致“空洞”，使得文件碎片比较多，所以频繁使用的表不建议包含TEXT类型字段，建议单独分出去，单独用一个表。

## 8.enum类型

ENUM类型也叫作枚举类型，ENUM类型的取值范围需要在定义字段时进行指定。设置字段值时，ENUM类型只允许从成员中选取单个值，不能一次选取多个值。

其所需要的存储空间由定义ENUM类型时指定的成员个数决定

![image-20211127111421460](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127111421460.png)

- 当ENUM类型包含1～255个成员时，需要1个字节的存储空间；
- 当ENUM类型包含256～65535个成员时，需要2个字节的存储空间。
- ENUM类型的成员个数的上限为65535个。

## 9.set类型

SET表示一个字符串对象，可以包含0个或多个成员，但成员个数的上限为 64 。设置字段值时，可以取取值范围内的 0 个或多个值。

也就是说我在开始的时候向这个set类型的字段中初始设定了许多可选的值，在以后的数据中，这个set字段的取值可以从这些初始值中选取，可以选取1个，也可以选取多个，但是我选取的值必须都存在于这些初始值当中。如果我插入的值有重复的，那么重复的值会被删除。

## 10.二进制字符串类型

## 11.JSON类型

## 12.空间类型



# 第13章_约束

## 1.约束的概述（constrain）

关系型数据库系统和文件系统的一个不同点是，关系型数据库本身能保证存储数据的完整性，不需要应用程序的控制，而文件系统一般需要在程序端进行控制。

### 数据完整性的三种形式：

* **实体完整性**保证表中有一个主键。
* **域完整性**保证数据每列的值满足特定的条件。可以使用外键约束，default约束，触发器，check约束来保证
* **参照完整性**保证两张表之间的关系

### 1.1 为什么需要约束

数据完整性（Data Integrity）是指数据的精确性（Accuracy）和可靠性（Reliability）。它是防止数据库中存在不符合语义规定的数据和防止因错误信息的输入输出造成无效操作或错误信息而提出的。

为了保证数据的完整性，SQL规范以约束的方式对表数据进行额外的条件限制。从一下四个方面考虑：

* 实体完整性（Entity Integrity）：例如，同一个表中，不能存在两条完全相同无法区分的记录。
* 域完整性（Domain Integrity）：例如，年龄范围0-120，性别范围“男/女” 
* 引用完整性（referential Integrity）：例如：员工所在部门，在部门表中要能找到这个部门
* 用户自定义完整性（User-defined Integrity）：例如：用户名唯一、密码不能为空等，本部门经理的工资不得高于本部门职工的平均工资的5倍。

### 1.2 什么是约束

约束是表级的强制规定。

可以在建表时规定约束（通过create table语句），或者在表创建之后通过alter table语句规定约束。

### 1.3 约束的分类

* 根据约束数据列的限制：

  * 单列约束：每个约束只约束一列
  * 多列约束：每个约束可以约束多列数据

* 根据约束的作用范围：

  * 列级约束：只能作用在一个列上，跟在列的定义后面
  * 表级约束：可以作用在多个列上，不与列一起，而是单独定义

  ![image-20211127114308063](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127114308063.png)

* 根据约束起的作用：
  * not null 非空约束
  * unique 唯一约束：规定某个字段在整个表中是唯一的
  * primary key 主键（非空切唯一）约束
  * foreign key 外键约束
  * check 检查约束(MySQL不支持，但可以使用，只是没有效果)
  * default 默认值约束

> **注意： MySQL5不支持check约束（MySQL8支持），但可以使用check约束，而没有任何效果**

查看某个表中已有的约束

```sql
#information_schema数据库名（系统库） 
#table_constraints表名称（专门存储各个表的约束） 
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';
```

## 2.非空约束not null

### 2.1注意事项

限定某个字段/某列的值不允许为空

默认所有类型的值都可以是null，包括int float等数据类型。

非空约束只能出现在表对象的列上，只能某个列单独限定非空，不能组合非空。

### 2.2  添加非空约束

1. 建表时

```sql
CREATE TABLE 表名称( 
    字段名 数据类型, 
    字段名 数据类型 NOT NULL, 
    字段名 数据类型 NOT NULL 
);
```

2. 建表后

```sql 
alter table 表名称 modify 字段名 数据类型 not null;
```

![image-20211127115317930](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127115317930.png)

### 2.3 删除非空约束

```sql
alter table 表名称 modify 字段名 数据类型 NULL;
#去掉not null，相当于修改某个非注解字段，该字段允 许为空
alter table 表名称 modify 字段名 数据类型;
#去掉not null，相当于修改某个非注解字段，该字段允许为空
```

## 3.唯一性约束

### 3.1 注意事项

用来限制某个字段/某列的值不能重复

**唯一性约束允许出现多个空值null**

一个表中可以有多个唯一约束。

唯一约束可以是某一个列的值唯一，也可以多个列组合的值唯一。

唯一性约束允许列值为空。

在创建唯一约束的时候，如果不给唯一约束命名，就默认和列名相同。

**MySQL会给唯一约束的列上默认创建一个唯一索引**

### 3.2 添加唯一约束

1. 建表时

```sql 
create table 表名称( 
    字段名 数据类型, 
    字段名 数据类型 unique, 
    字段名 数据类型 unique key, 字段名 数据类型 
);
create table 表名称( 
    字段名 数据类型, 
    字段名 数据类型, 
    字段名 数据类型, 
    [constraint 约束名] unique key(字段名)
);
```

2. 建表之后

```sql
#字段列表中如果是一个字段，表示该列的值唯一。如果是两个或更多个字段，那么复合唯一，即多个字段的组合是唯 一的
#方式1
alter table 表名称 add unique key(字段列表)
#方式2
alter table 表名称 modify 字段名 字段类型 unique;
```

### 3.3 关于复合唯一约束

### 3.4 删除唯一约束

* 添加唯一约束的列上会自动创建唯一索引
* 删除唯一约束只能通过删除唯一索引的方式删除
* 删除的时候需要指定唯一索引名，唯一索引名就是和唯一约束名一样。
* 如果创建唯一约束时没有指定名称，如果是单列，就默认和列名相同，如果是组合列，那么默认和组合列中排在第一个的列名相同，也可以自定义唯一约束名。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127120902078.png" alt="image-20211127120902078" style="zoom:50%;" />

![image-20211127120923239](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127120923239.png)

```sql
ALTER TABLE USER DROP INDEX uk_name_pwd;
```

![image-20211127121208243](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127121208243.png)

**可以通过show index from table_name语句查看表的索引**

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211127121128524.png" alt="image-20211127121128524" style="zoom:50%;" />

## 4.primary key约束

### 4.1 注意事项

* 用来唯一标识表中的一行记录

- 主键约束相当于**唯一约束+非空约束的组合**，主键约束列不允许重复，也不允许出现空值。
- 一个表中最多只能有一个主键约束，建立主键约束可以在列级别上创建，也可以在表级别上创建。
- 主键约束对应着表中的一列或者多列（复合主键）。
- 如果是多列组合的复合主键约束，那么这些列都不允许为空值，并且组合的值不允许重复。
- **MySQL的主键约束名总是Primary，就算自己命名了约束名也没有用。**
- 当创建主键约束时，系统默认会在所在的列或列组合上建立对应的**主键索引**（能够根据主键查询的，就根据主键查询，效率更高）。如果删除主键约束了，主键约束对应的索引就自动删除了。

### 4.2 添加主键约束

1. 建表时指定主键约束

```sql
create table 表名称( 
    字段名 数据类型 primary key, #列级模式 
    字段名 数据类型, 
    字段名 数据类型 
);
create table 表名称( 
    字段名 数据类型, 
    字段名 数据类型, 
    字段名 数据类型, 
    [constraint 约束名] primary key(字段名) #表级模式 
)
```

2. 建表之后添加主键约束

```sql
ALTER TABLE 表名称 ADD PRIMARY KEY(字段列表); 
#字段列表可以是一个字段，也可以是多个字段，如果是多 个字段的话，是复合主键
alter table test_pk modify id int primary key;
```

### 4.3 关于复合主键

### 4.4 删除主键约束

```sql
alter table 表名称 drop primary key;
```

删除主键约束，不需要指定主键名，因为一个表只有一个主键，删除主键约束后，非空约束还存在。不可以通过删除主键索引的方式删除主键约束，删除主键约束之后，主键索引也被删除。

## 5.自增列：AUTO_INCREMENT

某个字段的值自增，auto_increment

### 5.1 注意事项

* **一个表最多只能有一个自增长列**
* 当需要产生唯一标识或顺序值时，可设置自增长。
* **自增长列约束的列必须是键列（主键列、唯一键列）**
* 自增约束的列的数据类型**必须是整数类型**
* **如果自增列指定了0或null，那么这个值会在当前最大值 的基础上自增；如果自增列手动指定了具体值，直接赋值为具体值。**
* 可以设定为负值，默认从1开始自增。

### 5.2 如何指定自增约束

1. 建表时

```sql 
create table 表名称( 
    字段名 数据类型 primary key auto_increment, 
    字段名 数据类型 unique key not null, 
    字段名 数据类型 unique key, 
    字段名 数据类型 not null default 默认值
);
create table 表名称(
    字段名 数据类型 default 默认值 , 
    字段名 数据类型 unique key auto_increment, 
    字段名 数据类型 not null default 默认值,
    primary key(字段名) 
);
```

2. 建表后

```sql
alter table 表名 modify 字段名 数据类型 auto_increment
```

### 5.3 删除自增约束

```sql
alter table ... modify ... .... 
```

### 5.4 MySQL8新特性——自增变量的持久化

在MySQL8之前，自增主键auto_increment的值如果大于max(primary key)+1，**在MySQL重启之后，**会重新设置AUTO_INCREMENT=max(primary key)+1，这种现象在某些情况下会导致业务主键冲突或者其他难以发现的问题。

在MySQL8中将自增主键的计数器持久化到**重做日志**中，每次计数器发证改变，都会将其写入重做日志中，如果数据库重启，Innodb会将重做日志中的信息来初始化计数器的内存值。这也就解释了为什么在之前做实验的时候MySQL自增主键出现断层的现象。

## 6.foreign key约束

限定某个表的某个字段的引用完整性。

### 6.1 主表和从表/父表和子表

主表（父表）：被引用的表，被参考的表

从表（字表）：引用别人的表，参考别人的表

### 6.2 注意事项

1. **从表的外键列，必须参考、引用主表的主键或唯一约束的列。因为被依赖的值必须是唯一的。**
2. 在创建外键约束时，如果不给外键约束名**，默认名不是列名，而是自动产生一个外键名，**也可以自己指定外键约束名
3. 创建外键约束的时候主表必须存在。
4. 删除主表时，如果有外键约束，表是不能被删除的，需要先删除外键约束或者从表，在删除主表。
5. 当主表中的数据被从表参照时，主表的记录将不允许删除，如果删除数据，需要先删除从表中依赖该记录的数据，然后才可以删除主表的数据。
6. 一个表中可以建立多个外键约束。
7. 从表的外键列与主表被参照的列的数据类型必须一样，逻辑意义一致，如果类型不一样，那么建表将不会成功。
8. **当创建外键约束时，系统默认会在外键列上建立对应的普通索引，**但是索引名是外键约束名。（因此根据外键查询的效率会很高）。
9. **删除外键约束后，必须手动删除对应的索引**

### 6.3 添加外键约束

1. 建表时

```sql
create table 从表名称( 
    字段1 数据类型 primary key, 
    字段2 数据类型, 
    [CONSTRAINT <外键约束名称>] FOREIGN KEY（从表的某个字段) references 主表名(被参考字段) 
);
# foreign key : 在表级指定字表中的列
# references ：标识在父表中的列
```

2. 建表后

```sql
LTER TABLE 从表名 ADD [CONSTRAINT 约束名] FOREIGN KEY (从表的字段) REFERENCES 主表名(被引用 字段) [on update xx][on delete xx];
```

### 6.4 表的约束都有哪些

外键约束关系是针对双方的

* 添加了外键约束后，主表的修改和删除数据受约束
* 从表的外键列添加和修改受约束
* 在从表上建立外键，要求主表必须存在
* 删除主表时，要求从表先删除，或者将从表中的外键约束删除。

### 6.5 约束等级

* Cascade方式：在父表上update或delete记录时，同步update或delete字表中匹配记录。
* Set null 方式：在父表update或delete记录时，将字表上匹配记录的列设为null，但是要注意使用这种方式的话，字表的外键列不能为not null
* no action 方式：如果字表中有匹配的数据，不允许对父表对应候选键进行update或delete操作。
* restrict方式：和no action方式一样，都是立即检查外键约束
* set default 方式，父表有变更时，字表将外键列设置为一个默认值，但是innodb不能识别。

如果没有设置等级，就相当于restrict方式。

对于外键约束，最好采用`on update cascade on delete restrict`的方式。

### 6.6 删除外键约束

因为外键索引只能手动删除，不像主键索引如果删除主键那么索引也会删除。所以我们只能手动删除外键索引。

因此如果要删除外键约束，应该先删除外键约束然后删除外键索引。我认为它可能和外键索引是普通索引有关系，应该是普通索引和表级的约束没有关联，所以需要分开删除。

```sql
(1)第一步先查看约束名和删除外键约束 
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';#查看某个表的约束名 
ALTER TABLE 从表名 DROP FOREIGN KEY 外键约束名; 

（2）第二步查看索引名和删除索引。（注意，只能手动删除） 
SHOW INDEX FROM 表名称; #查看某个表的索引名 
ALTER TABLE 从表名 DROP INDEX 索引名;
```

### 6.7 开发场景

键和不建外键约束有什么区别？

答：建外键约束，你的操作（创建表、删除表、添加、修改、删除）会受到限制，从语法层面受到限制。例如：在员工表中不可能添加一个员工信息，它的部门的值在部门表中找不到。

不建外键约束，你的操作（创建表、删除表、添加、修改、删除）不受限制，要保证数据的 引用完整 性 ，只能依 靠程序员的自觉 ，或者是 在Java程序中进行限定 。例如：在员工表中，可以添加一个员工的信息，它的部门指定为一个完全不存在的部门。

**那么建和不建外键约束和查询有没有关系？**

没有关系。

> 在 MySQL 里，外键约束是有成本的，需要消耗系统资源。对于大并发的 SQL 操作，有可能会不适合。比如大型网站的中央数据库，可能会 因为外键约束的系统开销而变得非常慢 。所以， MySQL 允许你不使用系统自带的外键约束，在 应用层面 完成检查数据一致性的逻辑。也就是说，即使你不用外键约束，也要想办法通过应用层面的附加逻辑，来实现外键约束的功能，确保数据的一致性。

### 6.8 阿里开发规范

【强制】不得使用**外键和级联**，一切外键概念必须在应用层解决。

说明：（概念解释）学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外键。如果更新学生表中的 student_id，同时触发成绩表中的 student_id 更新，即为**级联更新**。外键与级联更新适用于 单 机低并发 ，不适合 分布式 、 高并发集群 ；级联更新是强阻塞，存在数据库 更新风暴 的风险；外键影响数据库的 插入速度 。

![image-20211128111453883](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211128111453883.png)

## 7.check约束

检查某个字段的值是否符合要求，一般指的是值的范围。

### 7.1 说明：MySQL5.7不支持

MySQL5.7 可以使用check约束，但check约束对数据验证没有任何作用。添加数据时，没有任何错误或警告

**但是MySQL8.0中可以使用check约束了**

### 7.2 注意事项

check约束不能使用其他的列，只能约束自己。

## 8 default约束

给某个字段/某列指定默认值，一旦设置默认值，在插入数据时，如果此字段没有显式赋值，则赋值为默认值。

### 8.1 如何添加默认约束

1. 建表时

```sql
create table 表名称( 
    字段名 数据类型 primary key, 
    字段名 数据类型 unique key not null, 
    字段名 数据类型 unique key, 
    字段名 数据类型 not null default 默认值
);
# 默认约束一般不在主键列和唯一约束列上添加
```

2. 建表后

```sql
alter table 表名 modify 字段名 数据类型 default 默认值
```

**注意：如果某个字段原来有非空约束，现在如果想要更改默认约束，如果想保留非空约束，那么非空约束也得显示的再次定义出来，否则非空约束会删除。修改非空约束的时候默认约束的变化也是同理**

### 8.2 如何删除默认约束

```sql
alter table ... modify ... ...;
# 再次修改字段并不添加默认约束。
```

## 9.面试

### 建表时，为什么要加not null default ‘’ 或default 0；

不想让表中出现null

### 为什么不想要null值？

1. 不好比较。null是一个特殊的值，比较是只能使用专门的is null 和 isnot null 来比较（或者使用安全等号<=>）。喷到运算符，通常返回null。
2. 效率不高，影响索引效果。
3. NULL 列需要更多的存储空间：需要一个额外字节作为判断是否为 NULL 的标志位。

### 带auto_increment约束的字段的值是从1开始的吗?

在MySQL中，默认AUTO_INCREMENT的初始值是1，每新增一条记录，字段值自动加1。设置自增属性（AUTO_INCREMENT）的时候，还可以指定第一条插入记录的自增字段的值这样新插入的记录的自增字段值从初始值开始递增，如在表中插入第一条记录，同时指定id值为5，则以后插入的记录的id值就会从6开始往上增加。添加主键约束时，往往需要设置字段自动增加属性。

![image-20211128114401181](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211128114401181.png)

### 并不是所有的表都可以任意选择存储引擎

外键约束不能跨引擎使用。MySQL支持多种存储引擎，每一个标都可以制定一个不同的存储引擎，需要注意的是：外键约束是用来保证数据参照完整性的，如果表之间需要关联外键，却制定了不同的存储引擎，那么这些表之间是不能创建外键约束的，所以说，存储引擎的选择也不完全是随意的。

### 约束和索引的区别

当我们创建一个唯一索引就创建了一个唯一约束，创建一个唯一约束就创建一个唯一索引，但是约束和索引的概念还是不同的，约束更像是一种逻辑的概念，用来保证数据的完整性，而索引是一种数据结构，即有逻辑上的概念，还有物理上的概念，在数据库中还代表这物理存储的方式。

# 第14章_视图

## 1.常见的数据库对象

| 对象              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| 表table           | 表是存储数据的逻辑单元，以行和列的形式存在                   |
| 数据字典          | 数据字典就是系统表，存放数据库相关信息的表，系统表的数据通常由数据库系统维护，程序员通常不应该修改，只可查看 |
| 约束constraint    | 执行数据校验的规则，用于保证数据完整性的规则                 |
| 视图view          | 一个或者多个数据表里的数据的逻辑显示，视图并不存储数据       |
| 索引index         | 用于提高查询性能，相当于书的目录                             |
| 存储过程procedure | 用于完成一次完整的业务处理，没有返回值，但是可以通过传出参数将多个值传给调用环境 |
| 存储函数function  | 用于完成一次特定的计算，具有一个返回值                       |
| 触发器trigger     | 相当于一个事件监听器，当数据库发生特定事件后，触发器被触发，完成相应的处理。 |

### 2.视图概述

### 2.1 为什么使用视图

1. 提高重用性，就像一个函数，我可以通过操作视图，方便的获取想要的数据
2. 可以使用视图对数据库进行重构，却不影响程序的运行。例如可以通过视图将一张表拆分为多张表，或者将多张表合为一张表。
3. 提高了安全性，我们可以对不同的用户设定不同的视图。

### 2.2 视图的理解

* 视图是一种**虚拟表**，本身**不具有数据**，占用很少的内存空间，它是SQL中一个重要的概念。
* **视图建立在已有表的基础上**，视图赖以建立的这些表称为**基表**

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211129104037904.png" alt="image-20211129104037904" style="zoom:50%;" />

* 视图的创建和删除只影响视图本身，不影响对应的基表，但是对视图中的数据进行增加、删除和修改操作时，数据表中的数据也会相应的发生变化，对基表中的数据进行增删改时，视图中的数据也会发生变化。
* 向视图提供数据内容的语句为SELECT语句，可以将视图理解为 **存储起来的SELECT语句**
* 视图，是向用户提供基表数据的另一种表现形式。**通常情况下，小型项目的数据库可以不使用视图，**但是在大型项目中，以及数据表比较复杂的情况下，视图的价值就凸显出来了，它可以帮助我们把经常查询的结果集放到虚拟表中，提升使用效率。理解和使用起来都非常方便。

## 3.创建视图

* **在create view语句中嵌入子查询**

```sql
create view [or replace]
[algorithm = {undefined|merge|temptable}]
view 视图名称 [(字段列表)]
as 查询语句
[with [cascaded|local] check option]
```

### 3.1 创建单表视图

![image-20211129105608589](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211129105608589.png)

实际上就是我们在SQL查询语句的基础上分装了视图view，这样就会基于SQL语句的结果集形成一张虚拟表。

在创建视图时，没有在视图名后面指定字段列表，则视图中字段列表默认和select语句中的字段列表一致。如果SELECT语句中给字段取了别名，那么视图中的字段名和别名相同。

### 3.2 创建多表联合视图

![image-20211129110055524](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211129110055524.png)

* **利用视图对数据进行格式化**

我们经常需要输出某个格式的内容，比如我们想输出员工姓名和对应的部门名，对应格式为emp_name(department_name)，就可以使用视图来完成数据格式化的操作：

```sql
CREATE VIEW emp_depart 
AS
SELECT CONCAT(last_name,'(',department_name,')') AS emp_dept FROM employees e JOIN departments d 
WHERE e.department_id = d.department_id
```

### 3.3 基于视图创建视图

当我们创建好一张视图之后，还可以在它的基础上继续创建视图。

## 4.查看视图

1. 查看数据库的表对象、视图对象

```sql
show tables;
```

2. 查看视图的结构

```sql
desc / describe 视图名称
```

3. 查看视图的属性信息

```sql
# 查看视图信息（显示数据表的存储引擎、版本、数据行数和数据大小等）
show table status like '视图名称'\G
```

![image-20211129110812132](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211129110812132.png)

执行结果显示，注释comment为view，说明这个表为视图，这个视图表的其他的信息为null，说明这是一个虚表。

4. 查看视图的详细定义信息

```sql
show create view 视图名称
```

## 5.更新视图的数据

### 5.1 一般情况

MySQL支持使用INSERT、UPDATE和DELETE语句对视图中的数据进行插入、更新和删除操作。当视图中的数据发生变化时，数据表中的数据也会发生变化，反之亦然。

### 5.2 不可更新的视图

要使视图可以更新，视图中的行和底层基本表中的行之间必须存在一对一的关系，另外当视图定义出现一下情况是，视图不支持更新操作。

* 在视图定义的时候指定了“algorithm=temptable”，视图将不支持insert和delete操作。
* 视图中不包含基表中所有被定义为非空但是有没有指定默认值的列，视图将不支持insert操作。如果这样的视图允许insert，那么基表中的那些列将不知道插入什么值。
* 在定义视图的select语句中使用了join联合查询，视图将不支持增删操作。
* 在定义视图的SELECT语句后的字段列表中使用 DISTINCT 、 聚合函数 、 GROUP BY 、 HAVING 、 UNION 等，视图将不支持INSERT、UPDATE、DELETE；
* 在定义视图的SELECT语句中包含了子查询，而子查询中引用了FROM后面的表，视图将不支持 INSERT、UPDATE、DELETE；
* 视图定义基于一个不可更新的视图，使用视图定义视图的情况
* 常量视图。

> 虽然可以更新视图数据，但是总的来说，**视图作为虚拟表，主要用于方便查询，**不建议更新视图的数据。**对视图数据的更改，都是通过对实际数据表里的数据的操作来完成的**

## 6.修改、删除视图

### 6.1 修改视图

1. 使用create **or replace** view 子句修改视图

```sql
CREATE OR REPLACE VIEW empvu80 
(id_number, name, sal, department_id) 
AS
SELECT employee_id, first_name || ' ' || last_name, salary, department_id 
FROM employees 
WHERE department_id = 80;
```

2. alter view

```sql
alter view 视图名称
as 
查询语句
```

### 6.2 删除视图

* 删除视图只是删除视图的定义，并不会删除基表的数据

* 删除视图的语法：

  ```sql
  drop view if exists 视图名
  drop view if exists 视图名1、视图名2
  ```

* 说明：基于视图a、b创建了行的视图c、如果将视图a或者视图b删除，会导致视图c的查询失败，这样的视图c需要手动删除或修改，否则影响使用。

## 7.总结

### 7.1 视图的优点

1. 操作简单

**将经常使用的查询操作定义为视图**，可以使开发人员不需要关心视图对应的数据表的结构、表与表之间的关联关系，也不需要关心数据表之间的业务逻辑和查询条件，而只需要简单地操作视图即可，极大简化了开发人员对数据库的操作。

2. 减少数据冗余（感觉不太对）

视图跟实际数据表不一样，它存储的是查询语句。所以，在使用的时候，我们要通过定义视图的查询语句来获取结果集。而视图本身不存储数据，不占用数据存储的资源，减少了数据冗余。

3. 数据安全

MySQL将用户对数据的 访问限制 在某些数据的结果集上，而这些数据的结果集可以使用视图来实现。用户不必直接查询或操作数据表。这也可以理解为视图具有 隔离性 。视图相当于在用户和实际的数据表之间加了一层虚拟表。

<img src="C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211129113120183.png" alt="image-20211129113120183" style="zoom:50%;" />

4. 适应灵活多变的需求

当业务系统的需求发生变化后，如果需要改动数据表的结构，则工作量相对较大，可以使用视图来减少改动的工作量。这种方式在实际工作中使用得比较多。

5. 能够分解复杂的查询逻辑

数据库中如果存在复杂的查询逻辑，则可以将问题进行分解，创建多个视图获取数据，再将创建的多个视图结合起来，完成复杂的查询逻辑。

### 7.2 视图的不足

如果我们在实际数据表的基础上创建了视图，那么，**如果实际数据表的结构变更了，我们就需要及时对相关的视图进行相应的维护**。特别是嵌套的视图（就是在视图的基础上创建视图），维护会变得比较复杂， 可读性不好 ，容易变成系统的潜在隐患。因为创建视图的 SQL 查询可能会对字段重命名，也可能包含复杂的逻辑，这些都会增加维护的成本。

实际项目中，如果视图过多，会导致数据库维护成本的问题。



# 第15章_存储过程和函数

**MySQL从5.0版本开始支持存储过程和函数**。存储过程和函数能够将复杂的SQL逻辑封装在一起，应用程序无须关注存储过程和函数内部复杂的SQL逻辑，而只需要简单地调用存储过程和函数即可。

## 1.存储过程概述

### 1.1 理解

**含义：**存储过程的英文为 Stored Procedure。它的思想很简单，就是一组经过预先编译的SQL语句的封装。

**执行过程：**存储过程预先存储在MySQL服务器上，需要执行的时候，客户端只需要向服务器端发出调用存储过程的命令，服务端就可以把预先存储好的这一系列SQL语句全部执行。

**优点：**

1. 简化操作，提高了SQL语句的重用性，减少了开发程序员的压力。

2、减少操作过程中的失误，提高效率

3、**减少网络传输量**（客户端不需要把所有的 SQL 语句通过网络发给服务器） 4、**减少了 SQL 语句暴露在网上的风险**，也提高了数据查询的安全性

**和视图、函数对比：**

它和视图有着同样的优点，清晰、安全，还可以减少网络传输量。不过它和视图不同，视图是 虚拟表 ，通常不对底层数据表直接操作，而存储过程是程序化的 SQL，**可以 直接操作底层数据表 ，相比于面向集合的操作方式，能够实现一些更复杂的数据处理。**

一旦存储过程被创建出来，使用它就像使用函数一样简单，我们直接通过调用存储过程名即可。相较于函数，存储过程是 **没有返回值** 的。

### 1.2 分类

存储过程的参数类型可以是in、out、inout。根据这点分类如下：

1、没有参数（无参数无返回值） 2、仅仅带in类型的参数（有参数没有返回值） 3、仅仅带out类型的参数（无参数有返回值） 4、即带in又带out（有参数有返回值） 5、带inout类型的参数（有参数有返回值）

## 2.创建存储过程

### 2.1 语法分析

语法：

```sql
create procedure 存储过程名(IN|OUT|INOUT 参数名 参数类型...)
[characteristics...]
begin
	存储过程体
	
end
```

说明：

1. 参数前面的符号的意思

* in：当前参数为输入参数，也就是表示入参；

  存储过程知识读取这个参数的值，如果没有定义参数种类，默认就是in，表示输入参数

* out：当前参数为输出参数，也就是表示出参；

  执行完成之后，调用这个存储过程的客户端或者应用程序就可以读取这个参数返回的值。

* inout：当前参数既可以为输入参数，亦可以为输出参数。

2. 形参类型可以是MySQL数据库中的任意类型
3. characteristics表示创建存储过程时指定的对存储过程的约束条件，它的取值信息如下：

```sql
LANGUAGE SQL 
| [NOT] DETERMINISTIC 
| { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA } 
| SQL SECURITY { DEFINER | INVOKER } 
| COMMENT 'string'
```

* language sql：说明存储过程执行体是由SQL语句组成的，当前系统支持的语言为SQL。
* [not] deterministic(不可逆转的)：指明存储过程执行的结果是否确定。deterministic表示结果是确定的，每次执行存储过程时，相同的输入会得到相同的输出。not deterministic表示结果是不确定的，相同的输入可能得到不同的输出。如果没有指定任意一个值，默认为not deterministic。
* {contains sql | no sql | reads sql data | modifies sql data}：指明子程序使用SQL语句的限制。
  * contains sql 表示当前存储过程的子程序包含sql语句，但是并不包含读写SQL语句；
  * no sql 表示当前存储过程的子程序中不包含任何sql语句；
  * reads sql data表示包含读取数据的sql语句；
  * modifies sql data表示包含写数据的sql语句
* sql security { definder | invoker}：执行当前存储过程的权限，即指明那些用户能够执行当前存储过程。
  * definer表示只有当前存储过程的创建者或者定义者产能执行当前存储过程
  * Invoker表示拥有当前存储过程的访问权限的用户能够执行当前存储过程
  * 如果没有设置相关值，则MySQL默认指定值为definer
* comment ‘string’：注释信息，用来描述存储过程

4. 存储过程体中可以有多条SQL语句，如果仅仅一条SQL语句，则可以忽略begin和end。编写存储过程并不是一件简单的事情，可能存储过程中需要复杂的 SQL 语句。

```sql
1.BEGIN…END：BEGIN…END 中间包含了多个语句，每个语句都以（;）号为结束符。
2.DECLARE：DECLARE 用来声明变量，使用的位置在于 BEGIN…END 语句中间，而且需要在其他语句使用之前进 行变量的声明。
3.set：赋值语句，用于对变量进行赋值。
4.select...into：把从数据表中查询的结果存放到变量中，也就是为变量赋值
```

5. 需要设置新的结束标记

```sql
delimiter 新的结束标记
```

因为MySQL默认的语句结束符号为分号，为了避免与存储过程中SQL语句结束符相冲突，需要使用delimiter改变存储过程的结束符。

当时用delimiter命令时，应该避免使用反斜杠“\”字符，因为反斜杠是MySQL的转义字符。

实例：

```sql
DELIMITER $ 
CREATE PROCEDURE 存储过程名(IN|OUT|INOUT 参数名 参数类型,...) [characteristics ...] 
BEGIN
	sql语句1; 
	sql语句2; 

END $
```

## 3.调用存储过程

### 3.1 调用格式

存储过程有多种调用方法。存储过程必须使用call语句调用，并且存储过程和数据库相关，如果要执行其他数据库中的存储过程，需要指定数据库名称，例如CALL dbname.procname。

```sql
call 存储过程名(实参列表)
```

**格式：**

1、调用in模式的参数

```sql
call sp1('值')
```

2、调用out模式的参数

```sql
set @name
call sp1(@name)
select @name
```

3、调用inout模式的参数

```sql
set @name=value
call sp1(@name)
select @name
```

举例：创建存储过程，实现累加计算，计算1+2+。。。+n等于多少。

```sql
DELIMITER // 
CREATE PROCEDURE `add_num`(IN n INT) 
BEGIN 
	DECLARE i INT; 
	DECLARE sum INT; 
	
	SET i = 1; 
	SET sum = 0; 
	WHILE i <= n DO 
		SET sum = sum + i; 
		SET i = i +1; 
	END WHILE; 
	SELECT sum; 
END // 
DELIMITER ;
```

如果使用的是Navicat工具，那么在编写存储过程的时候，Navicat会自动设置delimiter为其他符号，我们不需要在进行delimiter操作。

## 4.存储函数的使用

MySQL支持自定义函数，定义好之后，调用方式与调用MySQL预定义的系统函数一样。

### 4.1 语法分析

语法格式：

```sql
CREATE FUNCTION 函数名(参数名 参数类型,...) 
RETURNS 返回值类型 
[characteristics ...] 
BEGIN函数体 
	#函数体中肯定有 RETURN 语句 

END
```

说明：

1、参数列表：指定参数为IN、OUT或INOUT只对PROCEDURE是合法的，FUNCTION中总是默认为IN参数。

2、returns type语句表示函数返回数据的类型；

3、characteristic创建函数时指定的对函数的约束。取值与创建存储过程时的相同。

4、函数体也可以用begin。。end来表示SQL代码的开始和结束，如果函数体只有一条语句，也可以省略begin。。。end。

### 4.2 调用存储函数

在MySQL中，存储函数的使用方法与MySQL内部函数的使用方法是一样的。换言之，用户自己定义的存储函数与MySQL内部函数是一个性质的。区别在于，存储函数是 **用户自己定义** 的，而内部函数是MySQL 的 **开发者定义** 的。

```sql
select 函数名(实参列表)
```

### 4.3 存储函数和存储过程对比

![image-20211130094612350](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130094612350.png)

存储函数可以放在查询语句中使用，存储过程不行，反之，存储过程的功能更加强大，包括能够对表进行的操作（创建表、删除表）和事务操作，这些功能是存储函数不具备的。

## 5.存储过程和函数的查看、修改、删除

### 5.1 查看

MySQL存储了存储过程和函数的状态信息，用户可以使用show status和show create语句来查看，也可以直接从系统的information_schema数据库中查询。

1、使用show screate语句查看存储过程和函数的创建信息

```sql
show create {procedure | function}
```

2、使用show status语句查看存储过程和函数的状态信息

```sql
show {procedure | function} status [like 'pattern']
```

这个语句返回子程序的特征，如数据库、名字、类型、创建者以及创建和修改日期。

[LIKE 'pattern']：匹配存储过程或函数的名称，可以省略。当省略不写时，会列出MySQL数据库中存在的所有存储过程或函数的信息。 

3、从information_schema.Routines表中查看存储过程和函数信息

MySQL中存储过程和函数的信息存储在information_schema数据库下的Routines表中。可以通过查询该表的记录来查询存储过程和函数的信息。其基本语法形式如下：

```sql
select * from information_schema.Routines
where ROUTINE_NAME='存储过程或函数名'[and ROUTINE_TYPE={'procedure|function'}]
```

说明：如果在MySQL数据库中存在存储过程和函数名称相同的情况，最好指定ROUTINE_TYPE查询条件来指明查询的是存储过程还是函数。

### 5.2 修改

修改存储过程或函数，不影响存储过程或函数功能，只是修改相关特性，使用alter。

```sql
alter {procedure|function} 存储过程或函数名 [characteristic]
```

其中，characteristic指定存储过程或函数的特性，其取值信息和创建存储过程和函数时的信息略有不同，

```sql
{contains sql | no sql | reads sql data | modifies sql data}
| sql security {definer | invoker}
| comment 'string'
```

* contains sql 表示程序包含SQL，但是不包含读或写数据的语句
* no sql 表示程序中不包含SQL语句
* reads sql data 表示程序中包含读数据的语句
* modifies sql data 表示程序中包含写数据的语句
* sql security {definer|invoker} 指明谁又权限来执行
  * definer，表示只有定义者自己能执行
  * Invoker，表示授权了的用户可以执行（表示调用者可移执行）
* comment 注释

### 5.3 删除

```sql
DROP {PROCEDURE | FUNCTION} [IF EXISTS] 存储过程或函数的名
```

### 6.关于存储过程使用的争议

### 6.1 优点

1、一次编译多次使用。存储过程只在创建时进行编译，之后的是由都不需要重新编译。提高了SQL的执行效率

2、减少开发工作量，将代码 封装 成模块，实际上是编程的核心思想之一，这样可以把复杂的问题拆解成不同的模块，然后模块之间可以 重复使用 ，在减少开发工作量的同时，还能保证代码的结构清晰

3、存储过程安全性强。代码不用暴露在网络中

4、减少网络传输量，调用存储过程，只需要一次连接一次传输

### 6.2 缺点

1、可移植性差，储存过程不能跨数据库移植

2、调试困难

3、存储过程的版本管理困难。比如数据表索引发生变化，可能会导致存储过程失效，我们在开发软件的时候往往需要进行版本管理，但是存储过程本身没有版本控制，版本迭代更新的时候很麻烦。

4、不适合高并发的场景。高并发的场景需要减少数据库的压力，**有时数据库会采用分库分表的方式**，而且对可扩展性要求很高，在这种情况下，存储过程会变得难以维护， 增加数据库的压力 ，显然就不适用了。



# 第16章_变量、流程控制和游标

## 1.变量

在MySQL数据库的存储过程和函数中，可以使用变量来存储查询或计算的中间结果数据，或者输出最终的结果数据。

在MySQL数据库中，变量分为**系统变量**以及**用户自定义变量**

### 1.1 系统变量

#### 1.1.1 系统变量的分类

变量由系统定义，不是用户定义，属于**服务器**层面。启动MySQL服务（就是Linux或者Windows后台运行的MySQL服务），生成MySQL服务实例期间，MySQL将为MySQL服务内存中的系统变量赋值，这些系统变量定义了当前MySQL服务实例的属性、特征。这些系统变量的值要么是编译MySQL时参数的默认值，要么是配置文件（例如my.ini等）中的参数值。

系统变量分为**全局系统变量**（需要添加global关键字）以及**会话系统变量**（需要添加session关键字），有时也把全局系统变量简称为全局变量，有时也把会话系统变量称为local变量。**如果不写，默认会话级别**。静态变量（在MySQL服务实例运行期间他们的值不能使用set动态修改）属于特殊的全局系统变量。

![image-20211130105606555](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130105606555.png)

* 全局系统变量针对于所有会话（连接）有效，但是重启后就会失效
* 会话系统变量仅针对于当前会话（连接）有效。会话期间，当前会话对某个会话系统变量值的修改，不会影响其他会话同一个会话系统变量的值。
* 会话1对某个全局系统变量值的修改会导致会话2中同一个全局系统变量值的修改。

在MySQL中有些系统变量**只能是全局的**，例如 max_connections 用于限制服务器的最大连接数；有些系统变量作用域**既可以是全局又可以是会话**，例如 character_set_client 用于设置客户端的字符集；有些系统变量的作用域**只能是当前会话**，例如 pseudo_thread_id 用于标记当前会话的 MySQL 连接 ID。

#### 1.1.2 查看系统变量

* 查看所有或部分系统变量

```sql
# 查看所有全局变量
show global variables
# 查看所有会话变量
show session variables
或
show variables # 不写默认为会话
```

```sql
# 查看满足条件的部分系统变量
show global variables like '%表示符%'

# 查看满足条件的部分会话变量
show session variables likt '%表示符%'
```

* 查看指定系统变量

作为MySQL编码规范，MySQL中的系统变量以两个@开头，其中“@@global”仅仅用于标记全局变量，@@session仅用于标记会话系统变量，@@首先标记会话系统变量，如果会话系统变量不存在，则标记全局系统变量

![image-20211130111820093](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130111820093.png)

```sql
select @@global.变量名
select @@session.变量名
select @@变量名
```

* 修改系统变量的值

有些时候，数据库管理员需要修改系统变量的默认值，以便修改当前会话或者MySQL服务实例的属性、特征。

方式1：修改MySQL配合文件，继而修改MySQL系统变量的值（该方法需要重启MySQL服务）

方式2：在MySQL服务运行期间，使用set命令重新设置系统变量的值。

```sql
# 为某个系统变量赋值
# 方式1：
set @@global.变量名=变量值
# 方式2：
set global 变量名=变量值

# 为某个会话变量赋值
# 方式1：
set @@session.变量名=变量值；
# 方式2：
set session 变量名=变量值
```

### 1.2 用户变量

#### 1.2.1 用户变量分类

用户变量是用户自己定义的，作为MySQL编码规范，用户变量以一个@开头，根据作用范围不同，又分为**会话用户变量**和**局部变量**。

* 会话用户变量：作用域和会话变量一样，只对当前连接会话有效。
* 局部变量：只在begin和end语句块中有效，局部变量只能在存储过程和函数中使用。

#### 1.2.2 会话用户变量

* 变量的定义

```sql
# 方式1：“=或：=”
set @用户变量 = 值
set @用户变量 := 值

# 方式2：“:=”或into关键字
select @用户变量 := 表达式 [from 等子句]
select 表达式 into @用户变量 [from 等字句]
```

* 查看用户变量的值（查看、比较、运算等）

```sql
select @用户变量
```

#### 1.2.3 局部变量

定义：可以使用declare语句定义一个局部变量

作用域：仅仅在定义它的begin...end中有效

位置：只能放在begin...end中，而且只能放在第一句。

```sql
begin
	# 声明局部变量
	declare 变量1 变量类型 [default 默认值]
	declare 变量2，变量3...变量类型 [default 默认值]
	
	#为局部变量赋值 
	SET 变量名1 = 值; 
	SELECT 值 INTO 变量名2 [FROM 子句]; 
	#查看局部变量的值 
	SELECT 变量1,变量2,变量3;
	
end
```

1、定义局部变量

如果没有default子句，初始值默认为null

2、变量赋值

![image-20211130114030827](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130114030827.png)

3、使用变量（查看、比较、运算等）

select 局部变量名

#### 1.2.4对比会话变量与局部变量

![image-20211130114358849](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211130114358849.png)

## 2.定义条件与处理程序

**定义条件**是事先定义程序执行过程中可能遇到的问题，**处理程序**定义了在遇到问题时应该 采取的处理方式，并且保证存储过程或函数在遇到警告或错误时能继续执行。这样可以增强存储程序处理问题的能力，避免程序异常停止运行。

定义条件和处理程序在存储过程、存储函数中都是支持的。

如果没有定义条件和处理程序，如果在存储过程和存储函数中遇到错误，程序会报错退出，不会继续运行下面的程序。

### 2.1 定义条件

**定义条件**就是给MySQL中的错误码命名，这有助于存储的程序代码更清晰。他将一个错误名字和指定错误条件关联起来。这个名字可以随后被用在**定义处理程序的**declare handler语句中。

定义条件使用declare语句：

```sql
declare 错误名称 condition for 错误码（或错误条件）
```

错误码说明：

* `MySQL_error_code`和`sqlstate_value`都可以表示MySQL的错误。
  * MySQL_error_code 是数值类型错误代码
  * sqlstate_value 是长度为5的字符串类型错误代码
* 例如，在ERROR 1418 (HY000)中，1418是MySQL_error_code，'HY000'是sqlstate_value。
* 在ERROR 1142（42000）中，1142是MySQL_error_code，'42000'是sqlstate_value。

举例1：定义“Field_Not_Be_NULL”错误名与MySQL中违反非空约束的错误类型是“ERROR 1048 (23000)”对应。

```sql
# 使用MySQL_error_code
declare Field_Not_Be_NULL condition for 1048

# 使用sqlstate_value
declare Field_Not_Be_NULL condition for sqlstate '23000';
```

举例2：定义"ERROR 1148(42000)"错误，名称为command_not_allowed。 

```sql
declare command_not_allowed condition for 1448;
declare command_not_allowed condition for sqlstate '42000'
```

### 2.2 定义处理程序

可以为SQL执行过程中发生某种类型的错误定义特殊的处理程序。定义处理程序时，使用declare语句的语法如下：

```sql
declare 处理方式 handler for 错误类型 处理语句
```

* 处理方式：处理方式有三个取值
  * continue：表示遇到错误不处理，继续执行。
  * exit：表示遇到错误马上退出。
  * undo：表示遇到错误后撤回之前的操作，MySQL中暂时不支持这样的操作
* 错误类型（即条件）可以有如下取值：
  * SQLSTATE ‘字符串错误码’：表示长度为5的sqlstate_value类型的错误码
  * MySQL_error_code：匹配数值类型错误码
  * 错误名称：表示declare...condition定义的错误条件的名称
  * SQL_WARING：匹配所有以01开头的SQLSTATE错误代码
  * NOT FOUNT ：匹配所有以02开头的SQLSTATE错误代码
  * SQL_EXCEPTION：匹配所有没有被SQL_WARING或NOT FOUNT 驳货的SQLSTATE错误代码
* 处理语句：如果出现上述条件之一，则采用对应的处理方式，并执行指定的处理语句。语句可以是像“set 变量= 值”这样的简单语句，也可以是使用begin...end编写的复合语句。

定义处理程序的几种方式：

```sql
# 方式1：捕获sqlstate_value
declare continue handler for sqlstate '42s02' set @info = 'NO_SUCH_TABLE'

#方法2：捕获mysql_error_value 
DECLARE CONTINUE HANDLER FOR 1146 SET @info = 'NO_SUCH_TABLE';

#方法3：先定义条件，再调用 
DECLARE no_such_table CONDITION FOR 1146; DECLARE CONTINUE HANDLER FOR NO_SUCH_TABLE SET @info = 'NO_SUCH_TABLE'; 
#方法4：使用SQLWARNING 
DECLARE EXIT HANDLER FOR SQLWARNING SET @info = 'ERROR'; 
#方法5：使用NOT FOUND 
DECLARE EXIT HANDLER FOR NOT FOUND SET @info = 'NO_SUCH_TABLE'; 
#方法6：使用SQLEXCEPTION 
DECLARE EXIT HANDLER FOR SQLEXCEPTION SET @info = 'ERROR';
```

## 3.流程控制

只要是执行的程序，流程控制就分为三类：

* 顺序结构：程序从上往下依次执行
* 分支结构
* 循环结构

针对于MySQL 的流程控制语句主要有 3 类。注意：只能用于存储程序。

* 条件判断语句：if语句和case语句
* 循环语句：loop、while、repeat语句
* 跳转语句：iterate、和leave语句

### 3.1 分支结构 if

```sql
IF 表达式1 THEN 操作1 
[ELSEIF 表达式2 THEN 操作2]…… 
[ELSE 操作N] 
END IF
```

在begin end中使用

### 3.2 分支结构 case

case 分支结构在select语句中也能使用

```sql
#情况一：类似于switch 
CASE 表达式 
WHEN 值1 THEN 结果1或语句1(如果是语句，需要加分号) 
WHEN 值2 THEN 结果2或语句2(如果是语句，需要加分号) ... 
ELSE 结果n或语句n(如果是语句，需要加分号) 
END [case]（如果是放在begin end中需要加上case，如果放在select后面不需要）
```

case 语句的语法结构2

```sql
#情况二：类似于多重if 
CASE 
WHEN 条件1 THEN 结果1或语句1(如果是语句，需要加分号) 
WHEN 条件2 THEN 结果2或语句2(如果是语句，需要加分号) ... 
ELSE 结果n或语句n(如果是语句，需要加分号) 
END [case]（如果是放在begin end中需要加上case，如果放在select后面不需要）
```

### 3.3 循环结构 Loop

LOOP循环语句用来重复执行某些语句。LOOP内的语句一直重复执行直到循环被退出（使用**LEAVE子句**），跳出循环过程。

```sql
[loop_label:] LOOP 
	循环执行的语句 
END LOOP [loop_label]
```

举例：id值小于10时将重复执行循环过程。

```sql
DECLARE id INT DEFAULT 0; 
add_loop:LOOP 
	SET id = id +1; 
	IF id >= 10 THEN LEAVE add_loop; 
	END IF; 

END LOOP add_loop;
```

### 3.4 循环结构 while

```sql
DELIMITER // 
CREATE PROCEDURE update_salary_while(OUT num INT) BEGIN
	DECLARE avg_sal DOUBLE ; 
	DECLARE while_count INT DEFAULT 0; 
	
	SELECT AVG(salary) INTO avg_sal FROM employees; 
	
	WHILE avg_sal > 5000 DO 
		UPDATE employees SET salary = salary * 0.9;
		SET while_count = while_count + 1; 
		SELECT AVG(salary) INTO avg_sal FROM employees; 
	END WHILE; 
	
	SET num = while_count; 
END // 

DELIMITER ;
```

### 3.5 循环结构 repeat

```sql
DELIMITER // 

CREATE PROCEDURE update_salary_repeat(OUT num INT) 
BEGIN
	DECLARE avg_sal DOUBLE ; 
	DECLARE repeat_count INT DEFAULT 0; 
	
	SELECT AVG(salary) INTO avg_sal FROM employees; 		REPEAT
		UPDATE employees SET salary = salary * 1.15; 
	
		SET repeat_count = repeat_count + 1; 
		SELECT AVG(salary) INTO avg_sal FROM employees; 	UNTIL avg_sal >= 13000 
	END REPEAT; 
	SET num = repeat_count; 

END // 

DELIMITER ;
```

### 三种循环对比

1、三种循环都可以省略名称，但是如果循环中添加了控制语句（leave或iterate）则必须添加名称。

2、Loop：一般用于实现简单的死循环，while：先判断后执行；repeat：先执行后判断，没有停止条件的话至少执行一次。

### 3.6 跳转语句 leave语句（break）

LEAVE语句：可以用在循环语句内，或者以 BEGIN 和 END 包裹起来的程序体内，表示跳出循环或者跳出程序体的操作。如果你有面向过程的编程语言的使用经验，你可以把 LEAVE 理解为 break。

```sql
leave 标记名
```

### 3.7 跳转语句 iterate语句（continue）

ITERATE语句：只能用在循环语句（LOOP、REPEAT和WHILE语句）内，表示重新开始循环，将执行顺序转到语句段开头处。如果你有面向过程的编程语言的使用经验，你可以把 ITERATE 理解为 continue，意思为“再次循环”。

```sql
iterate label
```

## 4.游标

### 4.1 什么是游标（光标）

虽然我们也可以通过筛选条件 WHERE 和 HAVING，或者是限定返回记录的关键字 LIMIT 返回一条记录，但是，却无法在结果集中像指针一样，向前定位一条记录、向后定位一条记录，或者是 随意定位到某一 条记录 ，并对记录的数据进行处理。

这个时候，就可以用到游标。游标，提供了一种灵活的操作方式，让我们能够对结果集中的每一条记录进行定位，并对指向的记录中的数据进行操作的数据结构。**游标让** **SQL** **这种面向集合的语言有了面向过程开发的能力。**

在 SQL 中，游标是一种临时的数据库对象，可以指向存储在数据库表中的数据行指针。这里游标 **充当了 指针的作用** ，我们可以通过操作游标来对数据行进行操作。

**MySQL中游标可以在存储过程和函数中使用。**

### 4.2 使用游标步骤

游标必须在声明处理程序之前被声明，并且变量和条件害必须在声明游标或处理程序之前被声明。

如果想要使用游标，一般需要四个步骤。

第一步，声明游标：

```sql
declare cursor_name cursor for select_statement
```

要使用 SELECT 语句来获取数据结果集，而此时还没有开始遍历数据，这里 select_statement 代表的是SELECT 语句，返回一个用于创建游标的结果集

第二步，打开游标

```sql
open cursor_name
```

当我们定义好游标之后，如果想要使用游标，必须先打开游标。**打开游标的时候 SELECT 语句的查询结果集就会送到游标工作区**，为后面游标的 逐条读取 结果集中的记录做准备。

第三步，使用游标（从游标中获得数据）

```sql
fetch cursor_name into var_name [,var_name]....
```

这句的作用是使用 cursor_name 这个游标来读取当前行，并且将数据保存到 var_name 这个变量中，游标指针指到下一行。如果游标读取的数据行有多个列名，则在 INTO 关键字后面赋值给多个变量名即可。

注意：var_name必须在声明游标之前就定义好。

注意：**游标的查询结果集中的字段数，必须和into后面的变量数一致**，否则，在存储过程执行的时候会报错。

第四步：关闭游标

```sql
close cursor_name
```

有 OPEN 就会有 CLOSE，也就是打开和关闭游标。当我们使用完游标后需要关闭掉该游标。因为游标会占用系统资源 ，如果不及时关闭，**游标会一直保持到存储过程结束**，影响系统运行的效率。而关闭游标的操作，会释放游标占用的系统资源。

### 4.3 举例

创建存储过程“get_count_by_limit_total_salary()”，声明IN参limit_total_salary，DOUBLE类型；声明OUT参数total_count，INT类型。函数的功能可以实现累加薪资最高的几个员工的薪资值，直到薪资总和达到limit_total_salary参数的值，返回累加的人数给total_count。 

```sql
DELIMITER // 
CREATE PROCEDURE get_count_by_limit_total_salary(IN limit_total_salary DOUBLE,OUT total_count INT) 
BEGIN
	DECLARE sum_salary DOUBLE DEFAULT 0; #记录累加的总工资
    DECLARE cursor_salary DOUBLE DEFAULT 0; #记录某一个工资值 
    DECLARE emp_count INT DEFAULT 0; #记录循环个数 
    #定义游标 
    DECLARE emp_cursor CURSOR FOR SELECT salary FROM 
    	employees ORDER BY salary DESC; 
    #打开游标 
    OPEN emp_cursor; 
    REPEAT
    	#使用游标（从游标中获取数据） 
    	FETCH emp_cursor INTO cursor_salary; 
    	SET sum_salary = sum_salary + cursor_salary; 
    	SET emp_count = emp_count + 1; 
    UNTIL sum_salary >= limit_total_salary;
    END REPEAT; 
    SET total_count = emp_count; 
    #关闭游标
    CLOSE emp_cursor; 
END // 
DELIMITER ;
```

### 4.5 总结

游标是 MySQL 的一个重要的功能，为 逐条读取 结果集中的数据，提供了完美的解决方案。跟在应用层面实现相同的功能相比，游标可以在存储程序中使用，效率高，程序也更加简洁。

但同时也会带来一些性能问题，比如在使用游标的过程中，会对数据行进行 加锁 ，这样在业务并发量大的时候，不仅会影响业务之间的效率，还会 消耗系统资源 ，造成内存不足，这是因为游标是在内存中进行的处理。

## MySQL8新特性——全局变量的持久化

在MySQL数据库中，全局变量可以通过Set global语句来设置，但是使用set global语句设置的变量只会临时生效，数据库重启后，服务器又会从MySQL配置文件中读取变量的默认值，MySQL8版本新增了set persist命令。

```sql
set persist global max_connections = 1000
```

MySQL会将该命令的配置文件保存到数据目录下的mysqld-auto.cnf文件中，下次启动时，会读取该文件，其中的配置覆盖默认的配置文件。

