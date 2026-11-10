---
source_file: "/Users/xmli/me/writer/notes/computers/i-heart-logs/i-heart-logs.xmind"
source_name: "i-heart-logs.xmind"
source_mtime: "2026-11-03 20:12:04"
source_mtime_ts: 1793707924.013
source_size: 591592
exported_at: "2026-11-07T11:47:20+08:00"
generator: "xmind2md 0.1.0"
---

# I heart logs

> 📎 原始文件: [i-heart-logs.xmind](file:///Users/xmli/me/writer/notes/computers/i-heart-logs/i-heart-logs.xmind)
> 🕒 导出时间: 2026-11-07 11:47:20
> 🗂 源文件修改时间: 2026-11-03 20:12:04

- 1. Introduction
  - 1. 世人以为kafka处理的是text log，其实是commit log
2. 同一个kafka分区内的offset是逻辑时间戳
3. file: bytes的数组，table: record的数组
  - Logs in Databases
    - 1. 基于log的pub/sub机制，传输数据到replicas
2. 基于log的consistency机制，定序、更新
  - Logs in distributed Systems
    - 1. state machine replication=确定性过程+相同初始态+相同&有序input
2. deterministic: 不依赖时间，无随机性
3. log: 定序；相同&有序input
4. offset: 时钟；相同moment/offset的副本状态相同
5. partition: 横向扩展；分区内逻辑时钟，分区间墙上时钟
    - 1. 物理日志（row based），逻辑日志（statement based）
2. 状态机模型：请求先定序，各replicas依次处理请求
3. 主备模型：leader处理请求，其它replicas与leader同步
  - Changelog 101: Tables and Events Are Dual
- 2. Data Integration
  - 1. 整合组织的所有数据，为所有需要的系统服务
2. ETL：一种特定的数据整合手段
  - 1. Acquisition: 可靠、完整的数据流（日志）
2. Semantics: 数据建模、一致性、语义理解
3. Understanding: 可视化、报表
4. Automation: 算法处理、预测
  - Data Integration: Two Complications
    - 1. 多样化：金融数据历来都是以事件为中心的
2. 专用数据系统：OLAP, 索引, 批处理, 图分析
  - Log-Structured Data Flow
    - 1. 所有数据整合到日志中心，提供实时订阅
2. 订阅者根据逻辑时钟，度量消费进度、是否最新
3. 异步生产和消费，对不同消费速度的订阅很友好
4. log比msg或pub/sub更贴切描述数据复制实现
5. 日志≈消息+有序=原子广播
  - My Experience at LinkedIn
    - 1. 数据经常双向流动，很多DB既是src又是dest
2. 使用中心化的log系统作为hub
  - Relationship to ETL and the Data Warehouse
    - 1. DW: 批量查询基础设施，适合报表和临时分析
2. ETL: 提取, 清洗, 重组织 (星型, 雪花, 列存储)
  - ETL and Organizational Scalability
    - 1. 典型问题：DW组负责收集和清洗其它组生产的数据。因为缺少事先对齐，数据可能很难提取和转换
2. 改进：生产者将结构化数据发送到中心化的log系统中
3. 有组织的规划对于亚秒级数据收集和使用具有重大意义
  - Where Should We Put the Data Transformations
    - 1. 生产者：最了解数据，无信息损失
2. log系统：添加附加信息，并实时输出到新log
3. 消费者: 面向目标系统的聚合、转换，类似ETL
  - Decoupling Systems
    - 1. 采集text log, 容易导致数据流与处理流程耦合
2. log服务则是event driven，利于解耦
  - Scaling a Log
    - 1. 分区：分区内逻辑时钟，分区间墙上时钟
2. 批处理：send、落盘、复制、receive、ack
3. 零拷贝：
    - 1. 100b的msg，3副本，单线程，写75w/s，读90w/s
- 3. Logs and Real-time Stream Processing
  - 1. 流处理是一种连续处理数据的基础设施，跟MapReduce一样是一种通用计算模型
2. web公司不需要批量收集数据，网站数据是连续产生的，批量收集通常意味着手工操作或缺少数字化
3. 批处理实质在模仿窗口大小为一天的连续计算任务
4. 流处理是一种泛化的批处理，它按用户指定的频率输出数据，而不需要等待拿到所有输入数据
5. 流处理弥补了在线oltp与离线olap之间的空白
  - Data Flow Graphs
  - Logs and Stream Processing
    - 1. 多订阅：利于数据流动、调试、监控、开发
2. 有序：保障多个物化视图的数据一致性
3. 缓冲&每消费者：消费过缓 (丢弃, 背压, 缓冲)
    - 1. 丢弃：只在少数情况下可以接受，非通用方案
2. 背压：具备可传递性（TCP, GC），可能阻塞整个系统的所有上下游
3. 缓冲：将log看作超大缓冲，允许消费者随时失败或重启，同时不影响其它消费者
  - Reprocessing Data: The Lambda Architecture and an Alternative
    - Lambda架构
      - 1. CRUD简化成CR：大数据（hadoop）系统都这么做；重新计算成为可能；鲁棒性
2. 持久化层=批处理层+实时层：被现代流处理代替 ([链接](http://nathanmarz.com/blog/how-to-beat-the-cap-theorem.html))
    - 1. 存储原始input：不支持修改
2. 支持反复计算：代码是不断演化的 (需求, bug)
    - 1. 批处理与流处理并行，同样的逻辑需维护2份
2. storm或hadoop这类分布式框架编程很复杂
3. 方案：使用更高级的框架编码，编译到流处理和批处理系统中；运维成本、调试成本仍然很高
4. 一个有趣的类比是设计跨DB的ORM，相当困难
    - 1. 使用kafka保存足够的历史log（比如30天）
2. 当需要重算的时候，启用job2，并输出到新表
3. 当job2赶上进度之后，切换应用从新表读数据
4. 停止job1，并删除旧的输出表
    - 1. 只有代码变化时才需要重跑处理逻辑
2. 新旧表并存一段时间：快速回滚、A/B测试、bugfix
3. 支持输出到Hive等离线分析系统
4. 对比lambda架构：单一框架中完成开发全流程
  - Stateful Real-Time Processing
    - 1. 计数、聚合、join类流处理是带状态的
2. stream&table join：使用DB (changelog)丰富事件流
    - 1. 内存：进程crash则状态丢失
2. 远程存储：缺少本机存储，网络开销大
3. 本机DB：changelog，crash后状态重建，支持三方订阅
  - Log Compaction
    - 1. event data: 保留预定义时间/空间范围内的数据
2. keyed updates: 日志压缩（按key合并）
- 4. Building Data Systems with Logs
  - 1. 将组织的所有系统和数据流，看作是一个单一的分布式DB，将查询系统看作是索引，将流处理系统看作是到物化视图的触发器
2. 降本增效：大量+独立+小服务 => 单个+共享+大服务
  - Unbundling?
    - 1. 多系统并立的现状将继续：数据集成问题仍是成功使用数据的关键，因此log将显示尤为重要
2. 通用系统进一步吸纳各类功能，向超级系统演化
3. 新生代数据系统技术栈是开源的：这跟最终用户关系不大，但会极大加速系统开发迭代
  - The Place of the Log in System Architecture
    - 1. 解决数据一致性：(最终|立即)一致性，有序日志
2. 在不同的节点间data replication
3. 为writer提供commit语义：确保持久化后再ack
4. 对外提供数据的pub/sub服务
5. 恢复失败的副本上的数据；或创建新副本
6. 在不同的节点间做数据rebalance
    - 1. log：处理分布式系统中最微妙的那些问题
2. 服务层：业务相关设计 (存储结构、索引设计)
    - 1. 确保read-your-write语义：对比client请求与server索引中的时间戳，在数据更新完成前不返回请求，否则会返回stale data
2. 服务集群可以没有leader，log会保证正确性
    - 1. 复制状态机=全量快照+增量log
2. 完整日志+log压缩：通用方案，独立于具体系统
    - 1. 可使用有效的log存储机制。kafka支持PB级存储
2. 很多服务需要更好的硬件，比如index需要大量内存。存储log只需要普通硬盘，成本被大大均摊
