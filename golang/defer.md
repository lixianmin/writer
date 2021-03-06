

----

#### Basics

1. defer在包装函数return之后执行，常用于资源清理类工作
2. recover只有在defer的方法里才有意义，原因是recover用于恢复panic，只有当程序panic了并且已经返回的过程中才有recover的必要
3. 跟普通的函数相比，加了defer的函数需要消耗4~5倍的CPU时间



```go
// 单个同步
exit := make(chan struct{})
defer close(exit)

// 多个同步
var wg sync.WaitGroup
defer wg.Done()
```





---

#### defer使用原则

1. defer中的函数参数在调用到的那一刻确定 （defer是closure）

```go
// 输出0
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```

2. defer栈：LIFO （defer是closure）

```go
// 输出3、2、1、0
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
```

3. defer函数的执行发生在return之后，因此defer函数可以修改**命名的函数返回值**

```go
// 最终返回 2
func c() (i int) {
    defer func() { i++ }()
    return 1
}
```



----

#### References

1. [Defer, Panic, and Recover](https://blog.golang.org/defer-panic-and-recover)