

----

#### basics

1. `len(s)` 返回字节数组长度，cap()不接受string类型参数
2. **string的默认值是""，而不是nil**，也就是说你无法创建一个值为nil的string，反过来讲，这说明**string是一种结构类型，而不是引用类型**；
3. 支持 !=, ==, <, >, +, +=操作符
4. s[0]返回byte而不是rune
5. s[start:end]返回子串，其内部依旧指向原字节数组
6. 在**作为map[string]的key和for range迭代**时，编译器会优化以避免复制操作
7. bytes.Buffer相当于其它语言中StringBuffer
8. 



```go
strings.HasPrefix(s, prefix)
strings.HasSuffix(s, suffix)

strings.Join(slice, sep)
strings.Replace(s, old, new, -1)
strings.Split(s, sep)
strings.ToLower(s)

strings.TrimLeft(s, cutset)
strings.TrimSpace(s)

// 格式化字符串
s := fmt.Sprintf("%d", 123)

// 利用[]byte和string头结构“部分相同”，以非安全的指针类型转换来实现类型“变更”，从而避免底层数组复制
func toString(bs[] byte) string {
    return *(*string)(unsafe.Pointer(&bs))
}
```



```go
s := "我是中国人"
// 打印byte
for i := 0; i < len(s); i++ {
    fmt.Printf("byte %d:[%d]\n", i, s[i])
}

// 按Unicode字符打印
for i, c := range s {
    fmt.Printf("rune %d:[%c]\n", i, c)
}
// 注意：i仍然是s[i]的索引号，因此不是连续的
// rune 0:[我]
// rune 3:[是]
// rune 6:[中]
// rune 9:[国]
// rune 12:[人]
```



----

#### utf8

```go
utf8.ValidString(s) 		// 测试s是否是合法的utf-8文本
utf8.RuneCountInString(s)	// 返回准确的Unicode字符数量
```



#### string与int互转

```go
if num, err := strconv.ParseInt(s, 10, 0); err == nil {
    fmt.Printf("%T, %v\n", num, num)
}

s16 := strconv.FormatInt(num, 16)
fmt.Printf("%T, %s\n", s16, s16)
```

