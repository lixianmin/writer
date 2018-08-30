



----

#### basics

1. lateinit，在kotlin中，一个非可选值必须在编译时赋初始值，**否则编译器会报错**，而lateinit就是告诉编译器说：虽然我没有赋初值，但我不是忘记了，等会儿再说，我会记得的。
2. 



----



```kotlin
val p : String by lazy {
    // 延迟属性
}

// 扩展函数
fun String.spaceToCamelCase() {
    ....
}

// if not null 缩写
val files = File("test").listFiles()
println(files?.size)
println(files?.size ?: "empty")

// if null执行一个语句
val values = ....
val email = values["email"] ? ("email is missing")

// 在可能会空的集合中取第一个元素
val mainEmail = emails.firstOrNull() ?: ""

// if not null执行代码
val value = ...
value?.let {
    // 如果value不为null则执行
}

// 返回when表达式
fun transform (color :String) : Int {
    return when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color")
    }
}

// try catch表达式
fun test() {
    val result = try{
        count()
    }catch (e: ArithmeticException) {
        throw IllegalStateException(e)
    }
    
    // 使用result
}

// if表达式
fun foo(param : Int) {
    val result = if (param == 1) {
        "one"
    }else if (param == 2) {
        "two"
    }else {
        "three"
    }
}

// 返回类型为Unit的方法的Builder风格用法
fun arrayOfMinusOnes (size : Int) IntArray {
    return IntArray(size).apply { fill(-1)}
}

// 单表达式函数
fun theAnswer () = 42

fun transform (color: String) : Int =  when (color) {
        "Red" -> 0
        "Green" -> 1
        "Blue" -> 2
        else -> throw IllegalArgumentException("Invalid color")
 }

// 对一个对象实例调用多个方法
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degree: Double)
    fun foward(pixels: Double)
}

val myTurtle = Turtle()
with (myTurtle) {
    penDown()
    for (i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}

// Java7的try with resources
val stream = Files.newInputStream()
stream.buffered().reader().use{
    reader -> println(reader.readText())
}

// 使用可空Boolean
val b: Boolean? = ...
if (b == true) {
    
}else {
    // b是false或null
}

// 类型别名
typealias MouseClickHandler = (Any, MouseEvent) -> Unit
typealias PersionIndex = Map<String, Persion>

// 命名参数
drawSquare(x= 10, y=10, wi=true)


```





##### 使用区间

```kotlin
for (i in 1..100) 		// [1, 100]
for (i in 1 until 100)	// [1, 100)
for (x in 2..10 step 2)	//
for (x in 10 downTo 1)	// 
```



-----

#### 作用域函数

1. 这里面功能最强的是let，它使用it代表当前迭代对象，并自定义返回值

2. let和run都是自定义返回值，简称**let it run**，区别是run应用在this对象上，而let应用到it对象上

3. apply, also，它们都以**a开头，都返回this**，都是对一个对象做一件事情

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











