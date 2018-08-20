----

#### 将对象赋值给接口变量时，会复制该对象

```go
type Data struct {
	x int
}

func main () {
    var data = Data{1}
    // 使用对象赋值，则接口最后操作的将是一个全新的对象副本
	var holdNewData interface{} = data
    // 使用指针赋值，则接口最后操作的将是同一个对象
	var holdPointer interface{} = &data
	data.x = 2

    // 输出：data.x=2, holdNewData.x=1, holdPointer.x=2
	fmt.Printf("data.x=%d, holdNewData.x=%d, holdPointer.x=%d\n",
		data.x, holdNewData.(Data).x, holdPointer.(*Data).x)
}
```



----

#### 对接口赋值为nil时小心类型信息

接口变量的结构定义为

```go
type iface struct {
    tab *itab 			// 类型信息
    data unsafe.Pointer	// 实际对象指针
}
```

只有当接口变量内部的**两个指针(itab, data)都为nil时，接口才等于nil**。这很容易导致一些错误，尤其是在函数返回error时：

```go
type TestError struct{}

func (*TestError) Error() string {
	return "test error"
}

func test() error {
	var testError *TestError
	var err error = testError
    
    // 输出：testError==nil? [true], err==nil? [false]
	fmt.Printf("testError==nil? [%t], err==nil? [%t]\n", testError == nil, err == nil)
    // 注意，testError是有类型的
	return testError
}

func main () {
    // err的类型信息(tab)不为nil，因此对err的判空会失效
    err := test()
    if err == nil {
        return
    }
    
    // 类型推断也可以使用ok-idiom
    if testError, ok := err.(*TestError); ok {
        
    }
}
```











