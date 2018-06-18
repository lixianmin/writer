

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

#### java.nio.Buffer

1. 缓存：在IO操作中用作数据中转站，用于提高IO性能
2. 其下有8种buffer类，即ByteBuffer, MappedByteBuffer, CharBuffer, ShortBuffer, IntBuffer, LongBuffer, FloatBuffer, DoubleBuffer
3. ByteBuffer本身是一个abstract类，内含工厂方法allocate(int)与allocateDirect(int)用于生成对象；



所有缓冲区都有4个属性：capacity、limit、position、mark，并遵循：mark <= position <= limit <= capacity，下表格是对着4个属性的解释：

| 属性     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| Capacity | 容量，即可以容纳的最大数据量；在缓冲区创建时被设定并且不能改变 |
| Limit    | 表示缓冲区的当前终点，不能对缓冲区超过极限的位置进行读写操作。且极限是可以修改的 |
| Position | 位置，下一个要被读或写的元素的索引，每次读写缓冲区数据时都会改变改值，为下次读写作准备 |
| Mark     | 标记，调用mark()来设置mark=position，再调用reset()可以让position恢复到标记的位置 |



##### References

1. [ByteBuffer常用方法详解](https://blog.csdn.net/u012345283/article/details/38357851)



--------

#### 问张路

```java
	// HttpCodec中,这个send()与下面的sendChunk()第二个参数都是byte[]，为何处理方式不同？
	public static boolean send(IoSession session, byte[] data)
	{
		return NetManager.write(session, data);
	}

	public static boolean send(IoSession session, Octets data)
	{
		return NetManager.write(session, data);
	}

	public static boolean sendChunk(IoSession session, byte[] chunk)
	{
		int n = chunk.length;
		return n <= 0 || NetManager.write(session, ByteBuffer.wrap(chunk, 0, n));
	}
```

