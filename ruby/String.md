
http://ruby-doc.org/core-2.4.0/String.html

---
### 定义

类型 | 定义
---|---
单引号 | 不可修改，'\t'是\t
双引号 | 可修改，“\t”是制表符

---
#### 格式化

```ruby
puts "%20s %-10.3f"%['hello', 1234.34345]
puts format("%20s %-10.3f", 'hello', 1234.34345)

# %7.3f --> 小数点前是整个数字的最小宽度、小数点后的是小数宽度
puts format("%7.3f", 12.3456)       #=>  12.346  舍入3位小数
puts format("%7.2f", 12.3456)       #=>   12.35  舍入2位小数
puts format("%7.1f", 12.3456)       #=>    12.3  舍入1位小数
puts format("%0.1f", 12.3456)       #=>12.3      舍入1位小数，无填充
puts format("%0.2f", 12.3456)       #=>12.35     舍入2位小数，夫填充
```


​    
---
### 方法

方法        | 描述
---         |---
include? | 测试字符串包含关系 
start_with? | 谓词查询，以...开始
strip       | 不带!，简单的操作默认需要是安全的，因此直接原返回一个字符串的copy
upcase!     | 带!，复杂的操作会修改原始字符串
split(/\s/) | 以\s拆分字符串

---
### strip, strip!, chomp, chomp!, lstrip, lstrip!, rstrip, rstrip!

方法        | 描述
---         |---
strip  -> new_str       | 返回移除whitespace之后的新字符串
strip! -> self or nil   | 从str中移除whitespace，如果有修改，则返回self，否则返回nil
chomp(separator=$/) -> new_str      | 只删除str末尾的\r\n；rstrip会同时移除\r\n的和其它whitespace
chomp!(separator=$/) -> str or nil  |
lstrip -> new_str       | 
lstrip!-> self or nil   |
rstrip -> new_str       |
rstrip!-> self or nil   |


```ruby
"    hello    ".strip   #=> "hello"
"\tgoodbye\r\n".strip   #=> "goodbye"
"\x00\t\n\v\f\r ".strip #=> ""
```


```ruby
"hello".chomp                #=> "hello"
"hello\n".chomp              #=> "hello"
"hello\r\n".chomp            #=> "hello"
"hello\n\r".chomp            #=> "hello\n"
"hello\r".chomp              #=> "hello"
"hello \n there".chomp       #=> "hello \n there"
"hello".chomp("llo")         #=> "he"
"hello\r\n\r\n".chomp('')    #=> "hello"
"hello\r\n\r\r\n".chomp('')  #=> "hello\r\n\r"
```

---
### sub, sub!, gsub, gsub!

方法        | 描述
---         |---
sub         | 字符串替换，只替换找到的第一个位置
gsub        | 替换全部匹配的位置


```ruby
"hello".sub(/[aeiou]/, '*')                  #=> "h*llo"
"hello".sub(/([aeiou])/, '<\1>')             #=> "h<e>llo"
"hello".sub(/./) {|s| s.ord.to_s + ' ' }     #=> "104 ello"
"hello".sub(/(?<foo>[aeiou])/, '*\k<foo>*')  #=> "h*e*llo"
'Is SHELL your preferred shell?'.sub(/[[:upper:]]{2,}/, ENV)
 #=> "Is /bin/bash your preferred shell?"
```

