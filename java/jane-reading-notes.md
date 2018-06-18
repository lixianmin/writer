

---

1. 所谓safe操作应该是为了回滚，所以你看到DB相关的类都带Safe的内部类。DBRole.Safe role得到role对象，这样，对role的所有操作都是可以回滚的。
2. new P()是启动一个Procedure，是新的线程处理，onProcess()里面需要使用Safe操作



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

