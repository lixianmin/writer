#### 01 basics

1. MySQL的字符集请指定为[utf8mb4](https://dev.mysql.com/doc/refman/5.6/en/charset-unicode-utf8mb4.html)以支持完整的中文字符集；
2. `mysql -uroot -p123456；` -p与密码之间不允许有空格，只能在.sh文件使用；命令行中不能带密码
3. mysql -uroot -p123456 -h 192.168.1.88 -D mydb;
4. sql命令直接输入，**以";"或"\G"结束**并执行；
5. **判断相等时只使用一个"="**；
6. 可以使用`select *, id from account`这种方法，不能使用`select id, * from account`
8. explain xxx; 查询分析；
9. innoDB支持自适应hash索引，默认开启；另外还有空间索引、全文索引；
10. **datetime类型比较时一定要使用跟定义时一样的精度，否则会被四舍五入**，比如之前定义的是秒级精度，而比较时使用的是 '2019-06-26 23:59:59.499999' 会被砍为  '2019-06-26 23:59:59'，而 '2019-06-26 23:59:59.500000'会被进位为  '2019-06-27 00:00:00'；
11. 




| Command            | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| 1. desc tbl_name;<br />2. show columns from tbl_name; | 显示table结构                                       |
| show create procedure proc_name \G | 显示创建procedure语句 |
| show create table tbl_name; | 显示创建table的语句 |
| show databases; | 显示db列表 |
| show engines; | 显示存储引擎信息 |
| show index from til_name; | 显示表的索引情况 |
| show global status like 'Thread%'; | 查询线程状态 |
|  |  |
| select @@version; | 显示db版本 |
|  |  |
| source filepath.sql; | 导入sql文件 |
| use db_name; | 切换到db |



---

#### 02 create table

```mysql
create temporary table t(id int primary key, a int, index(a)) engine=innodb;
```



---

#### 03 select


```mysql

# 1.FROM > WHERE > GROUP BY > HAVING > SELECT 的字段 > DISTINCT > ORDER BY > LIMIT，这个优先级顺序中， from, where, group by, having它们的顺序与在select语句中出现的顺序是一致的
# 2. where作用于每一行，而having作用于每一个分组
select * from t where.. group by ... having

# 1. select语句能初始化seesion variables，比如：select @rowid:=0
# 2. from语句先于select语句执行，所不同的是：from只执行一次，而select每行执行一次
# 3. 执行顺序： FROM > WHERE > GROUP BY > HAVING > SELECT 的字段 > DISTINCT > ORDER BY > LIMIT
# 4. from语句中可以嵌套select语句
# 5. @rowid是会话变量，在整个会话中是全局变量，可以被后面的其它语句多次查询或修改；(select @rowid:= 0)的作用是在每次调用下面的sql时初始化@rowid:=0，否则在多次调用之间@rowid会累加
select @rowid:=@rowid+1 as rowid from account, (select @rowid:= 0) as init limit 10;

# limit通常放在最后，除非遇到了for update
select * from account limit 10 for update;

# 获取上一次insert生成的主键id；只要是使用同一个链接就可以
SELECT LAST_INSERT_ID();

# 这样的，LAST_INSERT_ID(xx)的作用就是传入什么，记一下，然后原值返回
UPDATE child_codes SET counter_field = LAST_INSERT_ID(counter_field + 1);
SELECT LAST_INSERT_ID();

# 自增id是异步落库的，导致数值上偏小的id在磁盘位置上却可能偏后，因此：
# 1. 如果想让结果按主键排序，必须加上order by id
# 2. 如果依赖自增id同步数据，则需要注意不能取当下时间的最大id值，需要考虑一个时间差，以确保这些id落库
select * from account_history where create_time < now() - interval 30 minute order by id;

# 查询哪些线程是比较慢的，发现以后可以 kill id杀死它；
select * from information_schema.processlist where info != 'null' order by time desc;

# 查询哪些表是大表，可以归档
select * from information_schema.tables order by table_rows desc;

select * from information_schema.tables where table_schema = 'coinbene_exchange';


# 查询最近一次该user_id的修改记录
select
	rec.id, rec.user_id, rec.create_time
from
	account_record rec, 
	(select max(id) max_id from account_record where create_time >= date_sub(now(), interval 60 minute) group by user_id) max_ids
where
	rec.id = max_ids.max_id

# 查询某个时间段账户的余额（需要去hive）
select id, user_id, total_balance_before, create_time from account_record a where a.id in (select max(id) from account_record where create_time < '2019-08-10' group by user_id) limit 100; 

```



##### 2. join优化

```mysql
# 由于join语句发生在from中，时间在where之间，因此where条件对表的过滤并不会对join时刻的表起作用，如果被驱动表很大的话，可能会导致join时间过长；这时可以考虑先通过在子查询中使用where过滤表行数；
```



------

#### 04 常见操作

##### 01 增删改查（crud）



```sql
insert into table_name [(column1, ...)] values (value1, ...)
update table_name set column1= value1 where [condition]
select * from table_name where [condition] group by [] having [] order by [] desc
delete from table_name where [condition]

alter table tbl_name add index [index_name] (column_list);
alter table tbl_name add unique (column_list);
show index from tbl_name;
alter table tabl_name drop index index_name;
```



##### 02 条件语句

| Name   | Notice                               |
| ------ | ------------------------------------ |
| where  | select的查询条件                     |
| having | group by的分类条件，可以使用聚合函数 |
| on     | join的连接条件                       |



##### 03 插


```mysql
# 1. 如果primary/unique key重复了，则插入失败
# 2. 凡insert语句，无论成败都会导致自增id+1
insert into t(id, v) values(1, 2)

## 1. 如果primary/unique key重复了，则直接忽略返回
## 2. 凡insert语句，无论成败都会导致自增id+1
insert ignore into t(id, v) values(1, 2)

# 1. 依据primary/unique key：无则插入，有则更新
# 2. 等价于：
#	if (exists old)
#		update
#	else
#		insert
#
# 3. 当它是insert的时候，因为是插入，因此会导致自增id+1；
# 4. 当它是update的时候，因为是修改，因此不会修改id。普通update也只修改需要set的一部分，其它的不变
# 5. 这条语句拿的是x锁，同时可以根据表中存在的数据执行update（这一点replace into做不到），因此特别适合于并发事务之间用于设置初始化条件 (比如like, friend表的并发维护)
insert into t(id, v) values (1,2) on duplicate key update v=v+1;

# 1. 依据primary/unique key：无则插入，有则替换
# 2. 等价于：
#	if (exists old) 
#		delete old
#	insert new
#
# 3. 因此只要在values(..)中未列出来的值，都会使用default value
# 4. 语法结构上，replace与insert一模一样的
# 5. 因为replace一定会调用insert，因此必然会导致自增id+1
replace into t(id, v) values (1, 2);

```



##### 04 删


1. insert、update、delete操作会返回被操作的行数



```mysql
# 1. 按时间跨度delete表，有可能单次操作表行数过多，因此需要加limit参数
# 2. 为了保证按时间由小到大的顺序删除表内容，需要加order by create_time
# 3. 有可能需要反复调用以下语句，直到返回值 < 1000
# 4. delete是DML语句，是正常的表数据维护，在事务中可以回滚
delete from user_event where create_time < ? order by create_time limit 1000;

# truncate是DDL语句，会重置整个表，包括auto_increament到1，无法回滚
truncate t;
```




##### 05 基于一张表更新另一张表

```mysql
# 方式一：
update t1, (select * from xxx where xxx) t2
set t1.name = t2.name
where t1.id = t2.id;


# 方式二：
update t1 inner join t2
on t1.id = t2.id
set t1.name = t2.name
where xxx
```



##### 06 设计时考虑预留字段

有些表，比如user_extend_info，几乎可以肯定后期会加入新的字段，但是在前期并不确定是有哪一些，因此应考虑加入varchar()类型的预留字段，后面可以在comment中加注释用于明确字段的作用



#####  07 字段尽量设置not null

MySQL可以在含有null的列上使用索引，包括普通的等值查询和范围查询，但针对null字段本身有一些特殊的处理方式需要注意：

1. 对null值不能使用=, <, >这样的运算符，判断列是否为null需要使用is null
2. 对null做算术运算的结果都是null
3. count时不会包含null的行
4. null比空字符串占用更多的存储空间



因此，不建议在列上允许为null，最好限制not null，并设置一个默认值，比如0和''空字符串，如果是timestamp类型，可以设置成'1970-01-01 08:00:01' （注意东8区）这样的特殊值。



##### 08 时间函数

```mysql
# 时间格式化
select date_format(now(), '%Y-%m-%d %H:00:00');

# 时间差
select date_sub(now(), interval 1 hour);
select date_add(now(), interval 1 day);
```



##### 09 用户管理

```mysql
# 创建用户
create user 'panda'@'%' identified by 'password';
grant all privileges on *.* to "panda"@'%';

# 创建只读用户
grant select on *.*  to  'grafana'@'192.168.16.11'  IDENTIFIED BY "pa$$word";

# 修改密码；可以设置空密码
alter user 'root'@'localhost' identified with mysql_native_password by '123456'; 

# 授权数据库实例
grant all privileges on wisdom.* to 'grafana'@'192.168.%';

# 回收数据库实例权限
revoke all privileges on wisdom.* from 'grafana'@'192.168.%';

# 列出当前用户列表
select user, host from mysql.user;

```



##### 10 事务

1. 在MySQL中， @x是用户自定义变量， @@x是系统变量
2. 

```mysql
# 是否自动commit
select @@autocommit;

# 查询隔离级别
select @@tx_isolation;					# 默认是session级别
select @@global.tx_isolation;		# 加上global.就是global级别

select @@session.tx_isolation;	# session级别
select @@transaction_isolation; # (MySQL 8.0)

# 修改事务隔离级别
set session tx_isolation = 'read-committed';		# 修改session变量
set global tx_isolation = 'repeatable-read';		# 修改global变量

set global transaction_isolation = 'repeatable-read';	# (MySQL 8.0)
set session transaction_isolation='read-committed'; 	# (MySQL 8.0)

# 事务
start transaction;
commit;
rollback;
```



------

#### 09 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. [INSERT ... ON DUPLICATE KEY UPDATE Syntax](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html)
3. [MySQL 四种事务隔离级的说明](https://www.cnblogs.com/zhoujinyi/p/3437475.html)
4. [Mysql show processlist 排查问题](https://www.cnblogs.com/duhuo/p/5678286.html)
5. [IS NULL Optimization](https://dev.mysql.com/doc/refman/5.7/en/is-null-optimization.html)