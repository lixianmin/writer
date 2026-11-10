---
source_file: "/Users/xmli/me/writer/db/mysql/MySQL索引.xmind"
source_name: "MySQL索引.xmind"
source_mtime: "2026-11-03 20:12:04"
source_mtime_ts: 1793707924.103
source_size: 212917
exported_at: "2026-11-07T11:47:20+08:00"
generator: "xmind2md 0.1.0"
---

# MySQL索引

> 📎 原始文件: [MySQL索引.xmind](file:///Users/xmli/me/writer/db/mysql/MySQL%E7%B4%A2%E5%BC%95.xmind)
> 🕒 导出时间: 2026-11-07 11:47:20
> 🗂 源文件修改时间: 2026-11-03 20:12:04

- 索引模型
  - Hash表
    - 只可用于等值查询
  - 有序数组
    - 适合于等值查询和范围查询
    - 插入成本太高，只适用于静态存储引擎
  - 搜索树
- 字符串索引
  - 完整索引
    - 占用空间较大
  - 前缀索引
    - alter table SUser add index index2(email(6));
    - 节省空间，但会增加扫描行数，增加查询成本
    - 定义好前缀索引的长度是关键
      - mysql> select 
 count(distinct left(email,4)）as L4,
 count(distinct left(email,5)）as L5,
 count(distinct left(email,6)）as L6,
from SUser;
    - 前缀索引无法使用覆盖索引，需要回表
  - 倒序存储
    - 当前缀区分度不高时（身份证号，全文件路径）可考虑倒序存储，再创建前缀索引
    - mysql> select field_list from t where id_card = reverse('input_id_card_string');
  - 配套Hash字段
    - mysql> alter table t add id_card_crc int unsigned, add index(id_card_crc);
    - mysql> select field_list from t where id_card_crc=crc32('input_id_card_string') and id_card='input_id_card_string'
    - 使用trigger在每次insert, update时更新crc值
- 选错索引
  - 优化器使用“采样统计”估算是否使用索引以及使用哪些索引，因此可能误判
    - “基数”越大，索引的区分度越好
    - show index from t;
  - 重新统计索引信息
    - analyze table t;
  - 强制使用特定索引
    - select * from t force index(a) where...
    - 如果索引改了名字，会影响这种SQL语句
