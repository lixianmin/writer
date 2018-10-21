


1. 递归且忽略大小写，并且显示行号

    ```shell
    grep -rin --include=*.didi tga . 
    ```

    在当前目录递归搜索包含tga的所有didi文件，忽略大小写，另一条功能类型的命令为find . -name '*.didi' | xargs grep 'tga'；

2. 匹配多个文件类型与匹配正则表达式

    ```shell
    grep -ri --include=*.{xml,lua} "_bag\.ab” .
    ```

    - —include是可以匹配多个文件类型的
    - 如果要使用正则表达式匹配，则需要加上引号（单引号也可以），比如这里要匹配真正的"."，就需要使用"\."

3. 同时匹配多个模式

    ```shell
    adb logcat | egrep -i ‘unity|debug|crash’
    ```

4. 查找文件内容符合pattern的文件并打开

    ```shell
    # grep -l 指只打印文件名，不输出别的
    # grep --null 指用null字符代替\n来分隔文件名
    # xargs -0 指接收文件名时用null字符来分隔
    grep -rl --null "pattern" . | xargs -0 open
    ```

5. 匹配到模式后，打印context附近5个数的行

   ```shell
   cat panda.log | grep -C 5 redis
   ```
