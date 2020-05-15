



-----

#### 0x01 常见操作



1. `$config = include $filename;` 引用文件
2. 参数可以有默认值 `public static function init($app_name = null)`
3. 弱类型的语言，返回值不需要写类型，也没法写
4. self代表是的当前类，所以在static方法中用得较多，可以通过 `new self()`创建对象



---

#### 0x02 array



1. `array_combine($keys, , $values)` 使用keys与values组一个hash出来
2. `array_merge($array1, $array2...)`返回一个合并后的新的array
3. 



-----

#### 0x03 file



1. `file_exists($filename)` 文件是否存在
2. 



---

#### 0x03 string



1. `explode(':', $key, 3)`将$key按 ':' 进行split，3是limit
2. `is_string($name)` 判断是否是字符串
3. `sprintf('%s/%s.php', CONF_PATH, $name)` 格式化

