

----

#### basics

1. 切片操作并不复制切片指向的元素，它创建一个新的切片并复用原来切片的底层数组
2. 切片本身是一个只读对象，其工作机制类似数组指针的一种包装。因此它的定位应该是动态数组
3. copy(dst, “text”) **支持从string复制数据到byte[]**
4. 如果slice长期引用大array中的小片段，则建议新建独立slice并复制出数据，以使得原始array可以被回收



----

#### 创建slice

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
  // 注意：这个append()会从第5个位置开始增加数据
	b = append(b, 1, 2, 3, 4, 5)
	printSlice(b)

	// 如果不指定cap，则cap() == len()
	c := make([]int, 5)
	printSlice(c)
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

#### References

1. [Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)