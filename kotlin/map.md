

```kotlin

// 只读map
val map = mapOf("a" to 1, "b" to 2)
println(map["key"])
map["key"] = value

for ((k, v) in map) {
    println("$k -> $v")
}

for (entry in map) {
    println("$entry.key -> $entry.value")
}

val readWriteMap = hashMapOf("foo" to 1, "bar" to 2)
val snapshot : Map<String, Int> = Hashmap(readWriteMap)


```

