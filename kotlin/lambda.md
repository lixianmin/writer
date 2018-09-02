----
#### basics
1. lambda是表达式，而不是函数，因此它里面的**return会直接跳出外层函数**；
2. **单参数lambda表达式使用it作为默认参数**，比如let和also就是单参数lambda表达式
3. 



----

#### 作用域函数

1. 这里面功能最强的是let，它使用it代表当前迭代对象，并自定义返回值

2. let和run都是自定义返回值，简称**let it run**，区别是run应用在this对象上，而let应用到it对象上

3. let和also都只是单参数lambda表达式而已

4. apply, also，它们都以**a开头，都返回this**，都是对一个对象做一件事情

   - apply在this对象上做
   - also是在it对象上做



```kotlin
// T.()表示传入的是一个不带参数的方法，该方法属于类T，而返回值this就是T对象
// apply以a开头，因此返回this，适合于链式表达式
fun <T> T.apply(block: T.() -> Unit): T {
    block();
    return this
}

// also以a开头，因此返回this，适合于链式表达式
fun <T> T.also(block: (T) -> Unit): T {
    block(this);
    return this
}

// 跟apply很像，只不过返回f()的返回值，而不是this
fun <T, R> T.run(f: T.() -> R): R = f()

// let将当前对象默认作为it参数，注意区分一下it与this
fun <T, R> T.let(f: (T) -> R): R = f(this)

// with
fun <T, R> with(receiver: T, f: T.() -> R): R = receiver.f()


```

