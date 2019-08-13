

---
#### 使用方法

```ruby
# 匹配默认是greedy的
# m是MatchData对象
text = '<a>hey<a>'
if m = /<.+>/.match(text)
    puts m[0]                               #=><a>hey<a>
end
# 注意m的作用域
puts m

# 通过加入一个?来开启lazy匹配模式，这一点与python相同
# String跟Regexp对象都拥有match方法
if m = text.match(/<.+?>/)
    puts m[0]                               #=><a>
end

# 遍历所有匹配到的match，如果其中含有group，则match是一个数组，否则是字符串
text.scan(/\w+/){ |match| puts match }      #=>a
                                            #=>hey
                                            #=>a

# $~代表由最近一次=~匹配到的信息，类型是MatchData
# $1代表第一个匹配组的内容，类型是String
# $~与$1都是特殊的全局变量，作用域是当前方法
if text =~ /(\w+)<a>/
    puts $~                                 #=>hey<a>
    puts $1                                 #=>hey，$1类型是String
end
```


​        

---
#### 基本元素

元素    | 释义
---     |---
/./     | Any character except a newline.
/./m    | Any character (the m modifier enables multiline mode)
/\w/    | A word character ([a-zA-Z0-9_])
/\W/    | A non-word character ([^a-zA-Z0-9_]). Please take a look at Bug #4044 if using /\W/ with the /i modifier.
/\d/    | A digit character ([0-9])
/\D/    | A non-digit character ([^0-9])
/\h/    | A hexdigit character ([0-9a-fA-F])
/\H/    | A non-hexdigit character ([^0-9a-fA-F])
/\s/    | A whitespace character: /[ \t\r\n\f\v]/
/\S/    | A non-whitespace character: /[^ \t\r\n\f\v]/