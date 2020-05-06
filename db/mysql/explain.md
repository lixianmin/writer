



---

#### 0x01 Explain输出列



| Column                                                       | JSON Name       | Meaning                                                      |
| ------------------------------------------------------------ | --------------- | ------------------------------------------------------------ |
| [`id`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_id) | `select_id`     | The `SELECT` identifier                                      |
| [`select_type`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_select_type) | None            | 查询的类型，主要包括普通查询、联合查询、子查询               |
| [`table`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_table) | `table_name`    | 被访问的数据库表名                                           |
| [`partitions`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_partitions) | `partitions`    | The matching partitions                                      |
| [`type`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_type) | `access_type`   | join查询的类型                                               |
| [`possible_keys`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_possible_keys) | `possible_keys` | 1. MySQL可能使用的索引<br />2. 如果值是NULL，则表示没有相关索引。这时如果想提高性能，可检验where子句，看是否引用了某些字段，并检查字段是否适合索引 |
| [`key`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_key) | `key`           | The index actually chosen                                    |
| [`key_len`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_key_len) | `key_length`    | 1. MySQL决定使用的键长度<br />2. 该值可以反应出一个多重索引中MySQL实际使用了哪部分 |
| [`ref`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_ref) | `ref`           | 哪个字段或常数与key一起被使用                                |
| [`rows`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_rows) | `rows`          | 1. MySQL要遍历多少行数据才能找到需要的结果<br />2. 估算值，不准确 |
| [`filtered`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_filtered) | `filtered`      | Percentage of rows filtered by table condition               |
| [`Extra`](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain_extra) | None            | Additional information                                       |



----

#### 0x02 type字段



type是判断sql语句是否得到优化的重要指标，结果值从好到坏依次是：



| type字段        | 描述                                 |
| --------------- | ------------------------------------ |
| system          | 系统表                               |
| const           | 读常量                               |
| eq_ref          | 最多一条匹配结果，通常是通过主键访问 |
| ref             | 被驱动表索引引用                     |
| fulltext        | 全文索引检索                         |
| ref_or_null     | 带NULL值的索引查询                   |
| index_merge     | 合并索引结果集                       |
| unique_subquery | 子查询中返回的字段是唯一索引         |
| index_subquery  | 子查询返回的是普通索引               |
| **range**       | 使用索引进行范围扫描                 |
| index           | 全索引扫描                           |
| all             | 全表扫描                             |



1. 一般来说，查询至少应该保证到range级，最好能达到ref级，all为全表扫描，是最坏的情况，这时往往是没有使用上索引





----

#### 0x03 Extra字段



| Extra字段                   | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| impossible where            | 通过收集到的统计信息判断出不可能存在的结果                   |
| only index                  | 1. 信息只能用索引树中的信息检索，比扫描整个表要快            |
| select tables optimized way | 使用了聚合函数，并且MySQL进行了快速定位，通常是min, max, count(*) |
| using filesort              | 1. 文件排序<br />2. sql语句中**包含order by，并且无法只使用索引就完成排序** |
| using index                 | 覆盖索引，无需回表                                           |
| using index condition       | 条件下推                                                     |
| using temporary             | 临时表，常见于**order by 和group by**                        |
| using where                 | 后过滤(Post filter)，发生于MySQL服务器层，而不是innodb层     |
| where used                  | 只用索引不够，使用了where限制                                |
|                             |                                                              |















