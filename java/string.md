

----

#### 0x01 基本方法列表



| 方法                            | 描述                                                      |
| ------------------------------- | --------------------------------------------------------- |
| String.format()                 | 跟C#中的format类似，使用StringBuiler实现                  |
| String.join()                   | 就是python中的join()方法                                  |
| substring(startIndex, endIndex) | 注意第二个参数是endIndex，定义区间 [startIndex, endIndex) |
|                                 |                                                           |



---

#### 0x02 数值与字符串互转



```java
// float转str。
// 原生的float不是对象，所以f自身并不包含的toString()方法，只能调用类Float的toString()方法
float f = 3.14f;
String s= Float.toString(f);
// String.valueOf(int/float/double)是一系列方法，内部调用对应的类toString()方法实现
String s1 = String.valueOf(f); 

// str转float
float f2 = Float.parseFloat(s);
```

