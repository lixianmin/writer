

----

#### 1 基本概念

##### 01 basics

|          |                                                              |      |
| -------- | ------------------------------------------------------------ | ---- |
| leader   | kafka的读写数据都是在leader节点                              |      |
| follower | 1. follower节点不提供服务<br />2. follower节点自己从leader定期pull数据 |      |
| ISR      | 1. in-sync Replica<br />2. kafka副本集合的子集，该子集中的所有节点都向leader回复ack之后，leader才会commit<br />3. 用于实现半同步复制<br />4. leader如果觉得某个follower落后太多，会将其从ISR中移除 |      |



----

#### 2 行为准则

##### 01 新建topic

1. 考虑好分区策略：区分一下**event stream还是change log**，如果是change log则**是否考虑log compact**，因此需要考虑按什么key进行分区的问题。



##### 02 发送数据

1. 按照建立topic时既定的分区策略进行分区。
2. 使用**caffeine+MySQL+threadLocal+每个item记住自己的topic**：批量从MySQL获取全局唯一id，批量发送到kafka。
3. 批量发送到kafka时，**采用size+timeout结合的方式**。如果不对size进行考量，那么可能会OOM；如果不对timeout进行考量，可能一天都发不出数据去。
4. **合法性检查与数据清洗（fix）：空字符串是否合理？是否超出了MySQL允许的表字段长度？数字为负值是否合理？如果是收集到的json串，还要考虑数据类型是否正确**
5. 



----

#### 3 shell

##### 01 启动kafka

```shell
# 启停
bin/kafka-server-start.sh config/server.properties &
bin/kafka-server-stop.sh

# 查看进程是否存在
ps aux | grep kafka
```



##### 02 查看topics

```shell
# 查看topics
bin/kafka-topics --zookeeper localhost:2181  --list
bin/kafka-topics --zookeeper localhost:2181  --describe --topic users
bin/kafka-topics --zookeeper localhost:2181  --describe --topic __consumer_offsets
```



```shell
bin/kafka-topics.sh --zookeeper localhost:8555 --describe --topic users

# 输出如下内容
# PartitionCount:4 			这个主题分布在4个分区上，分区的个数 <= broker数
# ReplicationFactor:2 	每个分区有2个副本
# Leader: 11						leader是11节点，其它的是follower节点
# Replicas: 11, 13			副本列表
# Isr: 13, 11						In sync Replicas列表

Topic:users	PartitionCount:4	ReplicationFactor:2	Configs:
	Topic: users	Partition: 0	Leader: 11	Replicas: 11,13	Isr: 13,11
	Topic: users	Partition: 1	Leader: 12	Replicas: 12,14	Isr: 14,12
	Topic: users	Partition: 2	Leader: 13	Replicas: 13,10	Isr: 13    # 这里10号节点就被踢出ISR了
	Topic: users	Partition: 3	Leader: 14	Replicas: 14,11	Isr: 14,11
```



##### 03 查看borkers

```shell
# 打开zookeeper的shell
bin/zkCli.sh -server localhost:8555

# 查看根目录
ls /

# 查看brokers
ls /brokers/ids
```



##### 09 其它


```bash
# 删除topic
bin/kafka-topics.sh --zookeeper localhost:2181  --delete --topic users

# 消息处理
bin/kafka-console-producer.sh --broker-list localhost:8592 --topic users
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic users --from-beginning

# 查看某group的offset
kafka-consumer-groups --bootstrap-server localhost:9092 --describe --group user-behaviour

```



docker-compose.yml文件

```yml
version: '2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    ports:
      - "2181:2181"
  kafka:
    build: .
    #设置网络模式 host模式，此模式和宿主机公用一个网络
    network_mode: "host"
    ports:
      # 映射到外面的9090端口
      - "9090:9092"
    environment:
      # docker所在的主机ip，不要使用localhost或127.0.0.1
      KAFKA_ADVERTISED_HOST_NAME: ${KAFKA_ADVERTISED_HOST_NAME}
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      
      # 好像不管用，用命令行仍然可以创建
      # KAFKA_AUTO_CREATE_TOPICS_ENABLE: 'false'
      
      # 创建topic1，拥有1个partition，3个replicas；Topic2的cleanup.policy设置为compact
      KAFKA_CREATE_TOPICS: "topic1:1:3, Topic2:1:1:compact"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```



----

#### 4 install

```bash
# 下载，然后改docker-compose.yml文件
git clone https://github.com/wurstmeister/kafka-docker.git
```



centos安装kafkacat

```shell
# 前置条件
yum install cmake doxygen
yum install gcc-c++  cyrus-sasl-devel.x86_64
yum install curl-devel

# 参考：https://medium.com/@frank.munz/kafkacat-on-amazonlinux-centos-d7ded88042e8
sudo yum update -y
sudo yum install git -y
# make sure you have the necessary dev tools avail, 
# or run the ff command:
sudo yum group install “Development Tools” -y
git clone https://github.com/edenhill/kafkacat.git
cd kafkacat/
./bootstrap.sh
./kafkacat -h
```



#### 5 清理策略

##### cleanup.policy: delete



##### cleanup.polocy: compact

```properties
cleanup.policy: compact
segment.bytes: 每个segment的大小，达到这个大小会产生新的segment, 默认是1G
segment.ms: 配置每隔n ms产生一个新的segment,默认是168h,也就是7天
retention.bytes: 总的segment的大小限制，达到这个限制后会删除旧的segment,默认值为-1，就是不会删除
retention.ms: segment的最后写入record的时间-当前时间 > retention.ms 的segment会被删除，默认是168h, 7天

# 可以容忍的脏数据比例，默认值0.5。清理数据需要对磁盘进行读写，如果数据只有很小的重复比例，实际上没有必要清理。
# 如果你的机器性能可以，而且数据量较大的话，这个值可以设置更小一些，比如0.1，这样对consumer更友好。
min.cleanable.dirty.ratio: 0.5

# 默认值0，也就是不会根据消息投递的时间来决定消息是否应该被compact。
min.compaction.lag.ms: 0
```







------

#### 8 FAQ

1. 重复消费的问题并没有很好的解决方案，需要在发送时使用snowflake算法创建一个分布式的uuid，然后接收端自己根据唯一id处理
2. producer不需要group，只有consumer才需要
3. 创建多个partition会速度快，但顺序就无法保证了
4. kafka采用顺序写硬盘的方式，有可能比随机写内存速度快
5. leader会维护一个与其保持同步的Replicas列表，该列表称为ISR(in-sync Replica)，每个Partition都会有一个ISR，而且是由leader动态维护。如果一个follower比一个leader落后太多，或者超过一定时间未发起数据复制请求，则leader将其从ISR中移除。当ISR中所有Replica都向Leader发送ACK时，leader才commit
6. 



------

#### 9 References

1. [kafka-docker](https://hub.docker.com/r/wurstmeister/kafka/)
2. [Kafka系列（四）Kafka消费者：从Kafka中读取数据](http://www.dengshenyu.com/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/2017/11/14/kafka-consumer.html)
4. [Twitter的分布式自增ID算法snowflake (Java版)](https://www.cnblogs.com/relucent/p/4955340.html)
5. [kafka无消息丢失配置](https://www.jianshu.com/p/741c506cc3ff)
6. [Kafka HA Kafka一致性重要机制之ISR](https://blog.csdn.net/qq_37502106/article/details/80271800)

