

------

#### 0x01 mysql命令行

1. MySQL的字符集请指定为[utf8mb4](https://dev.mysql.com/doc/refman/5.6/en/charset-unicode-utf8mb4.html)以支持完整的中文字符集；
2. mysql -uroot -p12345678；
3. mysql -uroot -p123456 -h 192.168.1.88 -D mydb;
4. sql命令直接输入，**以";"或"\G"结束**并执行；
5. **判断相等时只使用一个"="**；
6. alter user 'root'@'localhost' identified by '12345678'; 修改密码；可以设置空密码
7. explain xxx; 查询分析；
8. innoDB支持自适应hash索引，默认开启；另外还有空间索引、全文索引；




| Command            | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| desc table_name; | 显示table结构                                       |
| show columns from tbl_name like 'abc%'; | 显示table结构 |
| show create procedure proc_name \G | 显示创建procedure语句 |
| show create table tbl_name; | 显示创建table的语句 |
| show databases; | 显示db列表 |
| show engines; | 显示存储引擎信息 |
| show index from til_name; | 显示表的索引情况 |
|  |  |
| select @@autocommit; | 是否自动commit |
| select @@version; | 显示db版本 |
|  |  |
| set session transaction isolation level read committed; | 修改当前会话的隔离级别 |
| source filepath.sql; | 导入sql文件 |
| use db_name; | 切换到db |



------

#### 0x02 常见操作

##### 1. 增删改查（crud）



```sql
insert into table_name [(column1, ...)] values (value1, ...)
update table_name set column1= value1 where [condition]
select from table_name where [condition] group by [] having [] order by [] desc
delete from table_name where [condition]

alter table tbl_name add index [index_name] (column_list);
alter table tbl_name add unique (column_list);
show index from tbl_name;
alter table tabl_name drop index index_name;
```



##### 2. 条件语句

| Name   | Notice                               |
| ------ | ------------------------------------ |
| where  | select的查询条件                     |
| having | group by的分类条件，可以使用聚合函数 |
| on     | join的连接条件                       |

​    

##### 3. 花样插入



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



##### 4. 基于一张表更新另一张表

```mysql
// 方式一：
update t1, (select * from xxx where xxx) t2
set t1.name = t2.name
where t1.id = t2.id;


// 方式二：
update t1 inner join t2
on t1.id = t2.id
set t1.name = t2.name
where xxx
```



##### 5. 设计时考虑预留字段

有些表，比如user_extend_info，几乎可以肯定后期会加入新的字段，但是在前期并不确定是有哪一些，因此应考虑加入varchar()类型的预留字段，后面可以在comment中加注释用于明确字段的作用



##### 6. 定期清理旧数据

1. insert、update、delete操作会返回被操作的行数
2. 按时间跨度进行delete表，有可能单次操作表行数过多，因此需要加limit参数

```mysql
# 反复调用以下语句，直到返回值 < 1000
delete from user_event where create_time < ? limit 1000
```



#####  7. 清空table

```mysql
# truncate是DDL语句，会重置整个表，包括auto_increament到1，无法回滚
truncate t;

# delete是DML语句，就是正常的表数据维护，在事务中可以回滚
delete from t;

```



------

#### 0x09 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. [INSERT ... ON DUPLICATE KEY UPDATE Syntax](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html)
3. [MySQL 四种事务隔离级的说明](https://www.cnblogs.com/zhoujinyi/p/3437475.html)