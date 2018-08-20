

---

#### basics

1. goroutine的自定义栈初始仅2KB，但最大可能到GB规模
2. 与defer一样， goroutine也会因"延迟执行"而立即计算并复制执行参数



各种并发方案的优势如下：

1. 多进程：分布式负载均衡，减轻单进程垃圾回收压力
2. 多线程(LWP)：抢夺更多的CPU资源
3. 协程：提高CPU时间片利用率



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

