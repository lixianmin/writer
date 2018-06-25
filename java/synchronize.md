

---

#### Introduction

1. **lock必须在 finally 块中释放**，否则，如果受保护的代码将抛出异常，锁就有可能永远得不到释放
2. ReentrantLock 类模拟了synchronized的语义：果线程进入由线程已经拥有的监控器保护的 synchronized 块，就允许线程继续进行，当线程退出第二个（或者后续） synchronized 块的时候，不释放锁，只有线程退出它进入的监控器保护的第一个 synchronized 块时，才释放锁。



---

#### BlockingQueue

|                       | Add         | Remove       | Examine       | 描述                                                         |
| --------------------- | ----------- | ------------ | ------------- | ------------------------------------------------------------ |
| Throws **e**xceptions | add()       | remov**e**() | **e**lement() | e is **e**xception, e is **e**lement()                       |
| Special **o**bject    | **o**ffer() | **po**ll()   | **p**eek()    | o is special **o**bject, o is **o**ffer, o is p**o**ll, p is **p**oll, p is **p**eek |
| Blocks                | pu**t**()   | **t**ake()   |               | t is **t**ons, blocks everything.                            |
| Time**o**ut           | **o**ffer() | p**o**ll     |               | o is time**o**ut, o is **o**ffer, o is p**o**ll              |



---

#### wait & notify

根类 Object 包含某些特殊的方法，用来在线程的 wait() 、 notify() 和 notifyAll() 之间进行通信。这些是高级的并发性特性，许多开发人员从来没有用过它们 —— 这可能是件好事，因为它们相当微妙，很容易使用不当。幸运的是，随着 JDK 5.0 中引入 java.util.concurrent ，开发人员几乎更加没有什么地方需要使用这些方法了。



1. 调用obj的wait(), notify, notifyAll()方法前，必须获得obj锁，因此这三个方法只能在synchronized(obj){...} 代码段内调用。
2. 反过来讲，在synchronized(obj){...} 代码段执行时，一定正在持有obj锁。
3. 调用obj.wait()后，线程A就释放了obj的锁，否则线程B无法获得obj锁，也就无法在synchronized(obj){...} 代码段内唤醒A。
4. 当obj.wait()方法返回后，线程A需要再次获得obj锁，才能继续执行。  
5. 如果A1,A2,A3都在obj.wait()，则B调用obj.notify()只能唤醒A1,A2,A3中的一个，这与线程优先级无关，具体哪一个由JVM决定。  
6. obj.notifyAll()会全部唤醒A1,A2,A3，但是要继续执行obj.wait()的下一条语句，必须获得obj锁，因此A1,A2,A3只有一个有机会获得锁继续执行，例如A1，其余的需要等待A1释放obj锁之后才能继续执行。  
7. 当B调用obj.notify()/notifyAll()的时候，A1, A2, A3会被立即唤醒，但在B退出synchronized(obj){...} 代码块之前并不会让出obj锁，因此A1, A2, A3虽被唤醒，但并不会立即获得obj锁。

-----
#### References
1. [Java多线程之wait(),notify(),notifyAll()](https://blog.csdn.net/oracle_microsoft/article/details/6863662)
2. [JDK 5.0 中更灵活、更具可伸缩性的锁定机制](https://www.ibm.com/developerworks/cn/java/j-jtp10264/index.html)

