

---

#### basics

1. channel变量本身是指针，可用相等操作符判断是否为同一对象或nil
2. 内置函数cap和len返回缓冲区大小和当前已缓冲数量；如果是同步通道则都返回0，据此可以判断通道是同步还是异步。注意：**len(channel)并无法判断当前chan内是否有数据**。
3. 同步模式：配对成功时sender直接复制数据给receiver，否则发起配对的sender或receiver被阻塞，直到另一方出现后被唤醒
4. 及时用close关闭通道引发结束通知，否则可能会导致死锁
5. close(channel)引发的通知是群体性的，也未必就是通知结束，可以是任何需要表达的事件
6. **左发右收，左关右等**：
   - 左发：`c <- data` 是sender
   - 右等：`data <- c` 是receiver
   - 左关：**sender负责close channel**
   - 右等：receiver需要等到channel关闭后自动退出
7. receiver**等待的channel必须以函数参数的方式拿到**，因为它在对象上的引用可能在任意时刻被设置为nil
8. 对同一个对象的所有操作都要通过同一个channel进行，否则需要同步



| Operation type | Nil channel | Closed channel                                 |
| -------------- | ----------- | ---------------------------------------------- |
| Send           | Block       | Panic                                          |
| Receive        | Block       | Not block, return zero value of channel's type |
| Close          | Panic       | Panic                                          |



```go
// receiver准则：
// 1. receiver等待的channel必须以函数参数的方式拿到，因为它在对象上的引用可能在任意时刻被设置为nil
func (team *UserTeam) goProcessor(commandQueue chan IUserCommand) {
	for {
		select {
		// ok idiom，当commandQueue被sender关闭后，在receiver中等待所有数据接收完毕后自动退出
		case cmd, ok := <-commandQueue:
			if !ok {
				return
			}
            // do something
        case <- timerC:
        	// 当channel确认不用时，设置为nil，否则如果它是closed状态，有可能死循环
        	timerC = nil
        }
    }
}

// range 模式
// 循环获取消息，直到channel被关闭，所以这个是不能用在select内部，否则在没有数据时会block
for x := range c {
    println(c)
}
```



----

#### References

1. [Nil Channels Always Block](https://www.godesignpatterns.com/2014/05/nil-channels-always-block.html)