

---

#### basics



```kotlin

// lazy不保证线程安全，如果需要的话，传参数：LazyThreadSafetyMode.SYNCHRONIZED
private val singleton by lazy(LazyThreadSafetyMode.SYNCHRONIZED){}

// kotlin风格实例化并启动一个线程
thread(start=true) {
    println("running from thread():${Thread.currentThread()}")
}

// @Volatile注解
@Volatile private var running = false

// @Synchronized注解
@Synchronized fun synchronizedMethod() {
    println("inside a synchronized method:${Thread.currentThread()}")
}

// synchronize方法
fun methodWithSynchronizedBlock() {
    println("outside of a synchronized block:${Thread.currentThread()}")
    synchronized(this) {
         println("inside a synchronized block:${Thread.currentThread()}")
    }
}

```

 

----

#### 简单的val是线程安全的

You fail to make a clear distinction between a `val` with and without a custom getter. If you lump those two together, like in your question, then `val` is not thread-safe; however Kotlin does make this distinction, as you can observe in this example:

```kotlin
val simpleVal: Int? = 3
val customVal: Int? get() = simpleVal

fun main(args: Array<String>) {
    if (simpleVal != null) {
        println(simpleVal + 1)
    }

    if (customVal != null) {
        println(customVal + 1) // ERROR!
    }
}
```

> Error:(12, 21) Kotlin: Smart cast to 'Int' is impossible, because 'customVal' is a property that has open or custom getter

The smart cast is not allowed, among other reasons, due to the potential of another thread mutating the result of the custom `get()` call.

Therefore:

1. A simple `val` is thread-safe;
2. A `val` with a custom or open getter is not (necessarily) thread-safe.

---

#### references

1. [kotlin并发性](https://www.jianshu.com/p/3963e64e7fe7)
2. [Why there are no concurrency keywords in Kotlin?](https://stackoverflow.com/questions/35520583/why-there-are-no-concurrency-keywords-in-kotlin)
3. [Is a val property thread-safety?](https://stackoverflow.com/questions/50033339/is-a-val-property-thread-safety)

