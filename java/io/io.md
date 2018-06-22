

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
5. [SocketChannel](http://wiki.jikexueyuan.com/project/java-nio/socketchannel.html)