



---

#### sqlite3命令行

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

#### 常见操作



1. 时间操作

```sql
-- 选择expiration_date小于今天的数据
select * from MedicineTable where expiration_date < date('now'); 

```



----

#### References

1. [SQLite 教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
2. [python2操作sqlite3](https://docs.python.org/2/library/sqlite3.html)