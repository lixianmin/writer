---
source_file: "/Users/xmli/me/writer/java/Java并发.xmind"
source_name: "Java并发.xmind"
source_mtime: "2026-11-03 20:12:03"
source_mtime_ts: 1793707923.983
source_size: 311166
exported_at: "2026-11-07T11:47:20+08:00"
generator: "xmind2md 0.1.0"
---

# Java并发3C问题

> 📎 原始文件: [Java并发.xmind](file:///Users/xmli/me/writer/java/Java%E5%B9%B6%E5%8F%91.xmind)
> 🕒 导出时间: 2026-11-07 11:47:20
> 🗂 源文件修改时间: 2026-11-03 20:12:03

- 原子性：CPU线程切换导致
  - Lock：互斥性
    - 并发度
      - 普通锁：串行
      - 读写锁：读读并发
      - MVCC：读写并发
    - 粒度
      - 无锁：FAST
      - 普锁：语言锁
      - 重锁：分布式锁，RedLock，zk锁
    - 读写同框
      - 一锁二读三更新
      - 单独的数据读可用于展示，也只可用于展示，不可用于回写。如果想修改数据并回写数据源的话，要求读/写在一个互斥区间中完成
      - 双重锁检查
    - 常见坑
      - 读不加锁
        - 对简单变量，通常write我们会记得加锁，但有可能纠结read加不加，结论是：加锁
      - 多把锁保护
同一个资源
        - 普通方法与static方法上的synchronized不是同
一把锁，它们的锁定对象分别是this与Class
    - 死锁
      - 互斥条件
        - 原因：共享资源 X 和 Y 只能被一个线程占用
        - 方案：无锁FAST（Final, Atomic, Stack, ThreadLocal）
      - 请求与保持
        - 问题：在等待资源 Y 的时候，不释放已经到手的资源 X
        - 方案：一次性申请所有资源
        - 创建一个Allocator，负责同时分配多把锁资源
        - 等待通知 wait(), notifyAll()：线程首先获取互斥锁，当线程要求的条件不满足时，释放互斥锁，进入等待状态；当要求的条件满足时，通知等待的线程，重新获取互斥锁。
      - 不可剥夺
        - 问题：其他线程不能强行抢占线程 T1 占有的资源
        - 方案：主动释放已经占用的资源
        - 中断、超时、非阻塞
LockInteruptibly, tryLock()
        - select...for update
          - wait 1 等待完整结果集1s
          - nowait无法返回完整结果时立即返回
          - skip locked 跳过锁定行，返回部分结果
      - 环路等待
        - 按顺序请求锁，比如按userid排序
  - 事务：原子性
    - 锁：互斥性，发生异常时变量会处于中间状态
    - 事务：原子性，通过回滚确保不处于中间状态
- 并发三思
  - 分工
    - 码农负责
  - 互斥
    - Current Read
      - 参考数据库read view中的current read
  - 同步
    - Condition
- 有序性：Compiler指令重排
- 可见性：Cache导致
