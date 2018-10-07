



```kotlin
val list = listOf("a", "b", "c")			// 优先使用不可变的list
var arrayList = arrayListOf("a", "b", "c")	// 返回一个ArrayList<>

val items = listOf(1, 2, 3, 4)
items.first() == 1
items.last() == 4
items.filter{ it % 2 == 0}

// 遍历
for (item in array) {    
}

// 使用索引
for (i in array.indices) {
}

// 带索引
for ((index, value) in array.withIndex()) {
}

```

