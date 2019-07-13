

---

#### basics

1. struct中**只定义像chan这种同步变量**，那么**只要不存在将其置nil的操作**，就应该是线程安全的。但对有可能被设置为nil的chan，当在goroutine中使用的时候一定要使用参数传递，否则存在异步访问冲突
2. goroutine的自定义栈初始仅2KB，但最大可能到GB规模
3. 与defer一样， goroutine也会因"延迟执行"而立即计算并复制执行参数
4. Mutex并**不支持递归锁**，即便在同一goroutine下也会导致死锁
5. 对性能要求较高时，应避免使用`defer Unlock`
6. 对单个数据读写保护，可尝试使用原子操作，如`atomic.LoadInt32(&i)`
7. 执行严格测试，尽可能打开数据竞争检测：`go run -race main.go`



各种并发方案的优势如下：

1. 多进程：分布式负载均衡，减轻单进程垃圾回收压力
2. 多线程(LWP)：抢夺更多的CPU资源
3. 协程：提高CPU时间片利用率



---

#### 异步编程参考



1. 函数与方法是不一样的，方法包含状态，而函数没有状态
   - 因此所有**goroutine都应该设计成纯函数，配合线程安全的参数，可以保证线程安全**
   - 普通函数，如果确定它们**只在同一个goroutine中调用**，则它们的参数是可以带参数的
2. 可以将某些**只会在goroutine内部使用的函数定义在goroutine内**，这样可以提供更强的安全性保证：
   - 内部函数使用的所有变量，要么是goroutine参数（要求线程安全），要么是goroutine的局部变量（线程安全）
   - 内部函数意味着绝对不会被外部代码调用到，因此**不需要担心误用**问题
   - 内部函数可以带一部分upvalue，因此函数定义会简单不少
3. 关于goroutine中使用的变量：
   - **首选局部变量，这样可以确保该变量只在本goroutine中使用**，而不会被其它goroutine意外修改
   - 次选参数传入，这样可以确保该变量不会被异步设置成nil。典型的应用就是**goroutine中用作receiver的channel对象必须以参数方式传入**，因为主线程会将该close(channel)并设置原始channel变量为nil
   - 最次使用类成员变量或closure的upvalue，这时需要**程序员确保这些变量的可用性**，例如不会被异步关闭或置nil
   - 除局部变量外，无论是传入参数还是类成员变量，因为可能存在异步访问，因此**要么这些变量自己是线程安全的，要么就需要额外同步**保障



----

#### sync.Once

1. atomic速度快，但只能管得了自己，对别的代码没有帮助。**各人自扫门前雪**
2. Mutex速度慢，但可以形成一个临界区，临界区中的代码也可以得到同步

```go
func (o *Once) Do(f func()) {
    // 1. 这里为什么使用atomic操作，如果换成直接判断o.done == 1 行不行？
    // 	> 这部分的代码不受mutex保护，因此如果想o.done的操作立即被其它协程看到的话，需要使用atomic操作
	if atomic.LoadUint32(&o.done) == 1 {
		return
	}
	// Slow-path.
	o.m.Lock()
	defer o.m.Unlock()
    // 代码访问到这里是由mutex保护的，是互斥的，因此o.done == 0可以直接使用，不必使用atomic操作
	if o.done == 0 {
		defer atomic.StoreUint32(&o.done, 1)
		f()
	}
}
```



----

#### 同步方案解析



##### close channel方案

1. 如果是一次性同步
2. 如果是1对1的通知
3. 支持多个goroutine同时等待channel关闭

```go
// 进程退出并不会等待并发任务结束，可以通过channel阻塞，然后发出退出信号以同步
exit := make(chan struct{})
go func() {
    time.Sleep(time.Second)
    close(exit)
}()

// 如果通道关闭，立即解除阻塞
<- exit
```



##### sync.WaitGroup方案

1. 如果是一次性的通知
2. 如果是N对1的情况
3. **Add()和Wait()必须在同一个 goroutine中**，否则可能panic

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



##### sync.RWMutex

1. 在**读取数据的频率远远大于写数据的频率**的场合可以考虑使用RWMutex，例如控制同一个map在不同goroutine中的read/write的时候
2. **组合锁**模式：可以考虑定义一个结构体并组合一个sync.RWMutex，这样可以将sync.RWMutex与被加锁的数据紧密关联到一起，并且用起来很简洁，但是此时**注意使用结构体指针**，以防止无意中使用了结构体副本



```go
type User struct{
    m map[int]*User
    sync.RWMutex
}

var users = &User{m:make(map[int]*User, 8)}

func fetchUser (userID int) *User {
    users.RLock()
    var user, ok = users.m[userID]
    users.RUnlock()
    
    if !ok {
        users.Lock()
        users.m[userID] = newUser()
        users.Unlock()
    }
    
    return user
}
```



##### Semaphore方案

通常，我们使用sync.Mutex互斥量来做互斥访问，但当同时允许的并发数大于1的时候，就需要用到semaphore方案

```go
var parallelCount = 2
var sem = make(chan struct{}, parallelCount)

func test(){
    sem <- struct{}{}		// acquire获取信号
    defer func(){ <-sem }()	// release释放信息
    ......
}
```



---

#### References

1. [golang 核心开发者 Dmitry Vyukov(1.1 调度器作者) 关于性能剖析 ](https://my.oschina.net/evilunix/blog/371958)
2. [如何实现超高并发的无锁缓存？](https://www.jianshu.com/p/2ce951bf5951)
3. 











