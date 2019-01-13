



1. IgniteCache中的基操好像都是原子的，包括getAndPutIfAbsent, getAndReplace这种，这些操作的共同点很可能是方法签名中throws TransactionException
2. 事务通过ignite.transactions().txStart()启动，这中间的基本操作的结果都是事务





----

#### References

1. [Apache Ignite中文站](https://liyuj.gitee.io/doc/java/)
2. [Ignite在线文档](https://apacheignite.readme.io/docs/)
3. [苏宁 11.11：基于 Apache Ignite 日均十亿数据对账实践应用](https://www.infoq.cn/article/PYlP47meo*y96NK5bjTP)
4. [Apache Ignite(一)：简介以及和Coherence、Gemfire、Redis等的比较](https://blog.csdn.net/xiaojin21cen/article/details/79072955)
5. 

