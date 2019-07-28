---
1. require 'open-uri'， 然后网络下载就可以用了

---
#### 基本操作

```ruby
# 路径操作
File.basename
File.dirname
File.join

# 文件管理
File.delete
File.rename(from, to)

# 属性测试
File.readable?
File.writable?
File.executable?

File.directory?
File.file?
File.exists?
```

---
#### 文件读取

```ruby
# 读取整个文件
text = open filepath, &:read

# 按行读取, fin is closed automatically.
# 读文件时，可以用外部编码(UTF-8)读取，然后转换成内部编码(GBK)
open(filepath, 'r:utf-8:gbk') do |fin|
    # each, each_line是别名关系
    # each, each_line, each_with_index返回的line都是带\n的，需要调用chomp移除
    fin.each_with_index do |line, index|
        # 当line以\n结尾时puts并不会自动加入转行符
        puts line
        puts line.encoding.name #=> "GBK"
    end
end

# 使用IO库
IO.foreach (filepath) do |block|
    puts block
end
```

---
#### 遍历文件夹

是否记得，我们在osx下面==也是用find==查找文件的

```ruby
require 'find'

Find.find('./') do |path|
  if File.directory? (path)
      Find.prune
  else
      next
  end
end
```

---
#### IO open mode
Mode |  Meaning
-----| --------------------------------------------------------
"r"  |  Read-only, starts at beginning of file  (default mode).
"r+" |  Read-write, starts at beginning of file.
"w"  |  Write-only, truncates existing file to zero length or creates a new file for writing.
"w+" |  Read-write, truncates existing file to zero length or creates a new file for reading and writing.
"a"  |  Write-only, starts at end of file if file exists, otherwise creates a new file for writing.
"a+" |  Read-write, starts at end of file if file exists, otherwise creates a new file for reading and writing.
"b"  |  Binary file mode (may appear with any of the key letters listed above). Suppresses EOL <-> CRLF conversion on Windows.
"t"  |  Text file mode (may appear with any of the key letters listed above except "b").

---
#### Dir目录操作

http://ruby-doc.org/core-2.3.1/Dir.html


```ruby
    # 打印当前目录的内容
    # d.each()是类成员函数，因此只能使用foreach了
    Dir.foreach('.') {|name| puts name}
    
    # glob方式获取目录
    Dir.glob("*.[a-z][a-z])   #=> [“main.rb”]
    Dir.glob("*.{rb, h}")     #=> [“main.rb”, “config.h"]
    
    # 当前目录
    puts Dir.pwd
    puts Dir.getwd
    
    # 获取临时目录
    require 'tmpdir'
    puts Dir.tmpdir
```




