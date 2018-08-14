

------

#### 0x01 mysql命令行

1. mysql -uroot -p12345678；
2. sql命令直接输入，**以";"或"\G"结束**并执行；
3. **判断相等时只使用一个"="**；
4. alter user 'root'@'localhost' identified by '12345678'; 修改密码；
5. explain xxx; 查询分析




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

------

#### 0x03 References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. 