

----

#### Introduction



1. 到目前为止，我们使用的东西都是ChannelHandler，进一步的都是ChannelnboundHandler，包括Channelnitialier, SimpleChannelInboundHandler都是
2. 关于netty出站入站的问题，记住关键词：**吞吐**，就跟人吃饭一样，入站（收数据）当然是**吞**，吞自然是从头部开始的，出站（吐数据）自然是**吐**，吐的时候当然从尾部（胃部）发力...... 我靠，突然有点儿恶心
3. 每一个channel有一个eventLoop
4. channel是线程安全的