# MySQL基础

## 启动MySQL

~~~sql
sudo service mysql start/stop/restart/status
~~~

## 登录与退出

~~~sql
mysql -uroot -p
mysql -h127.0.0.1 -uroot -p
exit 
~~~

## SQL

### **注释：**

- 单行注释：-- 注释内容 或 # 注释内容（MySQL特有）
- 多行注释：/* 注释 */

### 分类

- DDL：操作数据库、表
- DQL：查询表中的数据
- DML：增、删、改表中数据
- DCL：授权

#### **DDL**

- 操作数据库

  ~~~sql
  show databases;												-- 查看所有数据库
  show create database mysql;					-- 查询创建mysql这个库的语句
  
  create database test01;                   								-- 创建数据库
  create database if not exists test01;                       -- 如果没有则创建
  create database test02 character set utf8;				-- 设置字符编码
  
  
  alter database test02 character set utf8;                      -- 修改数据库字符集
  
  
  drop database test02;												-- 删除数据库
  drop database if exists test02;									-- 如果有则删除
  
  use test02;															-- 使用数据库
  select database();                                                  -- 查看当前使用的数据库
  ~~~

- 操作表

  数据类型：

  - int：整数类型
  - double：小数类型  double（5,2）指的是最长为5位，小数点占两位
  - date：日期，只包含年月日
  - datetime：包含年月日时分秒
  - timestamp：时间戳，如果不赋值，则会给当前时间
  - varchar：字符串，varchar（20） 代表最长为20个字符

  ~~~sql
  show tables;                             -- 查看当前使用数据库的所有表
  desc 表名;                                        -- 查看表结构
  show create table teacher;			-- 显示表的创建语句
  
  
  create table teacher(								-- 创建表
      teacher_id int,
      teacher_name varchar(20),
      create_time timestamp
  );
  create table tea like teacher;				-- 复制表
  
  
  drop table teacher;               					-- 删除表
  drop table if exists teacher;
  
  
  alter table tea rename to teacher2;              												 -- 修改表名
  alter table teacher2 character set gbk;											-- 修改表的字符编码
  alter table teacher2 add teacher_age int;											-- 增加一列
  alter table teacher2 change teacher_age age varchar(20);					-- 修改列名与数据类型
  alter table teacher2 modify age int ;																		-- 仅仅修改列的数据类型
  
  
  alter table teacher2 drop age;																	-- 删除属性列
  ~~~



#### **DML**

~~~sql
insert into teacher2(teacher_id, teacher_name) value (123,'victor');            -- 插入数据，前后一一对应


delete from teacher2 where teacher_id=123;				-- 删除数据，没有条件删除所有，删除所有推荐下方这个
truncate table teacher2;												 -- 删除这个表，并创建一个一模一样的空表，效率高


update teacher2 set teacher_name='flank' where teacher_id=123;      -- 更改数据，不加条件，全部修改
~~~



#### **DQL**

语法：

select  字段   from  表   where   查询条件    group by   分组字段    having   分组之后的条件   order by  排序      limit    分页限定

~~~sql
select distinct age from student;-- 消除重复的结果集数
select name,id,age,id+age from student; --属性列值想加，如果有null参与运算，计算的值也是null

select name,id,age,id+age from student; --属性列值想加，如果有null参与运算，计算的值也是null
select name,id,ifnull(age,0),id+age from student;-- 如果age是null则就会取0
select name,id,ifnull(age,0),id+age as total from student;--起别名
select name,id,ifnull(age,0),id+age total from student;--起别名

select * from student where age between 5 and 8;
select * from student where age in (7,11);

select * from student where age is null;
select * from student where age is not null;

select * from student where name like '%v%';-- 模糊查询
select * from student where name like '_v%';
select * from student where name like '___';-- 模糊查询姓名三个字的人
~~~

~~~sql
-- 排序查询       order by 字段  排序方式
select * from student  order by age asc;   -- 升序，默认
select * from student  order by age desc ;   -- 降序
select * from student  order by age desc,id desc;   -- 先按年龄降序，如果年龄一样，则按学号降序


-- 聚合函数：将一列数据作为一个整体，进行纵向计算，自动排除null值
select COUNT(id) from student;       -- 自动排除null值
select COUNT(ifnull(id,0)) from student;  -- 避免null值

select max(id) from student;-- 最大值
select min(age) from student;-- 最小值

select sum(age) from student;-- age列的和
select avg(age) from student; -- age列的平均值


-- 分组查询
select sex,avg(age) from student group by sex;-- 以性别分组计算年龄平均值，
select sex,avg(age),count(id) from student group by sex;   -- 人数
select sex,avg(age),count(id) from student where age > 7 group by sex;    -- 限制年龄小于7的不参与分组

select sex,avg(age),count(id) from
student where age > 7 group by sex having count(id)>1; -- 分组之后在进行限定，只显示一个人以上的组
-- 注：不要添加除了分组字段和聚合函数以外的字段，没有意义
     	-- where后不可跟聚合函数，having可以



-- 分页查询
-- limit 开始索引 查询条数，只是用于MySQL
select * from student limit 0,3;    -- 从0索引开始查询3条数据

-- 分页公式：（当前页码-1）*条数
~~~

#### **约束**

概念：对表的数据进行限定，保证书数据的正确、有效、完整

~~~sql
-- ------------------------------------------主键   primary key，非空且唯一

alter table stu2 drop primary key; -- 删除主键

alter table stu2 modify id int primary key ;  -- 修改时添加主键

auto_increment  -- 自增,配合int类型主键一起使用，且自增只跟上一条数据有关，上一条的自增+1


---------------------------------------------------------- 非空   not null
create table stu (
    id int not null ,
    name varchar(20) not null
); -- 创建表时添加

alter table stu change name name varchar(10);-- 删除

alter table stu modify name varchar(20) not null ;   -- 修改时添加


---------------------------------------------------- 唯一    unique，值唯一，但是可以有多个null
create table stu2 (
    id int unique,
    name varchar(20) not null
);-- 创建时添加

alter table stu2 drop index id;-- 删除唯一

alter table stu2 modify id int unique;  -- 修改时添加


-- ------------------------------------------------------------外键    foreign key
create table stu(
    id int primary key auto_increment,
    name varchar(20) not null ,
    dept_id int not null ,
    constraint stu_dept foreign key(dept_id) references dept(id)-- 创建表时添加外键
);

alter table stu drop foreign key stu_dept;-- 删除外键

alter table stu add constraint stu_dept foreign key(dept_id) references dept(id); -- 添加外键

-- 添加外键并设置级联更新
alter table stu add constraint stu_dept foreign key(dept_id) references dept(id) on UPDATE cascade ; 
-- 添加外键并设置级联删除
alter table stu add constraint stu_dept foreign key(dept_id) references dept(id) on delete cascade ; 
~~~

#### **范式**

概念：设计数据库时，需要遵守的一些规范

- 第一范式：没一列都是不可分割的原子数据项，创建出的表都满足
- 第二范式：在第一范式基础上，非码属性必须完全依赖于码（消除非主属性对码的部分函数依赖）
  - 函数依赖：如果通过A属性（属性组）的值，可以确定唯一B属性的值，则B依赖于A，例如：学号--->姓名
  - 完全函数依赖：A是一个属性组，则B属性的值依赖于A属性组中所有的属性值，例如：（学号，课程名称）----->分数
  - 部分函数依赖：A是一个属性组，B属性的值依赖于A中一部分的值即可，例如：（学号，课程名称）---->姓名
  - 传递函数依赖：A-->B,B--->C，如果A属性（属性组）的值，可以确定唯一B属性的值，在通过B的值可以确定唯一C的值，则称C传递函数依赖于A，例如：学号---->系名，系名--->系主任
  - 码：如果一张表中，一个属性或属性组，被其他所有属性完全依赖，则称这个属性（属性组）为该表的码。就是说一个属性或属性组可以确定其他的所有属性，就是码
- 第三范式：在第二范式基础上，任何非主属性不依赖与其他非主属性（消除传递依赖）



#### **数据库的备份和还原**

~~~~sql
mysqldump -uroot -p123456 数据库名称 > 保存路径                       -- 备份数据库

创建新的数据库，使用source 保存路径    -- 还原
~~~~



#### **多表查询**

~~~sql
-- 内链接查询
	-- 1.隐士内链接
	select * from stu,dept where stu.dept_id=dept.id;
	
	-- 2.显式内链接
	select * from stu inner join dept on stu.dept_id = dept.id;     -- inner可以省略
	
-- 外链接
	-- 1.左外连接：查询左表所有数据以及交集部分
	select * from stu left outer join dept d on stu.dept_id = d.id;   -- outer可省略
	-- 2.右外连接：查询右表所有数据以及交集部分
	select * from stu right outer join dept d on stu.dept_id = d.id;    -- outer可省略
	
-- 子查询：查询中嵌套查询
	-- 结果为单行单列
	select * from stu where stu.id = (select MAX(id) from stu);
	
	-- 结果为多行单列
	select * from stu where dept_id in (select dept.id from dept where name='计算机工程系' or name='理学系');
	
	-- 结果为多行多列
	select * from dept inner join (select * from stu where age > 10) s where dept.id = s.dept_id;

~~~



### 事务基本介绍

**基本介绍**

- 概念：如果一个包含多个步骤的业务操作，则要么同时成功，要么同时失败
- 操作：
  1. 开启事务：start transaction ;
  2. 回滚：rollback ;
  3. 提交：commit ;
  4. MySQL数据库事务默认自动提交，Oracle默认手动提交
     - 自动提交：一条DML（增删改）语句会自动提交一次事务
     - 手动提交：需要事start transaction先开启事务，在commit提交
  5. 修改事务的默认提交方式：
     - 查看默认提交方式：select @@autocommit ;      1代表自动提交，0代表手动提交
     - 修改默认提交方式：set @@autocommit = 0 ;   

#### **四大特征**

1. 原子性：是不可分割的最小操作单位，要么同时成功，要么同时失败
2. 持久性：当事务提交或回滚后，数据库会持久化的保存数据
3. 隔离性：多个事务之间相互独立
4. 一致性：事务操作前后，数据总量不变



#### **事务的隔离级别**

概念：多个事务之间隔离的，相互独立的。但如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别可以解决这些问题

存在问题：

1. 脏读：一个事务，读取到另一个事务没有提交的数据
2. 不可重复读（虚读）：同一个事务中，两次读取的数据不一样
3. 幻读：一个事务操作（DML）数据表中所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改

隔离级别：

1. read uncommitted：读未提交

   产生问题：脏读、不可重复读、幻读

2. read committed：读已提交（Oracle默认）

   产生问题：不可重复读、幻读

3. repeatable read：可重复读（MySQL默认）

   产生问题：幻读

4. serializable：串行化

   可以解决所有问题

   注：隔离级别越高安全性越高、效率越低



数据库查询隔离级别：select @@tx_isolation;

设置隔离级别：set global transaction isolation level 隔离级别名称;





#### DCL（管理用户、授权）

- **管理用户**

  1. 查询用户

     ```sql
     select * from mysql.user;         
     -- 通配符：%表示可以在任意主机使用该用户登录
     ```

  2. 添加用户

     ```sql
     create user 'victor'@'localhost' identified by '123456';   -- 可以把localhost换成%
     ```

  3. 修该用户密码

     ```sql
     set password for 'victor'@'localhost' =password('1234');
     
     -- 忘记root用户密码
     	-- 1.先停止运行MySQL服务
     	-- 2.使用无验证方式启动MySQL
     		mysql --skip-grant-stbles
     	-- 3.新打开终端就可以不需要密码登录了，然后修改密码
     	-- 4.重启服务
     	-- 5.登录使用新密码
     ```

  4. 删除用户

     ```sql
     drop user 'victor'@'localhost';
     ```

- **授权**

  1. 查询权限

     ```sql
     set password for 'victor'@'localhost' =password('1234');
     
     -- USAGE 表示只能登录
     ```

  2. 授予权限

     ~~~sql
     grant 权限列表 on 数据库.表 to 'victor'@'localhost';
     
     grant select,delete on test01.stu to 'victor'@'localhost';
     
     grant all on *.* to 'victor'@'localhost'; -- 所有权限，所有库、表
     ~~~

  3. 撤销权限

     ~~~sql
     revoke 权限列表 on 数据库.表 from 'victor'@'localhost';
     
     revoke select on test01.stu from 'victor'@'localhost';
     
     revoke all on *.* from 'victor'@'localhost';
     ~~~




# MySQL高级部分

## Mysql分层架构

1. 连接层
2. 服务层
3. 引擎层
4. 存储层

## 存储引擎

### InnoDB（默认）

支持事务，有外键，支持行锁

| 对比     | MyIASM                                                   | InnoDB                                                       |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| 主外键   | 不支持                                                   | 支持                                                         |
| 事务     | 不支持                                                   | 支持                                                         |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发的操作 | 行锁，操作时只锁某一行，不对其他行有影响，适合高并发的操作   |
| 缓存     | 只缓存索引，不缓存真实数据                               | 不仅缓存索引还要缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间   | 小                                                       | 大                                                           |
| 关注点   | 性能                                                     | 事务                                                         |
| 默认安装 | Y                                                        | Y                                                            |

## 索引

### 性能下降Sql慢：

1. 查询语句写的烂
2. 索引失效
3. 关联查询join太多（设计缺陷和不合理的需求）
4. 服务器调优和各个参数配置（缓冲、线程数等）

### join查询：

sql的执行顺序：机读的时候是先从from开始

7种Join

~~~sql
create index idx_user_name on user(name) ----index后面为要建立的索引名称,user(name)为表名（列名）

create index idx_user_nameEmail on user(name,email) ----复合索引
~~~

### 概念

索引就是一种排好序的快速查找数据结构，帮助MySQL高效获取数据。一般索引本身也很大，不可能全部存储在内存中，因此往往会以索引文件的形式存储在磁盘上

我们常说的索引，如果没有特别指明，都是B树（多路搜索树，并不一定是二叉）组织结构的索引。其中聚集索引次要索引、复合索引、前缀索引、唯一索引都是使用B+树，统称索引。当然除了B+树这种类型的索引之外还有Hash索引

### 优势

- 提高数据检索的效率，降低数据库的IO成本

- 降低排序成本，降低CPU的消耗

### 劣势

1. 实际上索引也是一张表，该表保存了主键与索引字段，并指向实体表的字段，所以索引列也是要占用空间的
2. 虽然索引大大提高了查询效率，同时会降低更新表的速度，如对表进行Insert、Update、Delete，因为更新表时不仅要保存数据，还要保存一下索引文件每次更新添加了索引列字段，都会调整因为更新所带来的键值变化后的索引变化信息
3. 索引只是提高效率的一个因素，如果你的MySQL有大量的表，就需要花时间建立最优秀的索引

### 索引分类

- 单值索引：一个索引只包含一个单个列，一个表有多个单值索引

- 唯一索引：索引列的值必须唯一，但可以为NULL

- 复合索引：即一个索引包含很多列

- 基本语法：

  ~~~sql
  ----创建
  create [unique] index indexName on myTable(columnname(length));
  
  alter myTable add [unique] index [indexName] on (columnname(length));
  
  
  ----删除
  drop index [indexName] on myTable;
  
  ----查看
  show index from table_name\G
  ~~~

### 索引结构

- BTree索引*
- Hash索引
- full-text索引
- R-Tree索引

### 需要建立索引的情况

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询中与其他表关联的字段，外键关系建立索引
4. 频繁更新的字段不适合创建索引
5. Where条件里用不到的字段不创建索引
6. 单键/组合索引的选择问题，who？（在高并发下倾向创建复合索引）
7. 查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
8. 查询中统计或者分组字段

### 不需要建立索引的情况

1. 表记录太少
2. 经常增删改的表：因为不但要保存更新的字段，而且还要更新保存索引文件
3. 数据重复且分布平均的字段

### Explain性能分析

~~~sql
---使用方法
explain sql
---如：explain select * from user;
~~~

![](C:\Users\victor\learn\学习资料\MySQL\img\Snipaste_2021-04-05_15-33-16.png)

#### id

select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序

三种情况：

1. id相同，执行顺序由上至下
2. id不同，如果是子查询，id的号会递增，id的值越大优先级越大，越先被执行
3. id相同不同，同时存在

#### select_type（查询类型）

- SIMPLE：简单的select查询，查询中不包含子查询与union
- PRIMARY：查询中若包含任何复杂的子部分，最外层则被标记为此
- SUBQUERY：在select或where列表中包含子查询
- DERIVED：在from列表中包含的子查询被标记为DERIVED（衍生），MySQL会递归执行这些子查询，把结果放在临时表中
- UNION：若第二个Select出现在UNION之后，则被标记为UNION，若UNION包含在from子句的子查询中，外层select将被标记为：DERIVED
- UNION RESULT：从UNION表获取结果的select

#### table（哪张表）

#### partitions（）

#### type（访问类型）

最好到最坏的排序：system > const > eq_ref > ref > range > index > ALL，一般来说要保证级别到range，最好能达到ref

- system：表中就一条记录
- const：表示通过索引一次就找到了
- eq_ref：唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
- ref：非唯一性索引扫描，返回匹配某个单独值得所有行
- range：只检索给定范围的行，使用一个索引来选择行。key列显示使用了哪个索引
- index：只遍历索引树
- ALL：全表扫描

#### possible_keys（显示可能应用到本次查询的索引，但不一定被实际使用）

#### key（实际使用的索引，如果为NULL，则没有使用索引）

#### key_len（表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度，在不损失精度的情况下，长度越短越好）

#### ref（显示索引的那一列被使用，如果可能的话是一个常数。）

#### rows（大致估算出找到记录所需要的行数）

#### Extra（包含不适合在其他列中显示但十分重要的额外信息）

- Using filesort：说明mysql会对数据使用一个外部索引排序，而不是按照表内的索引顺序进行读取
- Using temporary：使用了临时表保存中间结果，Mysql在对查询结果进行排序时使用临时表，常见于order by与group by
- Using index：表示相应的select操作使用了覆盖索引，避免了访问表数据行，效率不错。如果同时出现using where，表明索引被引用来执行索引建的查找，如果没有出现using where，表明索引用来读取数据而非执行查找动作。索引覆盖：意思是select的数据列只用从索引中就能取到，而不必去读取数据行

## 索引优化

### 索引单表优化

当where后条件出现 > 或者 < 这种类似范围的条件时，这个条件后的索引全部失效，所以这个条件字段不建立复合索引即优化成功

### 两表优化

当使用左连接时，添加索引到右表相应的字段，因为左连接保证左表所有数据全有所以加在右表起决定性作用

右连接与左连接思想一致，建在左表

### join语句优化

1. 永远用小表驱动大表
2. 优先优化内层循环语句

### 避免索引失效

1. 全值匹配最喜欢
2. 位于索引前方的字段不能没有，索引中间的字段不在，后面的也会失效
3. 不在索引列上做任何操作（计算、函数、类型转换等），会导致索引失效而转向全表扫描
4. 不能使用索引中范围条件后边的列
5. 尽量使用覆盖索引（只访问索引的查询，索引列与查询列一致），减少select *
6. mysql在使用不等于（!=、<、>）的时候无法使用索引会导致全表扫描
7. is null、is not null也无法使用索引
8. like以通配符（%）开头索引会失效，导致全表扫描。如果想使用两边%则使用覆盖索引可以解决
9. 字符串不加单引号导致索引失效
10. 少用or，用它来连接时会导致索引失效

## 查询截取分析

### 步骤

1. 慢查询的开启与捕获
2. explain + 慢SQL分析
3. show profile 查询SQL在MySQL服务器里的执行细节和生命周期情况
4. SQL数据库服务器的参数调优



## 锁

### 表锁

偏向MyISAM存储引擎，开销小、加锁快；无死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低

~~~sql
lock table tableName read(write),tableName2(write)  --- 添加读锁或写锁

unlock tables;  ----释放锁
~~~

1. 读锁
   - 设置锁的session可以读取这个表，但是不能修改，修改时否则会报错
   - 其他session可以读取，但是修改时会阻塞，直到锁释放
2. 写锁
   - 设置锁的session可以读取、修改表内数据，不可以操作其他数据表
   - 其他session读取表、修改表会产生阻塞，直到锁释放

### 行锁

偏向InnoDB存储引擎，开销大、加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最小，并发度也很高。

InnoDB与MyISAM的最大不同两点：一是支持事务，二是采用了行级锁

当设置事务不是自动提交时，给表1设置了行锁，当前session修改数据但还没有提交时，另一个session也操作此行数据则会阻塞，直到当前session提交事务

索引失效导致行锁变成表锁

#### 间隙锁：

当我们适用范围条件而不是相等条件检索数据，并请求共享或排它锁时，InnoDB会给符合条件的已有数据记录的索引项加锁，对于键值在此范围内但并不存在的记录叫做间隙，InnoDB也会给这个“间隙”加锁，这种机制叫做间隙锁



优化建议：

1. 尽可能让所有数据检索都通过索引来完成，避免无索引使行锁升级为表锁
2. 合理设计索引，尽量缩小锁的范围
3. 尽可能较少检索条件，避免间隙锁
4. 尽量控制事务大小，减少锁定资源量和时间长度
5. 尽可能低级别事务隔离



### 页锁









