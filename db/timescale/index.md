



----

#### 0x01 BTree

b-tree适合所有的数据类型，支持排序，支持大于、小于、等于、大于或等于、小于或等于的搜索。

索引与递归查询结合，还能实现快速的稀疏检索。



1. [PostgrSQL 递归SQL的几个应用 - 极客与正常人的思维](https://github.com/digoal/blog/blob/master/201705/20170519_01.md)
2. 



----

#### 0x02 Hash

hash索引存储的是被索引字段VALUE的哈希值，只支持等值查询。

hash索引特别适用于字段VALUE非常长（不适合b-tree索引，因为b-tree一个PAGE至少要存储3个ENTRY，所以不支持特别长的VALUE）的场景，例如很长的字符串，并且用户只需要等值搜索，建议使用hash index。



----

#### 0x03 Gin

通用倒排索引 Generailized Inverted Index

##### 多值搜索

当需要搜索多值类型内的VALUE时，适合多值类型，例如数组、全文检索、TOKEN。（根据不同的类型，支持相交、包含、大于、在左边、在右边等搜索）

```mysql
create table t_gin1 (id int, arr int[]); 

create index idx_t_gin1_1 on t_gin1 using gin (arr); 

# && ”是否重叠“，比如：两个数组中只要有一部分数据是相同的，就是算重叠
select * from t_gin1 where arr && array[1,2]; 

# @> “是否包含”，比如：一个点是否包含在一个圆中
select * from t_gin1 where arr @> array[1,2];
```



##### 稀疏搜索

当用户的数据比较稀疏时，如果要搜索某个VALUE的值，可以适应btree_gin支持普通btree支持的类型。（支持btree的操作符）

```mysql
create extension btree_gin; 

create table t_gin2 (id int, c1 int); 

create index idx_t_gin2_1 on t_gin2 using gin (c1); 

select * from t_gin2 where c1=1;
```



##### 多列任意搜索

当用户需要按任意列进行搜索时，gin支持多列展开单独建立索引域，同时支持内部多域索引的bitmapAnd, bitmapOr合并，快速的返回按任意列搜索请求的数据。

```mysql
create table t_gin3 (id int, c1 int, c2 int, c3 int, c4 int, c5 int, c6 int, c7 int, c8 int, c9 int); 

create index idx_t_gin3_1 on t_gin3 using gin (c1,c2,c3,c4,c5,c6,c7,c8,c9); 

select * from t_gin3 where c1=1 or c2=1 or c4=1 and (c6=1 or c7=2) or c8=9 or c9=10;
```



---

#### 0x04 Brin

Block Range Index

BRIN 索引是块级索引，有别于B-TREE等索引，BRIN记录并不是以行号为单位记录索引明细，而是记录每个数据块或者每段连续的数据块的统计信息（min(val), max(val), has null? all null? left block id, right block id )。因此BRIN索引空间**占用特别的小**，对数据写入、更新、删除的影响也很小。

BRIN属于LOSSLY索引（有损索引），当被索引列的值与物理存储相关性很强时，BRIN索引的效果非常的好。例如时序数据，在时间或序列字段创建BRIN索引，进行等值、范围查询时效果很棒。

##### BTree索引 VS. Brin索引



|          | BTree索引 | Brin索引 |
| -------- | --------- | -------- |
| 空间占用 | 10GB      | 20MB     |
| 范围查询 | 20ms      | 60ms     |
| 精确查询 | 0.03ms    | 40ms     |
| 插入性能 | 30万行/s  | 60万行/s |



##### Partial Index



部分索引，Create索引时使用过滤条件只对表的一部分子集对应的索引字段建立索引。适用于分布严重失衡的数据分布，对于不同分布情况下的数据可以建立不同的索引。

例如：我们构造一个数据严重偏移的数据分布情况。表中有10,000,000的数据量，在id1这个字段上总共有100个唯一值，但1-99这些值只重复了10次；而id1=100却有9,999,010行，占了总数据量的99.9901%。很明显，对于id1=100这个值使用BTREE索引是完全没有必要的，不仅会消耗大量的存储空间，也无法带来查询性能的提升。这正是Partial Index适用的场景。

```mysql
# 针对非100的数据，创建btree索引
create index idx_btree on t(id1) where id1 != 100;

## 针对100的数据，建立brin索引
create index idx_brin on t using brin(id1) where id1 = 100;

# 由于非100的数据量也不大，可以统一建立brin索引
create index idx_brin_global on t using brin(id1);
```



1. [PostgreSQL 物联网黑科技 - 瘦身几百倍的索引(BRIN index)](https://github.com/digoal/blog/blob/master/201604/20160414_01.md)
2. [PostgreSQL中BRIN和BTREE索引的比较（一）](http://www.postgres.cn/news/viewone/1/143)
3. [PostgreSQL中BRIN和BTREE索引的比较（二）](http://www.postgres.cn/news/viewone/1/144)



---

#### 0x05 Bloom

`bloom` 提供基于 [Bloom filters](http://en.wikipedia.org/wiki/Bloom_filter) 的索引访问方法。

当表具有许多属性并且查询测试它们的任意组合时，bloom索引最有用。传统的btree索引比bloom索引更快，但它可能需要许多btree索引来支持所有可能的查询，而bloom索引则只需要一个。

注意：bloom索引仅支持相等查询，而btree索引也可以执行不等式和范围搜索。



```mysql
CREATE INDEX bloomidx ON tbloom USING bloom (i1,i2,i3)
       WITH (length=80, col1=2, col2=2, col3=4);
```



1. [F.5 bloom索引](https://www.docs4dev.com/docs/zh/postgre-sql/10.7/reference/bloom.html)
2. 



----

#### 0x09 References

1. [PostgreSQL 9种索引的原理和应用场景](http://mysql.taobao.org/monthly/2019/04/08/)