## MySQL

### 数据库分类

1. 关系型数据库(SQL)

​	MySQL、Oracle、SQL Server、DB2……

​	通过表和表之间，行和列之间的关系进行数据的存储

2. 非关系型数据库(NoSQL) Not Only

​	Redis、MongDB

​	非关系型数据库存储对象，通过对象自身的属性来决定

​	**DBMS(数据库管理系统)**

​		数据库的管理软件，科学有效的管理数据。维护和获取数据

### 操作数据库

#### 基本命令

1. 创建数据库

   ```sql
   CREATE DATABASE [IF NOT EXISTS] TEST;
   ```

2. 删除数据库

   ```sql
   DROP DATABASE [IF NOT EXISTS] TEST;
   ```

3. 使用数据库

   ```sql
   -- tab键的上面，如果表名或者字段名是一个特殊字符，就可以加上``
   USE `TEST`
   ```

4. 查看数据库

   ```sql
   SHOW DATABASES --查看所有的数据库
   ```

5. 建表语句

   ```sql
   CREATE TABLE IF NOT EXISTS `student`(
   	`id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
       `name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
       `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱'，
       PRIMARY KEY(`id`)
   )ENGINE=INNODB DEFAULT CHARSET=utf8
   ```

6. 常用命令

   ```sql
   SHOW CREATE DATABASE `test` -- 查看创建数据库的语句
   SHOW CREATE TABLE `student` -- 查看创建表的数据库语句
   DESC `student` -- 显示表的结构
   ```

7. 设置数据库表的字符编码集

   ```sql
   CHARSET=utf8
   ```

   不设置的话，就会采用MySQL默认的字符集编码格式（不支持中文！）

   MySQL 默认编码是 Latin1，不支持中文

8. 表操作

   ```sql
   ALTER TABLE `TABLE_NAME` RENAME AS `新表名`	-- 修改表的名称
   ALTER TABLE `TABLE_NAME` ADD `字段名` 字段类型		-- 添加字段
   ALTER TABLE `TABLE_NAEM` MODIFY `字段名` 字段类型		-- 修改字段类型和约束，不能修改字段名
   ALTER TABLE `TABLE_NAME` CHANGE `旧字段名` `新字段名`	-- 修改字段名、字段类型、约束
   ALTER TABLE `TABLE_NAME` DROP `字段名`		-- 删除字段
   DROP TABLE IF EXISTS `表名`		-- 删除表
   ```

   

#### 数据库的列类型

> 数值

- tinyint	十分小的数据	1个字节
- smallint  较小的数据       2个字节
- mediumint   中等大小的数据   3个字节
- **int   标准的整数   4个字节    常用的整形数据**
- bigint   较大的数据   8个字节
- float   浮点数   4个字节
- double   浮点数   8个字节(存在精度问题)
- **decimal   字符串形式的浮点数   金融计算**

> 字符串

- char		字符串固定大小		0 ~ 255
- **varchar	可变字符串				0 ~ 65535			常用的变量**
- tinytext	微型文本					2^8 - 1
- **text			文本串					2^16 - 1      保存大文本**

> 时间日期

- date   YYYY-MM-DD    日期
- time    HH : mm : ss       时间格式
- **datetime    YYYY-MM-DD HH : mm : ss 最常用的时间格式**
- **timestamp   时间戳    1970.1.1 到现在的毫秒数**

> null

- 没有值，未知

- **== 注意，不要使用NULL进行运算，结果为NULL**

#### 数据库的字段属性

**unsigned : **

- 无符号整数
- 声明该列不能为负数

**zerofill : **

- 用0来填充
- 不足的位数使用0来填充, int(3) 005

**自增 : **

- 自动在上一条记录的基础上加一
- 通常用来设计主键，必须是整数类型
- 可以自定义设计主键自增的起始值和步长

**非空 ：**

- 假设设置为NOT NULL，如果不填写值，会报错

### 数据库存储引擎

#### InnoDB 和 MYISAM

|              | MYISAM | INNODB                 |
| ------------ | ------ | ---------------------- |
| 事务支持     | 不支持 | 支持                   |
| 锁           | 表锁   | 行锁和表锁             |
| 外键         | 不支持 | 支持                   |
| 全文索引     | 支持   | 不支持                 |
| 表空间的大小 | 较小   | 较大，约为NYISAM的两倍 |

常规使用操作 :

- MYISAM   节约空间，速度较快
- INNODB   安全性较高，支持事务，多表多用户操作

#### 数据库在物理空间的存储位置

所有的数据库文件都存在data目录下，本质还是文件的储存

MySQL引擎在物理文件中的区别

- INNODB 的数据库表中只有一个 .frm 文件，以及上级目录中的 ibdata1 文件

- MYISAM 对应文件
  * *.frm   表结构的定义文件
  * *.MYD   数据文件 (data)
  * *.MYI     索引文件

### MySQL 数据管理

#### 外键

> 方式一 : 在创建表的时候添加外键

```sql
KEY `FK_XXX` (`字段名`)		-- 定义外键
CONSTRAINT `FK_XXX` FOREIGN KEY (`字段名`) REFERENCES `表名` (`字段名`)  			--添加约束
```

**删除有外键关系的表的时候，必须要先删除引用别人的表，再删除被引用的表(主表)**

> 方式二 : 给已经创建好的表添加外键 

```sql
ALTER TABLE `表名`
ADD CONSTRAINT `FK_XXX` FOREIGN KEY(`副表字段名`) REFERENCES `主表名` (`主表字段名`)
```

**实际情况 : **

- 数据库就是单纯的表，只用来存放数据，不推荐使用外键

- 一般在程序中实现外键约束

- 阿里的 java 规范中规定

  > **【强制】 不得使用外键与级联，一切外键概念必须在应用层解决**

#### DML(Data Manipulation Language)

- insert 
- update
- delete

>  insert (添加) :

```sql
-- 如果不写字段名，那么数据库一一匹配
insert into 表名(字段名,[字段名]...) values('值1',['值2']...);
-- 可以同时插入多行
insert into 表名(字段名) values('值1'),('值2')...
```

> update (修改) :

```sql
update `表名` set `字段名`='xxx'[,`字段名`='xxx',`字段名`='xxx'] [where 约束条件];
```

where 子句

| 操作符              | 含义     | 例子              | 结果  |
| ------------------- | -------- | ----------------- | ----- |
| =                   | 等于     | 5=8               | false |
| <> 或 !=            | 不等于   | 6<>5              | true  |
| >                   | 大于     | 5>9               | false |
| <                   | 小于     | 5<9               | true  |
| <=                  | 小于等于 | 5<=5              | true  |
| >=                  | 大于等于 | 6>=6              | true  |
| between ... and ... | 之间     | 2 between 3 and 4 | false |
| and                 | 与       | 1<2 and 3<2       | false |
| or                  | 或       | 1>2 or 1<2        | true  |

> delete (删除) :

```sql
-- 清除某些行的数据，表结构和索引约束不变
delete from `表名` [where 约束条件]
```

> truncate (清空数据库) :

```sql
-- 完全清空一个数据库表，表的结构和索引约束不会变
truncate `表名`
```

> truncate 和 delete ：

- 相同点 : 都能删除数据，都不会影响表结构
- 不同点 ：
  - truncate 重新设置自增列，计数器会归零
  - truncate 不会影响事务

#### DQL(Data Query Language)

- 所有的查询操作
- ==数据库最核心的语言==

- 使用频率最高

> 查询所有字段

```sql
select * from `表名` [where 约束条件]
```

> 查询指定字段

```sql
select `字段名`,[`字段名`]... from `表名` [where 约束条件]
```

> 给字段起别名

```sql
select `字段名` as `别名`,[...] from `表名` [where 约束条件]
```

> 去重

```sql
select distinct `字段名` from `表名` 	-- 会自动去掉重复数据
```

> 特殊查询

```sql
select version()		-- 查询系统版本(函数)
select 100*3-1 as 计算结果		-- 用来计算(表达式)
select @@auto_increment_increment		-- 查询自增的步长(变量)
```

select 后面可以跟的表达式 : ==文本值，列，NULL，函数，计算表达式，系统变量……==

where 条件子句

- 作用 ：查询数据中符合条件的字段

> 逻辑运算符

| 运算符   | 语法               | 描述                               |
| -------- | ------------------ | ---------------------------------- |
| and  &&  | a and b      a&&b  | 逻辑与，两个都为真，结果为真       |
| or  \|\| | a or b      a\|\|b | 逻辑或，只要有一个为真，结果就为真 |
| not  !   | not a       !a     | 真为假，假为真                     |

> 模糊查询，比较运算符

| 运算符      | 语法               | 描述                               |
| ----------- | ------------------ | ---------------------------------- |
| is null     | a is null          | 如果操作符为null,则结果为真        |
| is not null | a is not null      | 如果操作符不为null,则结果为真      |
| between and | a between b and c  | 若a在b和c之间，则结果为真          |
| **like**    | a like b           | SQL匹配，如果a匹配b，则结果为真    |
| **in**      | a in (a1,a2,a3...) | 如果a在a1,a2,a3...内，那么结果为真 |

==模糊查询通配符(用在like后面)==

- % ：表示所有，不限数量
- _ ：表示任意一个字符

**联表查询**

![Image text](https://github.com/leZviii/My-learning-experience-in-java/blob/main/picture/Multi_tableQuery.png)

> 自连接self join

- 核心就是将一张表看作多张表

**分页和排序**

> 排序 order by

- 升序 ASC (默认)
- 降序 DESC

> 分页 limit

- 缓解数据库压力
- 给用户的体验更好
- limit (n - 1) * pageSize,pageSize
- n : 当前页数；pageSize : 页面大小

#### MySQL 函数

> 常用函数

```sql
select abs()		--绝对值
select ceiling()	--向上取整
select floor()		--向下取整
select rand()		--返回一个0-1之间的随机数
select sign()		--判断一个数的符号 0 : 0;正数 : 1;负数 : -1
select char_length()--返回字符串长度
select concat() 	--拼接字符串
select insert()		--在指定位置插入某个字符串
select lower()		--小写
select upper()		--大写
select instr()		--返回字符串第一次出现的索引
select replace()	--替换字符串
select substring()	--返回指定位置的子字符串
select reverse()	--反转

--时间和日期函数
select current_date()		--获取当前日期
select curdate()			--获取当前日期
select now()				--获取当前时间
select local_time()			--获取当地时间
select sysdate()			--系统时间
select year(now())			--获取年份
select month(now())			--获取月份
select day(now())			--获取星期数
select hour(now())			--获取小时数
select minute(now())		--获取分钟数
select second(now())		--获取秒数

--系统函数
select system_user()		--当前系统用户
select user()
select version()			--当前版本
```

> 聚合函数

| 函数名称    | 描述     |
| ----------- | -------- |
| **count()** | 计数     |
| sum()       | 求和     |
| avg()       | 求平均数 |
| max()       | 求最大值 |
| min()       | 求最小值 |
| ...         | ...      |

==count()、count(*)、count(1)的区别==

- count(*) 包含所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL的情况

- count(1) 忽略了所有列，用1代表代码行，在统计结果的时候，不会忽略NULL值

- count(列名)会统计该列的行数，会忽略列值为NULL的情况
- 执行效率 :
  - 列名为主键时，count(列名) > count(1)
  - 列名不为主键，count(列名) < count(1)
  - 如果表有多个列且没有主键，count(1)的执行效率优于count(*)
  - 如果有主键，则count(*)的效率是最高的
  - 如果只有一个字段，则count(*)最优

> 分组函数

```sql
select avg(grade) as 平均分 from student 
group by id
having 平均分>80;				-- 用在分组函数后，表示过滤
```

#### 数据库级别的MD5加密

```sql
--修改的时候加密
update 表名 set 字段名=MD5(值);	
--插入的时候加密
insert into 表名 values(MD5());
--校验 : 将用户的密码使用 MD5 加密后再比较
```

#### 常见SQL语句的顺序

```sql
select [distinct] 字段1[...] from 表名
join 表名 on 等值判断
where (具体的值，子查询语句)
group by (通过哪个字段来分组)
having (过滤分组的信息，条件和where是一样的，只是位置不同)
order by ...
limit startIndex,pageSize;
```

### 事务

#### 什么是事务

事务就是一组SQL操作，要么都成功，要么都失败

> 事务原则 : ACID，原子性、一致性、隔离性、持久性 (脏读、幻读)

**1. 原子性 (Atomicity)**

原子性表示，这一组SQL于时要么一起成功，要么一起失败，不能只完成其中几句

**2. 一致性(Consistency)**

针对一个事务操作前与操作后的状态一致

**3. 持久性(Durability)**

表示事务==结束(提交)==后的数据不随外界原因导致数据丢失

**4. 隔离性(Isolation)**

针对多个用户同时操作，主要是排除其他事务的影响

#### 事务的隔离级别

1. 脏读

   指一个事务读取了另一个事务未提交的数据

2. 不可重复读

   表示在一个事务内两次读取的数据不一致(在这期间，其他事务修改了数据)

3. 虚读(幻读)

   是指在一个事务内，前后读取到的数据条数不一致(其他事务进行了添加或者删除操作)

> mySQL的事务是默认自动提交的

```sql
set autocommit = 0;			-- 关闭事务自动提交
set autocommit = 1;			-- 开启事务自动提交

-- 开启一个事务
start transaction
...
-- 设置保存点
savepoint 保存点名称;
-- 释放保存点
release savepoint 保存点名;
-- 提交
commit;
-- 回滚到保存点
rollback to 保存点名称;
-- 回滚
rollback;

explain sql;		-- 分析sql语句的执行
```

### 索引

> MySQL 官方对索引的定义为 : 索引(index)是帮助MySQL高效获取数据的数据结构。提取句子主干就可以得到索引的本质，索引是一种数据结构。

#### 索引的分类

- 主键索引 (primary key)
  - 唯一的标识，主键不可重复；只能有一个列作为主键

- 唯一索引 (unique key)
  - 避免重复的列出现，唯一索引可以重复，多个列都可以标识唯一索引

> ##### ==在一个表中，主键索引只能有一个，而唯一索引可以有多个==

- 常规索引 (key / index)
  - 默认的，使用index、key 关键字来设置

- 全文索引 (fulltext)
  - 在特定的数据库引擎下才有

**索引在数据量小的时候，作用不明显，但是在大数据量的时候，作用会非常明显**

#### 索引原则

1. 索引不是越多越好
2. 不要对经常变动的字段添加索引
3. 小数据量的表不需要添加索引
4. 索引一般用在常被查询的字段上

### 权限管理和备份

#### 用户管理

> SQL命令操作

用户表 ：mysql.user

本质 ： 对这张表进行增删改查

````sql
-- 创建用户
create user leviii indentified by '625008237';
-- 修改密码(当前用户)
set password = password('222222');
-- 修改密码(指定用户)
set password for leviii = password('2222222');
-- 重命名
rename user leviii to lezviii;
-- 用户授权(*表示全部)
grant all privileges on 库名.表名 to leviii;		-- grant，给别人授权的权限默认没有
-- 查询权限
show grants for leviii;  		-- 查看指定用户的权限
show grants for root@localhost		
-- 撤销权限
revoke all privileges on *.* from leviii;
-- 删除用户
drop user leviii;
````

#### MySQL备份

1. 拷贝物理文件

2. 在可视化工具中手动导出

3. 使用命令行导出

   ```bash
   # mysqldump -h 主机 -u 用户名 -p 密码 数据库名 表名1 表名2 表名3... > 物理磁盘位置
   mysqldump -hlocalhost -uroot -p625008237 test student > D:/a.sql
   
   # 导入
   source 备份文件
   # 在未登录mySQL的情况下
   mysql -uroot -p625008237 库名< 备份文件
   ```

### 数据库规范设计

==当数据库比较复杂的时候，我们需要设计数据库==

**糟糕的数据库设计**

- 数据冗余，浪费空间
- 数据库插入和删除数据麻烦、异常(外键导致)
- 程序的性能差

**良好的数据库设计**

- 节省内存空间
- 保证数据库的完整性
- 方便系统的开发和运行

**数据库设计步骤**

1. 分析需求 ：分析业务和需要处理的数据库的需求
2. 概要设计 ：设计关系图E-R图
3. 标识实体 ：确定实体和数据库字段的对应关系
4. 表示实体之间的关系 ：实体之间的关系转换为数据库表的关系

#### 数据库设计三范式

**数据库规范化的意义**

- 减少数据重复
- 减少更新时异常
- 减少插入异常
  - 无法显示正常信息

- 减少删除异常
  - 丢失有效的信息

**三大范式**

1. 原子性：要求数据库的每张表的每一列都是不可再分的原子数据项
2. 前提：满足第一范式，必须要有主键，并且要求每一列都必须和主键直接相关
3. 前提：满足第一和第二范式，非主键之间不可以有依赖

**性能优先**

- 一般要求关联查询的表不超过三张，因此在特殊情况下允许破环三范式
- 为了避免过多的表联查，可以给某张表添加冗余字段（从多表查询转换为单表查询）
- 故意增加一些计算列（从大数据量查询转换为小数据量查询）

### JDBC

#### 数据库驱动

SUN公司为了简化开发人员的(对数据库的统一)操作，提供了一个(Java操作数据库)的统一规范

这些规范的实现由具体的厂商去做（MySQL、Oracle...）

对于开发人员来说，我们只需要掌握JDBC的接口即可。

java.sql

javax.sql

还需要导入一个外部数据库驱动包：mysql-connector-java-xxxx.jar

