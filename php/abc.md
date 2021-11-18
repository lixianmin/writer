



-----

#### 01 常见操作



1. `$config = include $filename;` 引用文件
2. 参数可以有默认值 `public static function init($app_name = null)`
3. 弱类型的语言，没法指定返回值类型，但是在**方法注释中加上 `@return ClassType`可以让IDE识别类型从而给出提示信息**
4. self代表是的当前类，所以在static方法中用得较多，可以通过 `new self()`创建对象
5. `static::m()` 调用的方法m()有可能被子类override；`self::m()` 则一定**调用本类中的方法**（用于父类担心方法被子类覆盖时）





---

#### 02 array



1. `array_combine($keys, , $values)` 使用keys与values组一个hash出来
2. `array_merge($array1, $array2...)`返回一个合并后的新的array
3. 



-----

#### 03 file



```php
// 判断文件是否存在
if(file_exists($filename)) 
  
// 打印日志调试代码
// 不要使用var_dump，会输出到前端页面，破坏前端处理逻辑
file_put_contents("test.log", "loaded=$loaded \n", FILE_APPEND | LOCK_EX);


```





---

#### 04 string

```php

// https://www.php.net/manual/en/language.types.string.php#language.types.string.casting
// boolean跟string互转的逻辑是：true <=> "1", false <=> ""
// null会被转化为空串：""

echo true;  // 输出1
echo false; // 输出""
echo null;  // 输出""

// 常用调试方案：
echo json_encode($a, JSON_PARTIAL_OUTPUT_ON_ERROR); 	// 编码出错时输出部分结果，而不是""。有时我们使用json_encode()测试，输出空串让人迷惑
echo var_export($a, true);  // var_export无法处理递归引用
echo print_r($a, true);			// print_r可以处理递归引用

```



1. `explode(':', $key, 3)`将$key按 ':' 进行split，3是limit
2. `is_string($name)` 判断是否是字符串
3. `"$CONFIG_PATH/$name.php"` 插值字符串，参数中只能使用变量名
4. `sprintf('%s/%s.php', get_real_path(CONF_PATH), $name)` 格式化，参数中可以加方法
5. 

