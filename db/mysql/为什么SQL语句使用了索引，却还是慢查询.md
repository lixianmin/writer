---
source_file: "/Users/xmli/me/writer/db/mysql/为什么SQL语句使用了索引，却还是慢查询.xmind"
source_name: "为什么SQL语句使用了索引，却还是慢查询.xmind"
source_mtime: "2026-11-03 20:12:04"
source_mtime_ts: 1793707924.104
source_size: 54679
exported_at: "2026-11-07T11:47:20+08:00"
generator: "xmind2md 0.1.0"
---

# 为什么SQL语句使用了索引，却还是慢查询

> 📎 原始文件: [为什么SQL语句使用了索引，却还是慢查询.xmind](file:///Users/xmli/me/writer/db/mysql/%E4%B8%BA%E4%BB%80%E4%B9%88SQL%E8%AF%AD%E5%8F%A5%E4%BD%BF%E7%94%A8%E4%BA%86%E7%B4%A2%E5%BC%95%EF%BC%8C%E5%8D%B4%E8%BF%98%E6%98%AF%E6%85%A2%E6%9F%A5%E8%AF%A2.xmind)
> 🕒 导出时间: 2026-11-07 11:47:20
> 🗂 源文件修改时间: 2026-11-03 20:12:04

-  慢查询
  - 由SQL语句的执行时间决定，跟索引无直接因果
  - long_query_time，默认10s，生产通常设置为1s
- 使用索引
  - explain的结果中key!= null
  - 利用索引的快速搜索功能，并有效减少扫描行数
- 扫描行数
是关键
  - 使用了索引，但却是全表扫描
    - explain select * from t where id > 0;
  - 索引的过滤性
    - 以age索引过滤全中国的人数并不理想
    - 大表的索引需要足够好的过滤性才有意义
    - mysql5.6: 索引下推，避免回表（耗时）
    - mysql5.7: 虚拟列
