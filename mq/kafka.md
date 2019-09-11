



​	

---

#### 0x01 install

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





----

#### 0x02 shell


```bash
# 查看topics
kafka-topics --zookeeper 172.20.20.120:2181  --list
kafka-topics --zookeeper 172.20.20.120:2181  --describe --topic users
kafka-topics --zookeeper 172.20.20.120:2181  --describe --topic __consumer_offsets


# 删除topic
kafka-topics --zookeeper 172.20.20.120:2181  --delete --topic users

# 消息处理
kafka-console-producer --broker-list 172.20.20.120:9092 --topic users
kafka-console-consumer --bootstrap-server 172.20.20.120:9092 --topic users --from-beginning

# 查看某group的offset
kafka-consumer-groups --bootstrap-server 172.20.20.120:9092 --describe --group user-behaviour

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



------

#### 0x08 FAQ

1. 重复消费的问题并没有很好的解决方案，需要在发送时使用snowflake算法创建一个分布式的uuid，然后接收端自己根据唯一id处理
2. producer是不需要group，只有consumer才需要
3. 创建多个partition会速度快，但顺序就无法保证了
4. kafka采用顺序写硬盘的方式，有可能比随机写内存速度快
5. leader会维护一个与其基本保持同步的Replica列表，该列表称为ISR(in-sync Replica)，每个Partition都会有一个ISR，而且是由leader动态维护。如果一个flower比一个leader落后太多，或者超过一定时间未发起数据复制请求，则leader将其重ISR中移除。当ISR中所有Replica都向Leader发送ACK时，leader才commit
6. 



------

#### 0x09 References

1. [kafka-docker](https://hub.docker.com/r/wurstmeister/kafka/)
2. [Kafka系列（四）Kafka消费者：从Kafka中读取数据](http://www.dengshenyu.com/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F/2017/11/14/kafka-consumer.html)
3. [Kafka重复消费和丢失数据研究](http://blog.zollty.com/b/archive/about-kafka-repeated-consumption-and-lost-data.html)
4. [Twitter的分布式自增ID算法snowflake (Java版)](https://www.cnblogs.com/relucent/p/4955340.html)
5. [kafka无消息丢失配置](https://www.jianshu.com/p/741c506cc3ff)
6. [Kafka HA Kafka一致性重要机制之ISR](https://blog.csdn.net/qq_37502106/article/details/80271800 )

