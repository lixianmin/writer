

----

#### 0x01 java.io体系

##### java.io流分类

从不同的角度，流可以分为以下类别：

- 按数据流的方向不同分为**输入流和输出流**；
- 按处理数据的单位不同分为**字节流和字符流**；
- 按功能的不同分为**节点流和包装流（处理流）**。节点流是底层流，对应着数据的来源和目的地的对象；包装流是对底层流做包装的上层流，是加缓冲区的流；



##### 字节流 vs. 字符流

java.io流共涉及40多个类，看上去很杂乱，但实际上这此类都是从如下4个抽象类基类中派生出来的。

- **字节流**：InputStream/OutputStream: 面向byte的输入输出流的基类，比如BufferedInputStream, BufferedOutputStream;
- **字符流**：Reader/Writer: 面向char的输入输出流的基类，常用的如BufferedReader, BufferWriter;



##### 节点流 

| Class                                                        | Description                           |
| ------------------------------------------------------------ | ------ |
| ByteArrayInputStream<br>ByteArrayOutputStream | 1. 变长内存Stream，有些类似于C#中的MemoryStream。<br>2. 与之类似的是ByteBuffer（**定长**，但ByteBuffer.allocateDirect(1024* 1024*1024)会分配一个足够大的buffer给应用） |
| CharArrayReader<br>CharArrayWriter | 内存数组的输入、输出 |
| FileInputStream<br>FileOutputStream<br>FileReader<br>FileWriter | 将File对象转化为对应的Stream或Reader/Writer，构造方法的参数是File对象或fileName字符串 |
| PipedInputStream<br>PipedOutputStream<br>PipedReader<br/>PipedWriter | 管道输入、输出流，用来在线程间通信 |
| StringReader<br>StringWriter | 内存字符串的输入、输出 |



##### 包装流/处理流

| Class                                                        | Description                           |
| ------------------------------------------------------------ | ------ |
| BufferedInputStream<br>BufferedOutputStream<br>BufferedReader<br>BufferedWriter  | 缓冲流                                 |
| DataInputStream<br>DataOutputStream                          | 为stream增加对primitive数据的读写能力，如readInt(), readFloat(), readUTF()等 |
| InputStreamReader<br>OutputStreamWriter | 是字节流与字符流之间通信的桥梁 |



---

#### 0x02 File

File对象用于操作文件目录，并不包含读写文件的方法。

| method      | Description            |
| ----------- | ---------------------- |
| delete()    | 删除文件               |
| exists()    | 判断文件或目录是否存在 |
| isFile()    | 是否是文件             |
| mkdirs()    | 递归创建目录           |
| length()    | 文件长度               |
| listFiles() | 当前目录的文件列表     |



---

#### 0x03 Files



| Target     | Code                                                         |
| ---------- | ------------------------------------------------------------ |
| 全文件读写 | byte[] bytes = Files.readAllBytes(path); <br/>List<String> lines = Files.readAllLines(path);<br/>**Stream<String> lines = Files.lines(path);** |
| char读写   | Files.newBufferedReader()                                    |
| byte读写   | Files.newInputStream()                                       |
| 文件copy   | Files.copy(source, destination)                              |
| 文件删除   | Files.deleteIfExists(path)                                   |
| 目录遍历   | Files.walkFileTree()                                         |



---

#### 0x04 ByteBuffer

ByteBuffer的定位有些**类似于C#中的MemoryStream（但却是定长的）**：

1. 缓存：在IO操作中用作数据中转站，用于提高IO性能
2. Buffer下有8种buffer类，即ByteBuffer, MappedByteBuffer, CharBuffer, ShortBuffer, IntBuffer, LongBuffer, FloatBuffer, DoubleBuffer。基本数据类型中，除布尔类型外都有对应的缓冲区实现。
3. ByteBuffer本身是一个abstract类，内含工厂方法allocate(int)与allocateDirect(int)用于生成对象；
4. ByteBuffer是定长的，也就是capacity不会像MemoryStream那长自动增长；



所有缓冲区都有4个属性：capacity、limit、position、mark，并遵循：mark <= position <= limit <= capacity，下表格是对着4个属性的解释：

| 属性     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| Capacity | 容量，即可以容纳的最大数据量；在缓冲区创建时被设定并且不能改变 |
| Limit    | 表示缓冲区的当前终点，不能对缓冲区超过极限的位置进行读写操作。且极限是可以修改的 |
| Position | 位置，下一个要被读或写的元素的索引，每次读写缓冲区数据时都会改变改值，为下次读写作准备 |
| Mark     | 标记，调用mark()来设置mark=position，再调用reset()可以让position恢复到标记的位置 |



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

#### 0x05 Channel

早期java使用stream抽象io操作，java1.4之后引入nio，然后开始使用channel抽象io操作。channel所抽象的层次并不是io操作所处理的数据，而是一个**已经建立好的支持io操作的实体的连接**，也就是说channl一定对应着一个实体连接，常见的是file与net连接。

channel的操作都是基于ByteBuffer缓冲对象的，而不是像Stream一样基于byte[] buffer。



###### FileChannel

可能是**java中速度最快的文件读写方案**。

1. FileChannel在多线程下是安全的
2. read(dst, position) /write(src, position) 随机读写文件
3. `int write(src)`操作**不能保证将所有的数据写出**，它的返回值代表着写出的字节数。注意，这与OutputStream的`void write(byte[])`方法不一样，后者是没有返回值的。
4. FileChannel.open(path, StandardOpenOption.READ) 打开一个只读的文件
5. transferTo() 可能会被操作系统优化成一个非常快速的直接传输



```java

// copy 1.4GB的文件需要1.7s
public static long copyFile(String srcPath, String dstPath) throws IOException {

        try (FileChannel fci = FileChannel.open(Paths.get(srcPath), StandardOpenOption.READ);
             FileChannel fco = FileChannel.open(Paths.get(dstPath), StandardOpenOption.CREATE, StandardOpenOption.WRITE)
        ) {
            return fci.transferTo(0, fci.size(), fco);
//            long count = 0;
//            ByteBuffer buffer = ByteBuffer.allocate(4096);
//            while (fci.read(buffer) != -1 || buffer.position() != 0) {
//                buffer.flip();
//                count += fco.write(buffer);
//                buffer.compact();
//            }
//
//            return count;
        }
    }

// copy 1.4GB的文件需要3.0s
public static long copyFileByStream (String srcFile, String destFile) throws IOException {

        try (FileInputStream fis = new FileInputStream(srcFile);
             FileOutputStream fos = new FileOutputStream(destFile);
        ) {
            long count = 0;
            byte[] buffer = new byte[4096];

            for (int size = 0; (size = fis.read(buffer)) != -1; ) {
                fos.write(buffer, 0, size);
                count += size;
            }

            return count;
        }
}

// copy 1.4GB的文件需要3.6s
public static void copyFileByRandomAccessFile(String srcFile, String destFile) throws IOException {

        try (RandomAccessFile fis = new RandomAccessFile(srcFile, "r");
             RandomAccessFile fos = new RandomAccessFile(destFile, "rw");
             FileChannel fci = fis.getChannel();
             FileChannel fco = fos.getChannel();
        ) {

            long size = fci.size();
            MappedByteBuffer inputBuffer = fci.map(FileChannel.MapMode.READ_ONLY, 0, size);
            MappedByteBuffer outputBuffer = fco.map(FileChannel.MapMode.READ_WRITE, 0, size); // 因为这里使用的是MapMode.READ_WRITE，所以前面必须使用RandomAccessFile，而不能使用FileOutputStream

            for (int i = 0; i < size; i++) {
                byte b = inputBuffer.get(i);
                outputBuffer.put(i, b);
            }
        }
    }
```



---



#### 0x06 RandomAccessFile (deprecated)

1. 该类实现了DataInput与DataOutput接口，因此可以操作各种primitive数据类型；

2. 该类提供随机访问文件的能力，这是各种InputStream和OutputStream所不具备的；

3. 该类并没有考虑buffer机制，因此频繁小数据量IO时性能会很差，此时应该换用MappedByteBuffer或BufferedXXX系列类读写文件

   

---

#### References

1. [IO流学习总结](https://github.com/Snailclimb/Java-Guide/blob/master/Java%E7%9B%B8%E5%85%B3/Java%20IO%E4%B8%8ENIO.md)
2. [ByteBuffer常用方法详解](https://blog.csdn.net/u012345283/article/details/38357851)
3. [Java NIO学习笔记之二-图解ByteBuffer](https://my.oschina.net/flashsword/blog/159613) 
4. [Memory Stream in Java](https://stackoverflow.com/questions/8436688/memory-stream-in-java)