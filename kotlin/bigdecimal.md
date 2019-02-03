

----

#### 0x01 Abstract



| 目标 | 正确用法                      | 错误用法    | 说明                             |
| ---- | ----------------------------- | ----------- | -------------------------------- |
| 判0  | a.signum() == 0               |             |                                  |
| 判等 | a.compareTo(b)==0             | a.equals(b) | a.equals(b)要求精度(scale)也一样 |
| 除法 | a.divide(b, scale, HALF_DOWN) | a/b         | a的精度决定了结果的精度          |
| 乘除 | 先乘后除                      |             | 最后除，除不尽时减少精度丢失     |



1. 除法中，必须加上RoundingMode，否则对于除不尽的情况会抛出ArithmeticException
2. 使用BigDecimal的字符串构造函数，不要使用double参数的构造函数，否则可能会出现精度丢失



----

#### 0x09 References

1. [java中BigDecimal使用注意事项](https://blog.csdn.net/BuquTianya/article/details/58163867)
2. [Java中的BigDecimal使用注意事项](https://blog.csdn.net/lantianjialiang/article/details/43987703)
3. 