

---

#### ipython调试器命令

使用%pdb，在代码出错时启用调试器

命令 | 功能
---|---
**a**rgs | 显示当前函数的调用参数 
**b**reak number | 在当前文件的第number设置一个断点 
**b**reak path/to/file.py:number | 在指定文件的第number行设置一个断点 
**c**ontinue | 恢复程序运行
**h/?** | 打印帮助列表
**ll** / longlist| 打印当前栈帧的代码，比list显示的更多 
**n**ext   | 执行当前行，并前进到当前级别的下一行 
**p** expression | 打印表达示，打印变量值
**q**uit 	| 退出调试器 
**s**tep 	| 单步进入函数调用
**w**here | 打印当前位置的完整栈跟踪(包括上下文参考代码)

 

---



1. 安装

   ```shell
   pip install --user ipython
   pip install --user --upgrade jupyter
   ```

2. 使用`%hist`查看历史命令

3. 在一个交互式会话中，我们可以使用`%timeit`魔法命令快速测量代码运行时间。相同的命令会在一个循环中多次执行，多次运行时长的平均值作为该命令的最终评估时长

   ```python
   %timeit [x*x for x in range(10000)]
   ```

4. `%pylab`魔法命令可以使`numpy`和`matplotlib`中的科学计算功能生效，这些功能被称为基于向量和矩阵的高效操作，交互可视化特性。它能够让我们在控制台进行交互式计算和动态绘图。

    ```python
    %pylab
    x = linspace(-10, 10, 100)
    plot(x, sin(x))
    ```

