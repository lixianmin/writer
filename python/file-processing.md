

---
#### 0x01 按行读取
```
for line in open('file.txt'): #默认打开方式为'r'，即只读
    print(line, end='') #注意end=''，字串line本身包括了换行符
```

---
#### 0x02 随机读取
```
with open('file.txt') as fin:
    print(fin.readline()) #读一行，这包括最后的换行符

fin.seek(0)         #文件指针重置
print(fin.read())   #读取文件全文
```

---
#### 0x03 写文件
```
with open('file.txt', 'w') as fout:
    fout.write(s)       # 写一行，不带换行符
    print(s, file=fout) #写一行，带换行符
```

---
#### 0x04 文件编码问题

| unicode                                          | str（python2中称为str，python3中称为bytes） |
| ------------------------------------------------ | ------------------------------------------- |
| 对标物是内存中普通类对象，只不过它描述的是string | 对应序列化后的字节流，方便disk存储、net传输 |
| 描述单位是character，human readable              | 描述单位是byte, machine readable            |



A Unicode sandwich: bytes on the outside, Unicode on the inside.



1. **普通类对象存储到disk需要选择序列化方案，如xml, json等，而unicode对象的序列化方案就是utf-8**

2. PNG, JPEG, MP3, WAV, ASCII, UTF-8 etc are different forms of encodings

3. character是human readable，而str是machine readable

   - 从io设备交换数据使用的是str，就像我们使用字节流在网上传输数据一样；
   - 数据经过编码以byte的形式存储到文件，所以**才有文件编码**说法；

4. 从stdin读入是bytes类型；

5. stdout的output一定是bytes:

   - print()可以接受unicode参数，但会隐藏地执行了assii编码，因此有时候会出错；
   - 当有中文输出时，强烈**建议print()使用bytes参数，这样输出的中文才可以grep**；

6. 从sqlite3执行select读出的是unicode；

   ```python
   def print_text (text):
       if type(text) is str:
           print ('{}, {}'.format(type(text), text))
       else:
           print (u'{}, {}'.format(type(text), text))
   
   print_text('str字符串')
   print_text('str字符串解码为unicode对象'.decode('u8'))
   
   print_text(u'unicode对象')
   print_text(u'unicode对象编码为str'.encode('u8'))
   ```

   

python文件读写默认使用系统编码，即locale.getpreferredencoding()，在Windows下为cp936，linux下为utf8。因此在读写文件时最好明确指定文件编码：

```
with open('utf8.txt', 'w', encoding= 'utf8') as fout:
    fout.write('此文件使用utf8编码 \n')
    fout.write('结束 \n')
```

注意：
1. 当文件中仅包含ASSII字符时，Windows中记事本会将其识别为GB2312编码；
2. 脚本文件(*.py)本身需要是utf8格式，否则无法执行；



----

#### 0x05 References

1. [Byte Objects vs String in Python](https://www.geeksforgeeks.org/byte-objects-vs-string-python/)
2. [Pragmatic Unicode](https://nedbatchelder.com/text/unipain.html)
3. [Overcoming frustration: Correctly using unicode in python2](https://pythonhosted.org/kitchen/unicode-frustrations.html#overcoming-frustration-correctly-using-unicode-in-python2)