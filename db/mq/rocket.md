



----

#### 基本概念

1. group：逻辑概念，不同的group可以消费同一个topic下的所有消息
2. queue：物理概念，同一个topic下不同的queue之消息是互斥的，rocket的queue等价于kafka中的partition，简记为 PQ
3. 优先级：区分优先级只能使用不同的topic，不能使用不同的tag
   1. 同一个consumer订阅不同的tag以最后一次订阅的那个为准
   2. 即使在同一个项目中创建两个不同的consumer，订阅同一个topic的不同tag好像也不行，会收不到消息，这非常奇怪。曾尝试创建完成不同的properties对象，也只能收到部分消息



---

#### 0x02 分区顺序消息

1. 用户注册需要发送发验证码，以用户 ID 作为 Sharding Key，那么同一个用户发送的消息都会按照发布的先后顺序来消费。
2. 电商的订单创建，以订单 ID 作为 Sharding Key，那么同一个订单相关的创建订单消息、订单支付消息、订单退款消息、订单物流消息都会按照发布的先后顺序来消费。



阿里巴巴集团内部电商系统均使用分区顺序消息，既保证业务的顺序，同时又能保证业务的高性能。

**注意事项**

使用顺序消息时，请注意以下几点：

- 顺序消息暂不支持广播模式。
- 建议同一个 Group ID 只对应一种类型的 Topic，即不同时用于顺序消息和无序消息的收发。
- 顺序消息不支持异步发送方式，否则将无法严格保证顺序。



----

#### 0x09 References

1. [Rocket顺序消息](https://help.aliyun.com/document_detail/49319.html?spm=a2c4g.11186623.6.553.6ebd5d4akLicwC)
2. 