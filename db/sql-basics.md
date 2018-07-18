

---

#### 0x 01 基础概念


1. MySQL的字符集请指定为[utf8mb4](https://dev.mysql.com/doc/refman/5.6/en/charset-unicode-utf8mb4.html)以支持完整的中文字符集；
2. varchar(10)就是new StringBuilder(10)，而char(10)就是new char[10];
3. select count(*) as cnt, left(city, 3) as pref ...，所以as操作可不是重命名，而是变量定义；
4. 各种连接join方式：

<img src="https://raw.githubusercontent.com/lixianmin/writer/master/db/images/sql-joins-vis-rep-1.png" style="zoom:50%" />

---

---
#### 0x03 References
1. [图解 SQL 里的各种 JOIN](https://zhuanlan.zhihu.com/p/29234064)
