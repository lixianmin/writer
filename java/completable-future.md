

1. 实现了CompletionStage接口，thenApply()之类方法的注解，全可以在该接口中找到
2. 实现了Future接口，代表一个未完成的异步事件
3. `thenXXX`意味着这个阶段的动作发生当前的阶段正常完成之后



```java
// fn由原来的线程计算
public <U> CompletableFuture<U> 	thenApply(Function<? super T,? extends U> fn)
  
// fn由默认的线程池ForkJoinPool.commonPool()执行
public <U> CompletableFuture<U> 	thenApplyAsync(Function<? super T,? extends U> fn)

// fn由指定的executor执行
public <U> CompletableFuture<U> 	thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)
```



CompletableFuture提供了很多相似的方法，很多情况下似乎调用哪个都可以。可以参考方法的参数类型辅助记忆:

| 方法           | 参数                                                |      |
| -------------- | --------------------------------------------------- | ---- |
| thenRun        | Runnable会忽略计算的结果                            |      |
| thenAccept     | Consumer纯消费计算的结果                            |      |
| thenAcceptBoth | BiConsumer组合另外一个CompletionStage纯消费         |      |
| thenApply      | Function会对计算结果做转换                          |      |
| handle         | BiFunction组合另一个CompletionStage的计算结果并转换 |      |



----

#### References

1. [20个使用 Java CompletableFuture的例子](https://colobu.com/2018/03/12/20-Examples-of-Using-Java%E2%80%99s-CompletableFuture/)
2. [Java CompletableFuture 详解](https://colobu.com/2016/02/29/Java-CompletableFuture)