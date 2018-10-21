

http://ruby-doc.org/core-2.3.3/Enumerable.html

----
#### ect方法 (rdisc)


name    | description
---     |---
reject  | 
detect  | find
inject  | reduce
select  | find_all
collect | map

----
#### each_with_index

```ruby
hash = Hash.new
%w(cat dog wombat).each_with_index { |item, index|
  hash[item] = index
}
hash   #=> {"cat"=>0, "dog"=>1, "wombat"=>2}
```

#### find, detect

```ruby
(1..10).detect   { |i| i % 5 == 0 and i % 7 == 0 }   #=> nil
(1..100).find    { |i| i % 5 == 0 and i % 7 == 0 }   #=> 35
```

#### grep

```ruby
(1..100).grep 38..44                        #=> [38, 39, 40, 41, 42, 43, 44]

c = IO.constants
c.grep(/SEEK/)                              #=> [:SEEK_SET, :SEEK_CUR, :SEEK_END]
c.grep(/SEEK/) { |v| IO.const_get(v) }      #=> [0, 1, 2]

['a', 1, 2, 'b'].grep(String, &:upcase)     #=> ["A", "B"]
```

#### map, collect

```ruby
(1..4).map { |i| i*i }      #=> [1, 4, 9, 16]
(1..4).collect { "cat"  }   #=> ["cat", "cat", "cat", "cat"]
```

#### reduce, inject

```ruby
# 把一个序列归约为一个元素
[1,2,3].reduce(:+)          #=> 6
(1..5).reduce(0.5, :*)      #=> 60.0，指定初始值

%w{ cat sheep bear }.reduce do |memo, word|
    memo.length > word.length ? memo : word
end                         #=> "sheep"，选出最长的那一个字符串
```

#### reject

```ruby
(1..10).reject { |i|  i % 3 == 0 }          #=> [1, 2, 4, 5, 7, 8, 10]

[1, 2, 3, 4, 5].reject { |num| num.even? }  #=> [1, 3, 5]
```

#### select, find_all

```ruby
(1..10).find_all { |i| i % 3 == 0 }         #=> [3, 6, 9]

[1,2,3,4,5].select { |num| num.even? }      #=> [2, 4]
```

#### sort

```ruby
# Enumerable.sort会首先调用to_a，然后调用array的sort
%w(rhea kea flea).sort                      #=> ["flea", "kea", "rhea"]
(1..10).sort { |a, b| b <=> a }             #=> [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

#### sort_by

```ruby
[1, "5", 2, "7", "3"].sort_by { |a| a.to_i }#=> [1, 2, "3", "5", "7"]
[1, "5", 2, "7", "3"].sort_by(&:to_i)       #=> [1, 2, "3", "5", "7"]
```