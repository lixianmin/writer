

----

#### 创建slice或map



```go
func printSlice(slice []int) {
	fmt.Printf("len=%d, cap=%d, data=%v\n", len(slice), cap(slice), slice)
}

func main() {
	// 创建长度为0的slice, 后面的{}是放初始值的地方
	a := []int{}
	a = []int{0, 1, 2, 3}
	printSlice(a)

	// 定义一个len()==4, cap()==8的slice
	b := make([]int, 4, 8)
	b = append(b, 1, 2, 3, 4, 5)
	printSlice(b)

	// 如果不指定cap，则cap() == len()
	c := make([]int, 5)
	printSlice(c)

    定义一个长度为0的map, 后面的{}是用于存放初始值的地方
    dict := map[string]int{}
    dict = map[string]int{"hello": 1, "world": 2}
    // 直接创建一个map
    dict = make(map[string]int)
}
```



----

#### append

```go
// 将一个slice展开并附加到另一个slice尾部
a := []int{0, 1, 2, 3}
b := []int{4, 5, 6}
a = append(a, b...)
fmt.Printf("%v\n", a)

// 即使是nil的切片可以被追加
var empty []int
b = append(empty, 1, 2, 3, 4, 5)
```



----

1. 切片操作并不复制切片指向的元素，它创建一个新的切片并复用原来切片的底层数组



```go

	fmt.Printf("------------type=%T, len(slice)=%v\n", slice, len(slice))
	for i, v := range slice {
		fmt.Printf("%d = %d\n", i, v)
		dict[strconv.Itoa(v)] = v
	}

	fmt.Printf("------------type=%T, len(dict)=%v\n", dict, len(dict))
	key := "hello"
	dict[key] = 2
	delete(dict, key)

	// 从map 中读取某个不存在的键时，结果是 map 的元素类型的零值
	fmt.Printf("value of key : %v\n", dict[key])
	if v, ok := dict[key]; ok {
	}

	for k, v := range dict {
	}

```



----

#### References

1. [Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)