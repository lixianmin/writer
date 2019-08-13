









```kotlin
// 写文件，生成一个bufferedWriter，并自动关闭之
File(filepath).bufferedWriter().use {
  it.write("hello")
}
```

