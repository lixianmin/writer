

#### 1 名词

| 结构                 | Hadoop      | Storm      |
| -------------------- | ----------- | ---------- |
| 主节点               | JobTracker  | Nimbus     |
| 从节点               | TaskTracker | Supervisor |
| 应用程序             | Job         | Topology   |
| 把Proces(进程)称作： | Child       | Worker     |
| 计算模型             | Map/Reduce  | Spout/Bolt |



| 词条       | 用途                                                         |
| ---------- | ------------------------------------------------------------ |
| nimbus     | 负责资源分配和任务调度，对任务的分配信息会落到zookeeper上面的目录下 |
| supervisor | 1. 负责去zookeeper上的指定目录接受nimbus分配的任务，启动和停止属于自己管理的worker进程<br />2. 是当前物理机器上的管理者<br />3. 通过配置文件设置当前supervisor上启动多少个worker |
| topology   | 在storm cluster中，一个应用称为一个topology                  |
| worker     | 1. 一个topology由多个worker（jvm process）组成<br />2. config.setWorkerNum(n)设置的是**整个cluster中的worker数量**，而不是单个机器上的 |
| executor   | 1. 每个worker中有多个executor（thread）<br />2. 每个executor运行多个task（spout or bolt），task是实际的数据处理者 |
| task       | 最小任务执行单元，分为spout/bolt                             |
| spout      | 拓扑的消息源                                                 |
| bolt       | 拓扑的处理逻辑单元                                           |
|            |                                                              |



```java
Config conf = new Config();
conf.setNumWorkers(2); // 整个cluster设置jvm进程数为2

// 整体的并发度为 2+2+6 = 10，也就是10个线程。因为一共有2个worker，所以每个worker的线程数为10/2=5
topologyBuilder.setSpout("blue-spout", new BlueSpout(), 2); // set parallelism hint to 2

topologyBuilder.setBolt("green-bolt", new GreenBolt(), 2)
               .setNumTasks(4)	// executor=2，而task=4，因此每个线程处理2个任务
               .shuffleGrouping("blue-spout"); // 接收来自blue-spout的消息

topologyBuilder.setBolt("yellow-bolt", new YellowBolt(), 6)
               .shuffleGrouping("green-bolt"); // 接收来自green-bolt的消息

StormSubmitter.submitTopology(
        "mytopology",
        conf,
        topologyBuilder.createTopology()
    );
```



#### 2 运维

```shell

# 启动nimbus
nohup bin/storm nimbus &>/dev/null &

# 启动ui
nohup bin/storm ui &>/dev/null &


# 重启supervisor，先把旧的杀了
ps aux | grep supervisor  # 这个可能看到很多进程，有可能是混部，有可能全是workers
kill xxx

nohup bin/storm supervisor &>/dev/null &
```



-----

#### 9 References

1. [storm从入门到放弃(一)，storm介绍](https://www.cnblogs.com/intsmaze/p/7274361.html)
2. [What makes a running topology: worker processes, executors and tasks](https://storm.apache.org/releases/2.1.0/Understanding-the-parallelism-of-a-Storm-topology.html)
3. 