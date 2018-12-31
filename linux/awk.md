

---
#### 应用场景

1. 按列拆分文件

    ```shell
    awk 'NR!=1 {print > $6 } ' netstat.txt 
    ```

1. 指定输入输出的分隔符

    ```shell
    awk -F: '{print $1,$3,$5}' OFS='\t' /etc/passwd
    ```

1. 只使用条件过滤行数据

    ```shell
    awk '$2>0 && $6=="CLOSE_WAIT" || NR== 1' netstat.txt 
    ```

1. regex忽略大小写

    ```shell
    awk 'tolower($6) !~ /wait/ ' netstat.txt
    awk 'BEGIN{IGNORECASE=1} /wait/ ' netstat.txt
    ```

1. 数组、循环、printf

    ```shell
    awk 'NR!=1 {a[$6]++;} END { for (i in a) printf("%-8s\t%d\n", i, a[i]) } ' netstat.txt
    ```

1. 将svn新加的.ab文件扩展名改为.didi
    ​     

    ```shell
    svn st | awk '{ if ($1=="?") print(substr($2,0,length($2)-3)".didi") }’ 
    ```

1. 当svn有冲突时revert到上一个版本

    ```shell
    svn st | awk '$2 == "C" { system("svn revert "$3) } '
    ```

1. 查看ipa文件中某个目录的平均压缩率

    ```shell
    unzip -v torchlight.ipa | grep texture2d | awk '{sum+=$4} END {print sum/NR}'
    ```

1. 当前内存里占用内存最大的十大应用

    ```shell
    ps aux | awk 'NR!=1{a[$11]+=$6;}END{for (k in a) printf("%-9d\t%s\n", a[k], k); }' | sort -rnk1 | head -n10
    ```

1. 从file文件中找出长度大于80的行

    ```shell
    awk 'length>80' file
    awk 'length($0)>80' file
    ```

1. 打印文件行数

     ```shell
     awk 'END{ print NR }' file
     ```

---
#### reference

1. [AWK 简明教程](https://coolshell.cn/articles/9070.html)
2. [The GNU Awk User’s Guide](https://www.gnu.org/software/gawk/manual/gawk.html)
3. [Awk](http://www.grymoire.com/Unix/Awk.html)
4. [Awk in Action](https://www.gitbook.com/book/saubcy/awk-in-action/details)
5. [Awk Tutorial](https://www.gitbook.com/book/qiansen/awk-tutorial/details)
6. [awk学习总结](https://www.gitbook.com/book/tiramisutes/awk/details)