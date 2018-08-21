

---

#### basics

1. goroutine的自定义栈初始仅2KB，但最大可能到GB规模
2. 与defer一样， goroutine也会因"延迟执行"而立即计算并复制执行参数



各种并发方案的优势如下：

1. 多进程：分布式负载均衡，减轻单进程垃圾回收压力
2. 多线程(LWP)：抢夺更多的CPU资源
3. 协程：提高CPU时间片利用率



---

#### 异步编程参考



1. 函数与方法是不一样的，方法含有状态，因此**首选纯函数**，哪怕参数多一点儿，命名丑一点儿。**信心很重要，虽然代替不了测试，但纯函数会让状态变简单，编码思路清晰很多**。
2. 关于goroutine中使用的变量：
   - **首选局部变量，这样可以确保该变量只在本goroutine中使用**，而不会其它goroutine无意识的修改
   - 次选参数传入，这样可以确保该变量不会被异步设置成nil。典型的应用就是**goroutine中用作receiver的channel对象必须以参数方式传入**，因为主线程会将该close(channel)并设置原始channel变量为nil
   - 最次使用类成员变量或closure的upvalue，这时需要**程序员确保这些变量的可用性**，例如不会被异步关闭或置nil
   - 除局部变量外，无论是传入参数还是类成员变量，因为可能存在异步访问，因此**要么这些变量自己是线程安全的，要么就需要额外同步**保障



----

#### 同步方案解析

进程退出并不会等待并发任务结束，可以通过channel阻塞，然后发出退出信号以同步

```go
exit := make(chan struct{})
go func() {
    time.Sleep(time.Second)
    close(exit)
}()

// 如果通道关闭，立即解除阻塞
<- exit
```



如果需要等待多个任务结束，就需要使用sync.WaitGroup

```go
var wg sync.WaitGroup

for i=0; i< 10; i++ {
    // wg.Add(1)这一句要放到go之前，以确保在goroutine启动之前就被设置过了。
    wg.Add(1)
    go func(wg *sync.WaitGroup) { // 传递wg对象的时候要传递指针，否则会被clone一份
        // wg.Add(1)  --> 不允许放到这里，否则可能来不及设置，后面的wg.Wait()就已经退出了
        defer wg.Done()
    }(&wg)
}

wg.Wait()
```



控制对同一个map在不同goroutine中的read/write可以使用RWMutex

```go
var lock sync.RWMutex
// write
lock.Lock()
lock.Unlock()

// read
lock.RLock()
lock.RUnlock()
```

