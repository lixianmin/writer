



----

#### golang的Happens Before先行发生原则



##### 从一个unbuffered channel中receive操作先行发生于send操作结束

以下代码确保输出"hello, world"。写a先行发生于receive on c，而receive on c先行发生于send on c结束，而send on c结束先行发生于print(a)

```go
var c = make(chan int)
var a string

func f() {
	a = "hello, world"
	<-c
}
func main() {
	go f()
	c <- 0
	print(a)
}
```



---

#### java的Happens Before原则

这些原则可以辅助规划Java内存模型中的有序性，Happens before是指前一个操作的结果对后续操作是可见的。Happens Before规则本身没啥，但是结合顺序性与可传递性之后，就会导致这样的一个有趣的结果：**操作本身会变成一个分界线，分界线之前所有的操作的结果都会被分界线之后代码看到**。例如：

```java
class VolatileExample {
  int x = 0;
  volatile boolean v = false;
  public void writer() {
    x = 42;
    v = true;
  }
  public void reader() {
    // v作为volatile变量，将会作为分界线，writer()中设置x=42发生在v=true之前，因此在reader()发现v==true之后，x的值一定是42
    if (v == true) {
      // 这里 x 会是多少呢？
    }
  }
}
```



java的先行发生原则基本上包括**三原语、三线程**：

| name               | description                                                  |      |
| ------------------ | ------------------------------------------------------------ | ---- |
| volatile           | 对一个 volatile变量的写操作先行发生于后面对这个变量的读操作  |      |
| synchronized       | 一个unlock操作先行发生于后面对同一个锁的lock操作             |      |
| final              | 被final修饰的字段一旦在构造器中初始化完成，并且构造器没有把this的引用传递出去（this引用逃逸是一件很危险的事情，其它线程有可能通过this访问到“初始化了一半”的对象），那在其它线程中就能看到final字段的值 |      |
| thread.start()     | Thread对象的start()方法先行发生于此线程上的每一个动作        |      |
| thread.join()      | 线程中的所有操作都先行发生于对此线程的终止检测，我们可以通过thread.join()方法结束、thread.isAlive()的返回值等手段检测到线程已经终止执行。 |      |
| thread.interrupt() | 对线程的interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过thread.interrupted()方法检测到是否有中断事件发生。 |      |
| 构造函数           | 一个对象的初始化完成（构造函数执行结束）先行发生于它的finalize()方法开始。 |      |



-----

#### References

1. [The Go Memory Model](https://golang.org/ref/mem)