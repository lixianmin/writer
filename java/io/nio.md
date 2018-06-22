

---

#### 0x01 Channel简介

早期java使用stream抽象io操作，java1.4之后引入nio，然后开始使用channel抽象io操作。channel所抽象的层次并不是io操作所处理的数据，而是一个**已经建立好的支持io操作的实体的连接**，也就是说channl一定对应着一个实体连接，常见的是file与net连接。

channel的操作都是基于ByteBuffer缓冲对象的，而不是像Stream一样基于byte[] buffer。

unix五大IO模型参考图：

<img src="https://raw.githubusercontent.com/jasonGeng88/blog/master/201708/assets/java-nio-02.jpg" style="zoom:80%" />



------

#### 0x02 ByteBuffer

ByteBuffer的定位有些**类似于C#中的MemoryStream（但却是定长的）**：

1. 缓存：在IO操作中用作数据中转站，用于提高IO性能
2. Buffer下有8种buffer类，即ByteBuffer, MappedByteBuffer, CharBuffer, ShortBuffer, IntBuffer, LongBuffer, FloatBuffer, DoubleBuffer。基本数据类型中，除布尔类型外都有对应的缓冲区实现。
3. ByteBuffer本身是一个abstract类，内含工厂方法allocate(int)与allocateDirect(int)用于生成对象；
4. ByteBuffer是定长的，也就是capacity不会像MemoryStream那长自动增长；



所有缓冲区都有4个属性：capacity、limit、position、mark，并遵循：mark <= position <= limit <= capacity。

<img src="https://github.com/jasonGeng88/blog/raw/master/201708/assets/java-nio-03.png" style="zoom:50%" />

下表是对这4个属性的解释：

| 属性     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| capacity | 容量，即可以容纳的最大数据量；在缓冲区创建时被设定并且不能改变 |
| limit    | 表示缓冲区的当前终点，即写入、读取都不可超过该终点           |
| position | 位置，缓冲区下一个读写单元的位置，该值在每次读写时都会改变，为下次读写作准备 |
| mark     | 标记，调用mark()来设置mark=position，再调用reset()可以让position恢复到标记的位置 |



方法解析：

| methods       | description                                             |
| ------------- | ------------------------------------------------------- |
| asIntBuffer() | 创建IntBuffer视图                                       |
| clear()       | 重置position并设置limit= capacity; 计划**写数据前**调用 |
| flip() 轻弹   | 重置position并设置limit=position; 计划**读数据前**调用  |
| rewind()      | 重置position，计划**重新读数据前**调用                  |
| slice()       | 创建一个新的ByteBuffer，与当前的ByteBuffer共享内存      |
|               |                                                         |



---

#### 0x03 FileChannel

可能是**java中速度最快的文件读写方案**，相关特性如下：

1. FileChannel在多线程下是安全的
2. FileChannel不能配置为non-blocking mode
3. FileChannel.open(path, StandardOpenOption.READ) 打开一个只读的文件
4. transferTo() 可能会被操作系统优化成一个非常快速的直接传输
5. force() 方法将通道里尚未写入磁盘的数据强制写到磁盘上



FileChannel是**支持随机读写**的（因此RandomAccessFile可以废除了），相关操作包括：

1. position(long pos)/position()分别可以设置和获取当前文件指针；
2. read(dst, position) /write(src, position) 可以在随机读写文件；



------

#### 0x04 Files



| Target     | Code                                                         |
| ---------- | ------------------------------------------------------------ |
| 全文件读写 | byte[] bytes = Files.readAllBytes(path); <br/>List<String> lines = Files.readAllLines(path);<br/>**Stream<String> lines = Files.lines(path);** |
| char读写   | Files.newBufferedReader()                                    |
| byte读写   | Files.newInputStream()                                       |
| 文件copy   | Files.copy(source, destination)                              |
| 文件删除   | Files.deleteIfExists(path)                                   |
| 目录遍历   | Files.walkFileTree()                                         |



---

#### 0x05 读不出来 vs. 写不出去

在面向Stream的IO抽象模型中，read/write都是阻塞型方法，其中void write()系列方法都**没有返回值**，所以只要不发生异常，write()调用完成后可以保证数据写出到流中（此处忽略flush()相关内容）。

```java
// OutputStream接口
public void write(byte b[]);
public void write(byte b[], int off, int len);
public abstract void write(int b);

// Writer接口
public void write(char cbuf[]) 
abstract public void write(char cbuf[], int off, int len);
public void write(int c);
public void write(String str);
public void write(String str, int off, int len);
```



而在面向channel的抽象模型中，read/write都有一个int/long类型的返回值，它们代表着有效读写的数据长度。在non-blocking模式下要特别注意，nio的一次read/write可能**读不出来也写不出去**。因此需要小心处理。

一个向SocketChannel写数据的例子如下：

```java
String text = ...;
ByteBuffer buffer = ByteBuffer.wrap(text.getBytes(StandardCharsets.UTF_8));
// 循环写，直到写光buffer中的数据为止
while (buffer.hasRemaining()) {
    channel.write(buffer);
}
```



一个从SocketChannel中读数据并写出到FileChannel的例子如下：

```java
int readLength = 0;
// 1. read(buffer) > 0代表读出了数据；
// 2. 而buffer.position() != 0 代表buffer.compact()时buffer中有剩余数据
while ((readLength = srcChannel.read(buffer)) > 0 || buffer.position() != 0) {
    // flip()表示接下来要从buffer中get()了
    buffer.flip();
    // 注意totalLength需要记录在一个全局的位置，这个方法会被多次重入
    item.totalLength += dstChannel.write(buffer);
    // 1. 使用compact()而不是clear()，是因为前面的write()不能保证将数据全部写出
    // 2. compact()方法是为接下来read()数据做准备，就像flip()是为write()数据做准备一样
    // 3. compact()会设置position(remaining());当有剩余数据时会导致buffer.position()!= 0
    buffer.compact();
}

// readLength == -1 代表对方写完数据了
// 在使用http get一张image时，server会通知CONTENT-LENGTH，需要client自行判断读结束。
if (readLength == -1 || item.isDone()) {
    key.cancel();
    srcChannel.close();
    _items.remove(srcChannel);
}
```



---

#### 0x06 存疑问题

SocketChannel的non-blocking模式体现在connect会立即返回；ServerSocketChannel的non-blocking模式体现在accept()会立即返回；

SocketChannel在non-blocking mode下，调用channel.connect(remote);时，是否有可能直接返回true连接成功了，在这种情况下注册的OP_CONNECT还能在select()轮询时收到对应的事件通知吗？

```java
channel.configureBlocking (false);
channel.connect (remote);
channel.register (selector, SelectionKey. OP_ CONNECT| SelectionKey. OP_ READ)；
```



---

#### 0x07 References

1. [FileChannel](http://wiki.jikexueyuan.com/project/java-nio/filechannel.html)
2. [JAVA NIO 一步步构建I/O多路复用的请求模型](https://github.com/jasonGeng88/blog/blob/master/201708/java-nio.md)
