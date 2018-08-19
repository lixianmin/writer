

---

#### basics

1. channel变量本身是指针，可用相等操作符判断是否为同一对象或nil
2. 内置函数cap和len返回缓冲区大小和当前已缓冲数量；如果是同步通道则都返回0，据此可以判断通道是同步还是异步。这里有一个问题：**len(fiber)并无法判断当前chan内是否有数据**。
3. 同步模式：发送和接收双方配对，然后直接复制数据给对方，如配对失败，则置入等待队列，直到另一方出现后才被唤醒
4. 及时用close关闭通道引发结束通知，否则可能会导致死锁
5. close()引发的通知可以是群体性的，也未必就是通知结束，可以是任何需要表达的事件
6. 关闭的通道：
   - 向已关闭的通道发送数据，引发panic
   - 从已关闭的通道接收数据：
     - 如果通道内还有数据，则返回已缓冲数据，此时ok idom返回true
     - 如果通道内没有数据，则返回0值，此时ok idom返回false
7. 向nil通道收发数据，都会引发阻塞
8. **左发右收，左关右等**：
   - 左发：`c <- data` 是sender
   - 右等：`data <- c` 是receiver
   - 左关：sender负责close channel
   - 右等：receiver需要等到channel关闭后自动退出
9. **goroutine中接收数据的fiber在一定要通过参数传递获取**，因此它在对象上的引用很快会被设置为nil



| Operation type | Nil channel | Closed channel                                 |
| -------------- | ----------- | ---------------------------------------------- |
| Send           | Block       | Panic                                          |
| Receive        | Block       | Not block, return zero value of channel's type |
| Close          | Panic       | Panic                                          |



```go
// ok idom
for {
    select{
        case x, ok := <- c:
            // 据此判断通道是否被关闭，通道关闭了才能退出goroutine
            if !ok {
                return
            }
        case <- timerC:
        	// 如果一个channel确认不用了，就要置nil，否则如果它是closed状态，有可能死循环
        	timerC = nil
    }
}

// range 模式
// 循环获取消息，直到channel被关闭，所以这个是不能用于select的
for x := range c {
    println(c)
}
```



----

#### References

1. [Nil Channels Always Block](https://www.godesignpatterns.com/2014/05/nil-channels-always-block.html)