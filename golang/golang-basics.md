



----

#### shell

| Command              | Description                   |
| -------------------- | ----------------------------- |
| godoc -h             |                               |
| godoc -http=:6060    | 在6060端口起doc服务           |
| godoc package [Func] | 查询package中的Func的帮助文档 |
|                      |                               |
| gofmt -h             |                               |
| go run main.go       | 直接编译运行main.go           |



---

#### if for switch

1. for, if, switch都支持在主条件前加一个简单的前置语句
2. for, switch的主条件都可以是空的
3. 只有i++, 没有++i
4. switch支持default，但不需要break



----

#### string与int互转

```go
if num, err := strconv.ParseInt(s, 10, 0); err == nil {
    fmt.Printf("%T, %v\n", num, num)
}

s16 := strconv.FormatInt(num, 16)
fmt.Printf("%T, %s\n", s16, s16)
```



1. [Package strconv](https://golang.org/pkg/strconv/)
2. 

----

#### Tips

1. ":="是一种**受限环境**下对var的省略写法，因而只能在**定义新变量时使用**，因而只能在**函数内**使用
2. type是为命名而存在的，可以是：
	``` go
	type MyFloat float64
	type Vertex struct { X, Y float64 }
	type Disposable interface { Dispose() }
	```
4. [reflect.TypeOf](https://golang.org/pkg/reflect/#TypeOf)((*os.File)(nil))返回变量类型



----

#### References

1. [Golang 新手可能会踩的 50 个坑](https://segmentfault.com/a/1190000013739000)