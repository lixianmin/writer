

-----
很多内置shell程序都在/usr/bin目录下，比如：
1. /usr/bin/python
2. /usr/bin/ruby
3. /usr/bin/env

-----
#### 0x01 重定向

```shell

grep da *  2>

# 把标准输出和标准错误都重定向到/dev/null
rm f a.out &> /dev/null

# >&2 把输出重定向到标准错误，并且如果之前标准错误被重定向到了某个file，则该输出也重定向到该file。这里的&可以理解为"the same as"
echo "hello" >&2
```



语法    | 含义
---     |---
<       | 重定向标准输入
>       | 重定向标准输出
2>      | 重定向标准错误输出
2>&1 | 重定向标准错误输出到标准输出 

----
#### 0x02 shell特殊变量



| 变量     | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| $0       | 当前脚本的文件名                                             |
| $n       | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是\$1，第二个参数是$2 |
| $#       | 传递给脚本或函数的参数个数                                   |
| $*       | 传递给脚本或函数的所有参数                                   |
| $@       | 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同 |
| $?       | 上个shell命令的退出状态，或函数的返回值                      |
| $$       | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |
|          |                                                              |
| ${a}     | 变量a的值，在不引起歧义的情况下可以省略大括号                |
| $(cmd)   | 命令替换，结果为shell命令cmd的输出，和\`cmd\`效果相同        |
| $((exp)) | 和\`expr exp\`效果相同，计算数据表达式exp的值，exp可以是三目运算符和逻辑表达式 |



1. [Shell特殊变量：Shell $0, $#, $*, $@, $?, $$和命令行参数](http://c.biancheng.net/cpp/view/2739.html)

---
#### 0x03 常用shell指令

```shell
# 查看linux发生版
cat /etc/*release

# cd支持glob方式
cd *07  # 进入17-01-07目录

# 时间格式化
date -d -1hour '+%Y%m%d'
date -d "${start_time} CST +1 day" "+%Y-%m-%d %H:%M:%S"

# m按兆输出，d深度为2，r逆序，n按字符串中的数字的值排序
du -md 2 | sort -rn
# 显示当前目录下所有entry的大小
du -sh *

# 每隔1秒，执行一次
echo "while true; do ls; sleep 1; done" | sh

# 打印所有shell导出的变量
export

# 列出所有内置的命令及语法
help

# 建立当前目录下file.txt文件的软链接到桌面
ln -s \`pwd\`/file.txt ~/Desktop

# 查看80端口占用进程
lsof -i:80

# 加了行号的cat
nl a.txt

# 以后面的输入为key匹配所有可能的shell命令打印出帮助
man -k ruby

# 将stdout的输出内容place到clipboard（剪贴板）中
pbcopy < file

# -s	将数据显示为一行，而不是多行
# -d:	将数据合并为一行时使用:分隔，而不是使用tab或space
# -		从stdin读取一次数据
ls | paste -sd:

# 查看网卡流量: 1s一次，一共2次
sar -n DEV 1 2

# -t 默认情况下使用ssh执行远程命令并不会分配伪终端，但有些命令需要基于屏幕输入输出进行交互，此时可以使用-t参数分配一个伪终端，直到ssh命令执行结束
ssh root@xl-slave022 -t 'docker exec $(docker ps --filter "name=sqoop" --format "{{.ID}}") bash /scripts/import_all.sh'


# tail
tail -f logfile.out		# 持续显示文件末尾新添加的数据
tail -n +2 file.in		# 跳过2行，显示文件后面的数据
tail -n 2 file.in			# 只保留最后2行的数据

# 将dir_name中的文件压缩打包，不包含.svn目录
tar cjvpf a.tar.gz dir_name --exclude .svn

# 以全屏方式监控文件的变化。watch命令需要brew安装
watch tail -r ~/Library/Logs/Unity/Editor.log

# 循环执行一条命令
while True; do echo "hello"; sleep 1; done;
```



----

#### 0x04 条件语句



| 条件        | 描述                                |
| ----------- | ----------------------------------- |
| [-a file]   | 如果file存在                        |
| [-b file]   | 如果file存在 && 是一个block特殊文件 |
| [-c file]   | 如果file存在 && 是一个char特殊文件  |
| [-d file]   | 如果file存在 && 是一个directory     |
| [-w file]   | 如果file存在 && 可write             |
| [-x file]   | 如果file存在 && 可以eXecute         |
| [-z string] | 如果"string"的长度为0               |
|             |                                     |
|             |                                     |
|             |                                     |





---
#### bc

1. echo '3+4' | bc
2. 命令行直接打bc，然后会开一个交互式的控制台，然后反复输入计算