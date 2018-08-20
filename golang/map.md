

------

#### map

1. 访问不存在的key，默认返回0值，不会引发错误，但推荐使用ok idiom模式
2. 在迭代期间删除或新增键值是安全的
3. 在不同的goroutine中对同一个map分别read/write是不安全的，需要进行同步



```go
	// 定义一个长度为0的map, 后面的{}是用于存放初始值的地方
    dict := map[string]int{}
    dict = map[string]int{"hello": 1, "world": 2}
    // 直接创建一个map
    dict = make(map[string]int)

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



------

#### References

1. 