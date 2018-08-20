



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

4. `debug.PrintStack()`打印完整的堆栈信息

5. array是值类型，`return array`会复制整个数组

6. break语句用于for, switch, select，而continue仅仅用于for循环

7. `data:=[...]int{1, 2, 3}` 按需初始值数量确定的数组



```go
// range会复制目标数据，受直接影响的是array，可改用数组指针或slice类型
data := [3]int{1, 2, 3}
for i, x := range data {    // 这里使用的是data的复制品
}

for i, x := range data[:] { // 仅复制slice，不包括底层的array
}
```





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

#### 如何选择方法的receiver类型

以下类型建议直接用T

1. 无需修改状态的小对象或固定值
2. 引用类型、string、函数指针包装对象



以下类型建议使用*T

1. 要修改实例状态
2. 大对象建议用*T，以减少复制成本
3. 若包含Mutext等同步字段，用*T，避免因复制造成锁操作无效
4. 其它无法确定的情况，都用*T



----

#### References

1. [Golang 新手可能会踩的 50 个坑](https://segmentfault.com/a/1190000013739000)
2. [Package strconv](https://golang.org/pkg/strconv/)