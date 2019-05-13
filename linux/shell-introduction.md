

---
很多内置shell程序都在/usr/bin目录下，比如：
1. /usr/bin/python
2. /usr/bin/ruby
3. /usr/bin/env

---
#### 重定向

语法    | 含义
---     |---
<       | 重定向标准输入
>       | 重定向标准输出
2>      | 重定向标准错误输出

---
#### shell特殊变量

变量| 含义
--- |---
$0	| 当前脚本的文件名
$n	| 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。
$#	| 传递给脚本或函数的参数个数。
$*	| 传递给脚本或函数的所有参数。
$@	| 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同。
$?	| 上个命令的退出状态，或函数的返回值。
$$  | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。

[Shell特殊变量：Shell $0, $#, $*, $@, $?, $$和命令行参数](http://c.biancheng.net/cpp/view/2739.html)

---
#### 常用shell指令

```shell
# cd支持glob方式
cd *07  # 进入17-01-07目录

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

# 查看网卡流量: 1s一次，一共2次
sar -n DEV 1 2
# 持续显示文件末尾新添加的数据
tail -f logfile.out

# 将dir_name中的文件压缩打包，不包含.svn目录
tar cjvpf a.tar.gz dir_name --exclude .svn

# 以全屏方式监控文件的变化。watch命令需要brew安装
watch tail -r ~/Library/Logs/Unity/Editor.log

# 循环执行一条命令
while True; do echo "hello"; sleep 1; done;
```


​    
---
#### bc

1. echo '3+4' | bc
2. 命令行直接打bc，然后会开一个交互式的控制台，然后反复输入计算