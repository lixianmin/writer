





---

##### 4.3.3 混用范式化和反范式化

最常见的反范式化数据的方法是复制或者缓存，在不同的表中存储相同的特定列。在MySQL 5.0和更新的版本中，可以使用触发器更新缓存值，这使得实现这样的方案变得更简单。

> 在代码设计中同样会遇到类似的问题。缓存一致性很难维护，如果缓存的是错误的数据，那还不如没有。
>
> 在MVC中，为了使UI上显示的数据跟Model中一致，通常只有两种方案，一种是实时获取，一种是订阅数据更新事件（在数据库中对应着触发器更新缓存值）。在 MVC中的Model更倾向于正交化的设计（对应数据库的范式化设计），这可能是由两方面的原因导致的：一是维护缓存一致性代价很高，二是因为代码都在内存中实时进行数据关联的代价不大。



---

#### 5.1 索引基础

##### 5.1.1 索引的类型

在MySQL中，索引是在存储引擎而不是服务器层实现的

> MySQL5.5之后，默认存储引擎InnoDB，但可以在create table指定存储引擎



###### B-Tree索引

B-Tree索引适用于全值键、键值范围（between或like）或键最左前缀查找（like 'abc%'）



###### Hash索引

只有Memory引擎显示支持Hash索引

InnoDB引擎有一个特殊的功能叫"adaptive hash index"，当InnoDB注意到某些索引值被使用的非常频繁时，它会有内存中基于B-Tree索引之后再创建一个Hash索引。

**伪Hash索引**。比如对变长的url，可以在表中创建一个url_crc列。查询方法如下：

```sql
select id from table 
where url_crc = CRC32("http://www.mysql.com") and url = "http://www.mysql.com";
```

1. 跟查询hash表一样，需要先比较hash值再比较实际值；
2. 作为hash值的url_crc列表需要两个trigger在before insert和before update的时候更新；
3. 不要使用SHA1()和MD5()函数计算hash值，因为这两个是强加密函数，计算出来的hash值长（浪费空间），比较也会更慢（浪费时间）；



---

#### 5.2 索引的优点

1. 索引大大减少了服务器需要扫描的数据量；
2. 索引可以帮助服务器避免排序和临时表；
3. 索引可以将随机IO变为顺序IO；

索引适合GB量的数据，当数据量达到TB量级的时候，定位单条记录的意义不大，所以经常会使用块级别的元数据技术来替代索引。



---

#### 5.3 高性能索引策略

##### 5.3.1 独立的列

"独立的列"是指索引不能是表达式的一部分，也不是能函数的参数。下面这个查询就无法使用actor_id列的索引：

```sql
select actor_id from table where actor_id + 1 = 5;
```

所以，remember，**始终将索引列单独放到比较符号的一侧**。

##### 5.3.2 前缀索引和索引选择性

有时候需要索引很长的字符列，这会让索引变得大且慢。一个策略是前面提到过的模拟Hash索引。但有时候这样做还不够，还可以做些什么呢？

一般情况下某个列前缀的选择性也是足够高的，足以满足查询性能。对于BLOB, TEXT或者很长的VARCHAR类型的列，必须使用前缀索引，因为MySQL不允许索引这些列的完整长度。

前缀索引是一种能使索引更小、更快的有效办法，但另一方面也有其缺点：MySQL无法使用前缀索引做order by和group by，也无法使用前缀索引做覆盖扫描。



##### 5.3.3 多列索引

 index_merge优化的特点：

1. 只能合并针对单个table的scan；

2. 如果where子句很复杂的话，可能需要拆解：

```sql
(x AND y) OR z => (x OR z) AND (y OR z)
(x OR y) AND z => (x AND z) OR (y AND z)
```

3. 不能应用到full-text indices；



----

#### References

1. [Index Merge Optimization](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)
2. [MySQL 优化之 index merge(索引合并)](https://www.cnblogs.com/digdeep/archive/2015/11/18/4975977.html)
3. 







