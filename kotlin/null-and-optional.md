

----



| 符号 | 示例                            | 描述                                                         |
| ---- | ------------------------------- | ------------------------------------------------------------ |
| ?.   | `files?.size`                   | 如果不为null则执行                                           |
| ?:   | `emails.first()?:"empty email"` | **Elvis运算符**：如果为null则返回后面那个，跟上面那个相比，由一个点号变成了两个点号 |
| !!   | `if (isRight!!) {}`             | 强制提取optional类型，如果是null则异常，可以**当断言使用**   |
| ::   | `Dog::name`                     | 用于提取各类引用，比如KClass引用，函数引用，属性引用，构造函数引用 |



```kotlin
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

// 使用可空Boolean
val b: Boolean? = ...
if (b == true) {
    
}else {
    // b是false或null
}

```

