

----

#### 0x01 java io体系

java IO流共涉及40多个类，看上去很杂乱，但实际上这此类都是从如下4个抽象类基类中派生出来的。

- **InputStream/OutputStream**: byte输入输出流的基类，比如BufferedInputStream, BufferedOutputStream;
- **Reader/Writer**: char输入输出流的基类，常用的如BufferedReader, BufferWriter;



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

#### References

1. [IO流学习总结](https://github.com/Snailclimb/Java-Guide/blob/master/Java%E7%9B%B8%E5%85%B3/Java%20IO%E4%B8%8ENIO.md)