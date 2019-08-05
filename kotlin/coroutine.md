

---



| Dispatchers            | 描述                                             | 场景      |
| ---------------------- | ------------------------------------------------ | --------- |
| Dispatchers.Default    | launch和async的默认dispatcher，线程数= 2~CPU核数 | CPU密集型 |
| Dispatchers.Main       | 单线程dispatcher                                 | UI专用    |
| Dispatchers.IO         | 最小64线程，与Dispatchers.Default共用线程池      | IO密集型  |
| Dispatchers.Unconfined | 协程resume时会使用另一个线程                     | 慎用      |



| 构造器                                                       | 描述                                         | 场景       |
| ------------------------------------------------------------ | -------------------------------------------- | ---------- |
| async                                                        |                                              |            |
| [coroutineScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/coroutine-scope.html) | 创建新的CoroutineScope，全部完成或取消后返回 |            |
| launch                                                       | 不阻塞，返回job                              |            |
| runBlocking                                                  | 阻塞当前线程，直到协程执行完毕               | 测试、Main |
| withContext                                                  | 协程运行过程中切换context，不创建新协程      |            |



```kotlin
// 不阻塞，返回deferred
val result = GlobalScope.async {}
result.await()

// 不阻塞，返回job
val job = GlobalScope.launch(Dispatchers.IO) 
job.cancel()	// 普通方法，可以在任何地方调用
job.join()		// 可中断方法，需在协程内调用




```





----



##### 1. 延时调用

```kotlin
val time = measureTimeMillis {
  // 加入LAZY参数，则在await()那一刻才启动
  val one = async(start = CoroutineStart.LAZY) { doSomethingUsefulOne() }
  val two = async(start = CoroutineStart.LAZY) { doSomethingUsefulTwo() }
  println("The answer is ${one.await() + two.await()}")
}
println("Completed in $time ms")
```



##### 2. 在finally里调用suspending function

```kotlin
val job = launch {
  try {
    repeat(1000) { i ->
                  println("I'm sleeping $i ...")
                  delay(500L)
                 }
  } finally {
    // 这里将context切换为NonCancellable，否则调用delay会抛出CancellationException
    withContext(NonCancellable) {
      println("I'm running finally")
      delay(1000L)
      println("And I've just delayed for 1 sec because I'm non-cancellable")
    }
  }
}
delay(1300L) // delay a bit
println("main: I'm tired of waiting!")
job.cancelAndJoin() // cancels the job and waits for its completion
println("main: Now I can quit.")
```



##### 超时取消

```kotlin
val result = withTimeoutOrNull(1300L) {
  repeat(1000) { i ->
                println("I'm sleeping $i ...")
                delay(500L)
               }
  "Done" // will get cancelled before it produces this result
}
println("Result is $result")
```







----

#### 0x09 References

1. [Kotlin协程笔记](https://www.jianshu.com/p/8dc8abca50e3)
2. 