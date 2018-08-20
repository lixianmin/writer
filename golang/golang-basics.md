



----

#### basics

1. ":="是一种**受限环境**下对var的省略写法，因而只能在**定义新变量时使用**，因而只能在函数内使用，要特别小心在嵌套作用域下，**将"="写作":="可能会将赋值误写成重定义一个新变量**

2. type是为命名而存在的，可以是：

   ```go
   type MyFloat float64
   type Vertex struct { X, Y float64 }
   type Disposable interface { Dispose() }
   ```

3. [reflect.TypeOf](https://golang.org/pkg/reflect/#TypeOf)((*os.File)(nil))返回变量类型



----

#### shell

| Command              | Description                                     |
| -------------------- | ----------------------------------------------- |
| godoc -h             |                                                 |
| godoc -http=:6060    | 在6060端口起doc服务                             |
| godoc package [Func] | 查询package中的Func的帮助文档                   |
|                      |                                                 |
| gofmt -h             |                                                 |
| go fmt               | `go fmt -l -w` 格式化当前目录下所有的文件并写回 |
| go run main.go       | 直接编译运行main.go                             |
| go test              | 运行单元测试                                    |



---

#### if for switch

1. for, if, switch都支持在主条件前加一个简单的前置语句
2. for, switch的主条件都可以是空的
3. switch支持default，但不需要break



------

#### 引用类型

1. golang中引用类型特指slice, map, chan这三种类型，这意味着其它类型，包括array, struct, interface等都是结构体类型，在传递是完整的copy，而不是只传递引用
2. 引用类型需要使用make进行初始化，**编译器**会将make转换成目标类型专用的创建函数，因此不存在运行时性能开销



----

#### string与int互转

```go
if num, err := strconv.ParseInt(s, 10, 0); err == nil {
    fmt.Printf("%T, %v\n", num, num)
}

s16 := strconv.FormatInt(num, 16)
fmt.Printf("%T, %s\n", s16, s16)
```



----

#### References

1. [Golang 新手可能会踩的 50 个坑](https://segmentfault.com/a/1190000013739000)
2. [Package strconv](https://golang.org/pkg/strconv/)