



```go

// 计算阶乘
func factorial(n int) int {
	var fact = 1
	for i := 2; i < n+1; i++ {
		fact *= i
	}

	return fact
}

// 计算全排序
func permutation(data []string) []string {
	if len(data) == 1 {
		return data
	}

	var items = make([]string, 0, factorial(len(data)))
	for i := 0; i < len(data); i++ {
		data[0], data[i] = data[i], data[0]
		for _, s := range permutation(data[1:]) {
			items = append(items, data[0]+s)
		}
		data[0], data[i] = data[i], data[0]
	}

	return items
}
```

