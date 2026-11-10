---
source_file: "/Users/xmli/me/writer/db/02ACID.xmind"
source_name: "02ACID.xmind"
source_mtime: "2026-11-03 20:12:04"
source_mtime_ts: 1793707924.086
source_size: 192996
exported_at: "2026-11-07T11:47:20+08:00"
generator: "xmind2md 0.1.0"
---

# ACID

> 📎 原始文件: [02ACID.xmind](file:///Users/xmli/me/writer/db/02ACID.xmind)
> 🕒 导出时间: 2026-11-07 11:47:20
> 🗂 源文件修改时间: 2026-11-03 20:12:04

- Abortability(目标)
  - 1. 重试安全
2. 事务 X 幂等
- Consistency(约束)
  - 规则一致性
    - 主键约束
    - 唯一键约束
    - 外键约束
  - 读写同锁：update与if语句在同一个临界区中
- Isolation(手段)
  - 事务隔离级别
    - Read Uncommited
    - Read Commited
    - Repeatable Read
    - Searializable
- Durability(结果)
  - binlog
    - 1. 同步复制：安全+故障block
2. 异步复制：丢+快->数量多+分布广
3. 半同步复制：一主二从三副本
    - 自以为是的主节点：
1. 『休克』不自知
2. 不能靠自身信息判定自身状态
3. quorum：获得法定票数占系统节点半数以上
  - 两大绝学
    - 落盘：redolog原地复活
      - innodb_flush_log_at_trx_commit参数：
0. 只写log buffer, 每1s刷盘, 进程崩溃丢1s数据
1. 事务commit前fsync到磁盘，速度感人
2.只写page cache, 每1s刷盘, 除了宕机啥都不怕
    - 副本：binlog影分身
      - sync_binlog参数：
0. 只写page cache, 由OS刷盘, 速度快, 怕宕机
1. 事务commit前fsync到磁盘，速度感人
2+. 写page cache，每N次事务fsync一次
