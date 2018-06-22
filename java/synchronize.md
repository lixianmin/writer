---

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

