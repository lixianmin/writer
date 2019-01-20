

------

#### 0x01 mysql命令行

1. MySQL的字符集请指定为[utf8mb4](https://dev.mysql.com/doc/refman/5.6/en/charset-unicode-utf8mb4.html)以支持完整的中文字符集；
2. mysql -uroot -p12345678；
3. sql命令直接输入，**以";"或"\G"结束**并执行；
4. **判断相等时只使用一个"="**；
5. alter user 'root'@'localhost' identified by '12345678'; 修改密码；可以设置空密码
6. explain xxx; 查询分析




| Command            | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| desc table_name; | 显示table结构                                       |
| show columns from tbl_name like 'abc%'; | 显示table结构 |
| show create procedure proc_name \G | 显示创建procedure语句 |
| show create table tbl_name; | 显示创建table的语句 |
| show databases; | 显示db列表 |
| source filepath.sql; | 导入sql文件 |
| use db_name; | 切换到db |



------

#### 0x02 常见操作

1. 增删改查（crud）操作

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

2. 条件语句

   | Name   | Notice             |
   | ------ | ------------------ |
   | where  | select的查询条件   |
   | having | group by的分类条件 |
   | on     | join的连接条件     |

​    

3. 没有则插入/存在则更新

```mysql

// 方式一：此种方式下，如果旧值是存在的，则只更新update中设置的那一部分，其它的保持旧值
INSERT INTO t1(a,b,c) VALUES (1,2,3)
ON DUPLICATE KEY UPDATE c=c+1;

// 方式二：此种方式下，如果旧值是存在的，则会先把旧值删除，再插入一条新的，因此如果VALUES()列中没有对应的值，则会设置为默认值
REPLACE INTO test VALUES (1, 'Old', '2014-08-20 18:47:00');

```



4. 通过一张表更新另一张表

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



---

#### 0x04 事务隔离级别



| 隔离级别 | Dirty Read | NonRepeatable Read | Phantom Read |
| -------- | ---------- | ------------------ | ------------ |
|      未提交读（Read uncommitted）  |      √                         | √        |             √             |
|       已提交读（Read committed）      |    x    |                     √             |          √       |
|       可重复读（Repeatable read）       |   x   |                   x                |     √          |
|        可串行化（Serializable ）    |          x  |                      x      |             x          |



1. 未提交读(Read Uncommitted)：允许脏读，也就是可能读取到其他会话中未提交事务修改的数据
2. 提交读(Read Committed)：只能读取到已经提交的数据，Oracle等多数数据库默认都是该级别 (不重复读)
3. **可重复读(Repeated Read)**：可重复读。在同一个事务内的查询都是事务开始时刻一致的，InnoDB默认级别。在SQL标准中，该隔离级别消除了不可重复读，但是还存在**幻读**（第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样）
4. 串行读(Serializable)：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞



------

#### 0x09 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. [INSERT ... ON DUPLICATE KEY UPDATE Syntax](https://dev.mysql.com/doc/refman/8.0/en/insert-on-duplicate.html)
3. [MySQL 四种事务隔离级的说明](https://www.cnblogs.com/zhoujinyi/p/3437475.html)