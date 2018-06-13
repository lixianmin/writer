

---
#### 按行读取
```
for line in open('file.txt'): #默认打开方式为'r'，即只读
    print(line, end='') #注意end=''，字串line本身包括了换行符
```

---
#### 随机读取
```
with open('file.txt') as fin:
    print(fin.readline()) #读一行，这包括最后的换行符

fin.seek(0)         #文件指针重置
print(fin.read())   #读取文件全文
```

---
#### 写文件
```
with open('file.txt', 'w') as fout:
    fout.write(s)       # 写一行，不带换行符
    print(s, file=fout) #写一行，带换行符
```

---
#### 文件编码问题

只有**文件才会涉及到编码，所以称为文件编码**：

1. 从文件或io设备交换数据使用的是str，只有**str才会有文件编码**；

2. 从stdin读入是str类型，但是print()的时候可以使用str或unicode；

3. 从sqlite3执行select读出的是unicode；

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