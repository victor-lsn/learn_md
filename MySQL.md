# MySQL

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

     

