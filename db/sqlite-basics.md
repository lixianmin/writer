



---

#### 0x01 sqlite3命令行

1. mac中terminal自带sqlite3命令；
2. sql命令直接输入，以";"结束并执行；



| Command            | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| .schema  ?PATTERN? | table的创建命令                                              |
| .databases         | databases                                                    |
| .exit              | exit                                                         |
| .open FILENAME     | 打开db，FILENAME是完整文件名，如果不完整**则会新建**一个数据库 |
| .tables            | tables                                                       |
|                    |                                                              |



----

#### 0x02 常见操作

1. 增删改查（crud）操作

   ```sql
   insert into table_name [(column1, ...)] values (value1, ...)
   update table_name set column1= value1 where [condition]
   select from table_name where [condition] group by [] having [] order by [] desc
   delete from table_name where [condition]
   ```

2. 条件语句

    | Name   | Notice             |
    | ------ | ------------------ |
    | where  | select的查询条件   |
    | having | group by的分类条件 |
    | on     | join的连接条件     |



3. 时间
  使用Integer类型，插入删除时使用str

    ```sql
    -- 选择expiration_date小于今天的数据
    select * from MedicineTable where expiration_date < date('now'); 
    ```

4. bool
  创建使用Integer类型，插入时使用0/1

  ```sql
  insert into Inventroy(name, nessary) values ('百蕊颗粒', 1)
  select * from Inventory where nessary
  ```

   

----

#### 0x03 References

1. [SQLite 教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
2. [python2操作sqlite3](https://docs.python.org/2/library/sqlite3.html)