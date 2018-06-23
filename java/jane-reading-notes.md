

---

1. 所谓safe操作应该是为了回滚，所以你看到DB相关的类都带Safe的内部类。DBRole.Safe role得到role对象，这样，对role的所有操作都是可以回滚的。
2. new P()是启动一个Procedure，是新的线程处理，onProcess()里面需要使用Safe操作
3. 从编程的角度，BIO比NIO要更新容易理解，因此大多数应用程序在调用网络层的时候通常会期望一个阻塞模式，最好的解决方案就是通过写一个阻塞模式的模仿框架来隐藏掉这一表象。这就是 MINA 所做的事情！



---

#### HttpCodec



```java
	public static boolean sendChunk(IoSession session, String chunk)
	{
        // 1. 在net发送数据之前首先将String对象以UTF-8格式编码为bytes数据
        // 2. 这里使用了StandardCharsets.UTF_8
		return sendChunk(session, chunk.getBytes(StandardCharsets.UTF_8));
	}
```



---



##### References

1. 



--------

#### 问张路

